### Task
We have confidential data that needs to be transferred to a remote location, so we need to encrypt that data.We also need to decrypt data we received from a remote location in order to understand its content.


On storage server in Stratos Datacenter we have private and public keys stored at /home/*_key.asc. Use these keys to perform the following actions.


- Encrypt /home/encrypt_me.txt to /home/encrypted_me.asc.

  Must pass ``` --armor``` to specify we want ASCII output.
```
sudo gpg -e --recipient-file public_key.asc --armor --output encrypted_me.asc encrypt_me.txt 
```

- Decrypt /home/decrypt_me.asc to /home/decrypted_me.txt. (Passphrase for decryption and encryption is kodekloud).
```
sudo gpg -d --recipient-file private_key.asc --output decrypted_me.txt decrypt_me.asc
```

- The user ID you can use is kodekloud@kodekloud.com.
