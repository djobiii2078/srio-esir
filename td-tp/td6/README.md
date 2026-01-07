# TD6 - Exécution **de confiance** sans TPM ni TEE


Dans ce TP, nous allons exécuter **le même programme** dans deux modes :

| Mode                                 | Propriétés                                                |
| ------------------------------------ | --------------------------------------------------------- |
| **Non fiable (untrusted)**           | Aucune confidentialité, aucune intégrité                  |
| **Fiable (trusted – contournement)** | Confidentialité des données + détection des modifications |

👉 **Aucun TPM, aucun SGX/TRUSTZone, aucun TEE matériel**.

Toute la confiance repose sur :

* la cryptographie
* la séparation des rôles
* la vérification explicite

---

## 1. Modèle de menace (très important)

Nous supposons que :

* le système d’exploitation est malveillant
* l’attaquant peut être root
* la mémoire des processus est lisible
* les binaires peuvent être modifiés

Nous supposons que :

* le CPU n’est pas compromis
* la cryptographie est correcte

**Objectifs de sécurité** :

* protéger la confidentialité des données
* détecter toute modification du code

---

## 2. Calcul exemple

Nous utilisons volontairement un calcul simple :

```
Somme([a, b, c, d]) → entier
```
---

## 3. Organisation du projet

```
trusted-exec-lab/
├── untrusted/
│   └── sum_untrusted.py
├── trusted/
│   ├── worker.py
│   ├── client.py
│   └── verify_worker.sh
├── keys/
└── scripts/
```

---

## 4. Partie I — Exécution NON FIABLE

### 4.1 Code non fiable

```python
data = [1, 2, 3, 4]
print("Somme :", sum(data))
```

### 4.2 Exécution

```bash
python3 untrusted/sum_untrusted.py
```

Résultat attendu : `Somme : 10`

### 4.3 Attaque triviale

```bash
#!/bin/bash
sed -i 's/1, 2, 3, 4/100, 200, 300, 400/' untrusted/sum_untrusted.py
python3 untrusted/sum_untrusted.py
```

Résultat : `Somme : 1000`

---

## 5. Partie II — Exécution « fiable » avec Ed25519 via ECC

### 5.1 Installation

```bash
pip3 install --user pycryptodomex
```

### 5.2 Génération des clés (`scripts/gen_keys.py`)

```python
from Cryptodome.PublicKey import ECC

key = ECC.generate(curve="Ed25519")
with open("keys/private.pem", "wt") as f:
    f.write(key.export_key(format="PEM"))
with open("keys/public.pem", "wt") as f:
    f.write(key.public_key().export_key(format="PEM"))
```

### 5.3 Worker (`trusted/worker.py`)

```python
import sys
from Cryptodome.PublicKey import ECC
from Cryptodome.Signature import eddsa

private_key = ECC.import_key(open("keys/private.pem").read())

data = [1,2,3,4]
total = sum(data)
total_bytes = total.to_bytes(4,'little')

signer = eddsa.new(private_key, 'rfc8032')
signature = signer.sign(total_bytes)

sys.stdout.buffer.write(total_bytes + signature)
```

### 5.4 Client (`trusted/client.py`)

```python
import subprocess
from Cryptodome.PublicKey import ECC
from Cryptodome.Signature import eddsa

public_key = ECC.import_key(open("keys/public.pem").read())

p = subprocess.Popen(["python3", "trusted/worker.py"], stdout=subprocess.PIPE)
out = p.stdout.read()

if len(out) != 4+64:
    raise ValueError(f"Sortie invalide, attendue 68 octets, reçue {len(out)}")

result_bytes = out[:4]
signature = out[4:]

verifier = eddsa.new(public_key, 'rfc8032')
verifier.verify(result_bytes, signature)

print("Somme :", int.from_bytes(result_bytes,'little'))
print("Signature vérifiée ✓")
```

### 5.5 Exécution

```bash
python3 trusted/client.py
```

Résultat attendu :

```
Somme : 10
Signature vérifiée ✓
```

---

## 6. Vérifications de sécurité

### 6.1 Intégrité du worker

```bash
sha256sum trusted/worker.py > trusted/worker.hash
echo "Empreinte enregistrée"
```

### 6.2 Détection de modification

```bash
echo "# attaque" >> trusted/worker.py
sha256sum -c trusted/worker.hash
```

Résultat : `FAILED`

### 6.3 Confidentialité

L’OS ne voit jamais la clé privée ni la sortie non signée.

---

## 7. Comparaison finale

| Propriété           | Non fiable | Fiable (Ed25519 ECC) |
| ------------------- | ---------- | -------------------- |
| Données en clair    | Oui        | Non                  |
| Intégrité du calcul | Non        | Oui                  |
| Détection d’attaque | Non        | Oui                  |
| Matériel spécial    | Non        | Non                  |

---

## 8. Ce que cette solution fait / ne fait pas

**Protège :**

* intégrité du résultat
* détection de modification
* confidentialité des entrées/sorties

**Ne protège pas :**

* attaques par canaux auxiliaires
* extraction de clés en mémoire
* compromission du processus Python ou du système hôte

---


> L’exécution de confiance peut être construite en logiciel avec des signatures modernes. Les TEE matériels renforcent la sécurité mais ne sont pas nécessaires pour démontrer les concepts.

---

## 9. Questions ouvertes

1. Pourquoi le worker est-il « de confiance » ici ?
2. Que se passe-t-il si la clé privée fuit ?
3. Quelle différence avec un TEE matériel comme SGX ou TPM ?
4. Dans quels contextes cette approche logicielle suffit-elle ?

---
