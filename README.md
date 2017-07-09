# FilesForETH
A contract and program for selling files through Ethereum. The amount of data transferred through the contract is low and independent of the number/size of the files. No cryptography is done by the contract when there is no disagreement

## Protocol

### 1. Initialization

Let **F** be an array of **n** encrypted files (or database entries, etc.)
  1. Each **F[n]** is individually encrypted using a different key, producing **Fe[n]**. **D[n]** is that key appended to some verification data (to verify decryption later).
  2. Each **D[n]** is individually encrypted using a different key, **K1[n]**, producing **E1[n]**.
  3. Each **K1[n]** is individually encrypted using the same key, **Kb**. This produces **E2[n]**.
  4. Each **E1[n]** is individually signed by the seller, producing **S1[n]**.
  5. The seller sends **Fe** and the arrays **E1**, **S1**, and **E2** to the buyer.
  6. The buyer signs each **E2[n]** individually, producing **S2[n]**. The buyer sends **S2** to the seller.

### 2. Verification

  1. The buyer picks **m** (**m << n**) sample files, and sends their ID to the seller.
  2. The seller sends the **K1**s corresponding to those files to the buyer.
  3. The buyer decrypts the corresponding **E1**s and then **D**s, to access those files. The buyer continues only when satisfied that **E1** is in fact useful and the files are correct.

### 3. Payment

  1. The buyer sends Ether to the contract.
  2. If the payment is enough, the seller must send **Kb** to the contract (within 5 minutes), which then forwards it to the buyer.
  3. If the buyer is satisfied with the **Kb** he received and makes no accusation within 5 additional minutes, the transaction is completed and the seller receives the Ether.

### 4. Accusation (in case of disagreement only)

  1. The buyer may send an accusation (claiming an **E1[k]** is undecryptable; remember that the "Verifcation" step convinced him that **E1** was what he wanted) to the contract, consisting of **E1[k]**, and **S1[k]**.
  2. The contract checks that the signature is valid (that **E1[k]** isn't just some random number produced by the seller), and informs the seller of the accusation. A wrong accusation results in the transaction being completed immediately.
  3. The seller has 5 minutes to respond with a counter-accusation. Failing to respond on time or providing an invalid counter-acusation causes the buyer to be refunded. A counter-accusation consists of **E2[k]** and **S2[k]** (as proof that the buyer had actually received that item). The contract then checks if **Kb** decrypts **E2[k]** into a valid **K1[k]** (one that can decrypt the **E1[k]** the buyer was claiming to be undecryptable). The check that **K1[k]** decrypts **E1[k]** is done by veryfying that the decrypted data contains the verification data, as described in paragraph 1. If the contract can successfully do this, the accusation is disproved and the transaction is completed.

### Note

Note that the encrypted files **Fe** can be transferred directly to the buyer before the transaction starts, or even hosted publicly. **D** can remain the same between transactions with other people, since although contract contents (**Kb**) are public, **Kb** does not directly decrypt the files. Therefore, only **Kb** (and **E2**, as a consequence), must be changed.
