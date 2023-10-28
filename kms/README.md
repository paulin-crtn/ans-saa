# 🔑 Key Management Service

Create, store and manage symmetric and asymmetric keys in order to achieve cryptographic operations (encrypt and decrypt).

> [!IMPORTANT]
> **Keys never leave KMS** - Provides FIPS 140-2 (L2)

- KMS Keys are **logical** (ID, date, policy, desc & state) and backed by **physical** key material
- Keys are generated or imported in KMS and stored in an encrypted form (never in plain text)
- AWS owned vs. Customer owned
- Support rotation
- Backing key (can still decrypt data with previous keys)
- Can be used for up to **4KB** of data

> [!NOTE]
> Keys are isolated to a region (but can be replicated to multi-region)

## 🔏 Data Encryption Keys (DEK)

- Works on data > 4KB
- KMS generate a Plaintext key (used to encrypt the data and then discarded) and a Ciphertext key (used to decrypt the data)
- Data is stored with encrypted key outside KMS
- Data and encrypted key must be sent to KMS for decryption (KMS will decrypt the encrypted key)

## 👮‍♂️ Security & Policies

Unlike other AWS services, KMS has to explicitly be told that keys trust the AWS account that there contain within.
