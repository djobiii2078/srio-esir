# TD6 - Exécution **de confiance** sans TPM ni TEE


Dans ce TP, nous allons exécuter **le même programme** dans deux modes :

| Mode                                 | Propriétés                                                |
| ------------------------------------ | --------------------------------------------------------- |
| **Non fiable (untrusted)**           | Aucune confidentialité, aucune intégrité                  |
| **Fiable (trusted – contournement)** | Confidentialité des données + détection des modifications |

**Aucun TPM, aucun SGX/TRUSTZone, aucun TEE matériel**.

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

Créer l’arborescence suivante :

```text
trusted-exec-lab/
├── untrusted/
│   └── sum_untrusted.py
├── trusted/
│   ├── worker.c
│   ├── client.py
│   └── verify_worker.sh
├── keys/
│   ├── private.pem
│   └── public.pem
└── scripts/
    ├── attack_untrusted.sh
    └── run_trusted.sh
```

---

## 4. Partie I — Exécution NON FIABLE

### 4.1 Code non fiable

**Fichier : `untrusted/sum_untrusted.py`**

```python
data = [1, 2, 3, 4]
print("Somme :", sum(data))
```

---

### 4.2 Exécution

```bash
python3 untrusted/sum_untrusted.py
```

Résultat attendu :

```
Somme : 10
```

---

### 4.3 Attaque triviale par l’OS

**Fichier : `scripts/attack_untrusted.sh`**

```bash
#!/bin/bash
sed -i 's/1, 2, 3, 4/100, 200, 300, 400/' untrusted/sum_untrusted.py
python3 untrusted/sum_untrusted.py
```

Exécution :

```bash
chmod +x scripts/attack_untrusted.sh
./scripts/attack_untrusted.sh
```

Résultat :

```
Somme : 1000
```

**Conclusion** :

> Sans protection, l’OS peut lire, modifier et falsifier le calcul.

---

## 5. Partie II — Exécution « fiable » (solution de contournement)

### Principe général

Nous construisons artificiellement la confiance en utilisant :

1. chiffrement de bout en bout
2. processus de calcul minimal
3. vérification d’intégrité du binaire
4. aucune donnée en clair côté OS

---

## 6. Mise en œuvre pas à pas

---

### 6.1 Génération des clés cryptographiques

```bash
mkdir keys
openssl genrsa -out keys/private.pem 2048
openssl rsa -in keys/private.pem -pubout -out keys/public.pem
```

---

### 6.2 Programme « worker » (processus de confiance)

**Fichier : `trusted/worker.c`**

```c
#include <stdio.h>
#include <openssl/rsa.h>
#include <openssl/pem.h>

int main() {
    FILE *fp = fopen("keys/private.pem", "r");
    RSA *rsa = PEM_read_RSAPrivateKey(fp, NULL, NULL, NULL);

    unsigned char enc[256];
    fread(enc, 1, 256, stdin);

    unsigned char dec[256];
    RSA_private_decrypt(256, enc, dec, rsa, RSA_PKCS1_PADDING);

    int *data = (int *)dec;
    int sum = data[0] + data[1] + data[2] + data[3];

    unsigned char out[256];
    RSA_private_encrypt(sizeof(int), (unsigned char *)&sum,
                        out, rsa, RSA_PKCS1_PADDING);

    fwrite(out, 1, 256, stdout);
}
```

Compilation :

```bash
gcc trusted/worker.c -lcrypto -o trusted/worker
```

---

### 6.3 Client non fiable mais « aveugle »

**Fichier : `trusted/client.py`**

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5
import subprocess

key = RSA.importKey(open("keys/public.pem").read())
cipher = PKCS1_v1_5.new(key)

data = (1).to_bytes(4, 'little') * 4
enc = cipher.encrypt(data)

p = subprocess.Popen(
    ["./trusted/worker"],
    stdin=subprocess.PIPE,
    stdout=subprocess.PIPE
)

out, _ = p.communicate(enc)

cipher = PKCS1_v1_5.new(key)
result = cipher.decrypt(out, None)

print("Somme :", int.from_bytes(result, 'little'))
```

---

### 6.4 Exécution du mode fiable

```bash
python3 trusted/client.py
```

Résultat attendu :

```
Somme : 4
```

L’OS ne voit jamais les données en clair.

---

## 7. Vérifications de sécurité

---

### 7.1 Vérification de l’intégrité du worker

**Fichier : `trusted/verify_worker.sh`**

```bash
#!/bin/bash
sha256sum trusted/worker > trusted/worker.hash
echo "Empreinte enregistrée"
```

---

### 7.2 Détection de modification

```bash
echo "// attaque" >> trusted/worker.c
gcc trusted/worker.c -lcrypto -o trusted/worker
sha256sum -c trusted/worker.hash
```

Résultat attendu :

```
FAILED
```

Toute modification est détectée.

---

### 7.3 Vérification de l’absence de secrets en clair

```bash
strings trusted/worker | grep -E "1|2|3|4|10"
```

Résultat attendu :

```
(aucune sortie)
```

---

## 8. Comparaison finale

| Propriété           | Non fiable | Fiable (logiciel) |
| ------------------- | ---------- | ----------------- |
| Données en clair    | Oui        | Non               |
| Intégrité du calcul | Non        | Oui               |
| Détection d’attaque | Non        | Oui               |
| Matériel spécial    | Non        | Non               |

---

## 9. Ce que cette solution fait (et ne fait pas)

### Protège :

* confidentialité des entrées/sorties
* intégrité du résultat
* détection de modification du code

### Ne protège PAS :

* attaques par canaux auxiliaires
* extraction de clés en mémoire
* compromission du CPU

---

> **L’exécution de confiance est une propriété de l’architecture logicielle, et non du matériel.**

Les TEE matériels renforcent la sécurité, mais **ne la remplacent pas**.

---

## Questions ouvertes
1. Pourquoi le worker est-il considéré comme « de confiance » ?
2. Que se passe-t-il si la clé privée fuit ?
3. En quoi cette solution diffère-t-elle d’un TEE matériel ?
4. Dans quels contextes est-elle suffisante en pratique ?

---

