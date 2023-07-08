---
title: "Secure Your Files - Encrypt and Decrypt Your Files"
datePublished: Fri Jul 07 2023 00:38:48 GMT+0000 (Coordinated Universal Time)
cuid: cljrumcjt000009juav1bdqtx
slug: secure-your-files-encrypt-and-decrypt-your-files
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1688690244604/5dd69de5-af5f-4e31-9b2f-8b66aa347b0c.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1688690322038/a35ef357-e122-4e2a-a185-0d2cc128b9d1.jpeg
tags: python, script, cryptography, hashing, hacking-tools

---

## Introduction:

Python script developed that provides an intuitive solution for encrypting and decrypting files using [AES encryption](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). With its user-friendly graphical user interface (GUI) built with tkinter, Secure Your Files allows users to easily select files and perform encryption or decryption operations with just a few clicks.

Features:

1. User-Friendly GUI: Secure Your Files boasts a sleek and intuitive GUI design, inspired by the Discord theme. Users can quickly navigate through the interface, select files, and perform encryption or decryption operations effortlessly.
    
2. File Encryption: Secure Your Files enables users to encrypt single files or multiple files simultaneously. By selecting the desired files, users can apply AES encryption to protect their sensitive data from unauthorized access.
    
3. File Decryption: Secure Your Files allows users to decrypt encrypted files back to their original format. This functionality ensures convenient access and utilization of encrypted files whenever needed.
    
4. Random Encryption Key: Secure Your Files generates a random encryption key for each session, enhancing the security of the encrypted files. The random key generation ensures the uniqueness and unpredictability of the encryption keys.
    
5. AES Encryption with CBC Mode: Secure Your Files utilizes the AES (Advanced Encryption Standard) encryption algorithm in Cipher Block Chaining (CBC) mode. This combination ensures robust encryption and adds an extra layer of security to the files.
    
6. [PKCS7 Padding](https://en.wikipedia.org/wiki/Padding_(cryptography)): Secure Your Files implements PKCS7 padding, a widely used padding scheme, to ensure compatibility and proper alignment of data blocks during the encryption and decryption processes.
    

## How to Use Secure Your Files:

1. Clone the Repository: Clone or download the Secure Your Files repository from the [**GitHub repository**](https://github.com/mdSHash/Secure-Your-Files/tree/Master).
    
2. Install Dependencies: Ensure that you have Python 3.x installed on your machine. Install the necessary dependencies, including the [`Crypto`](https://pypi.org/project/pycrypto/) library, by running the command `pip install` [`pycryptodome`](https://pypi.org/project/pycryptodome/).
    
3. Run the Script: Launch Secure Your Files by running the Python script `Secure_Your_Files.py`.
    
4. Utilize the GUI:
    
    * Click the "Encrypt File" button to select one or more files for encryption.
        
    * Click the "Decrypt File" button to select encrypted files for decryption.
        
    * Click the "Show Key" button to view the encryption key used in the current session.
        

## Sample Run Scenario:

1. Open the Secure Your Files application by running the `Secure_Your_Files.py` script.
    
2. The GUI window will appear with the title "Secure Your Files" and buttons for file encryption, file decryption, and viewing the encryption key.
    
3. Click the "Encrypt File" button to initiate the file encryption process.
    
4. A file selection dialog will open. Choose the file(s) you want to encrypt and click "Open".
    
5. Secure Your Files will encrypt the selected file(s) using AES encryption and display a message box indicating that the encryption process is complete.
    
6. To decrypt a file, click the "Decrypt File" button.
    
7. Select the encrypted file(s) you want to decrypt using the file selection dialog and click "Open".
    
8. Secure Your Files will decrypt the selected file(s) back to their original format and display a message box indicating that the decryption process is complete.
    
9. If you want to view the encryption key used in the current session, click the "Show Key" button. A message box will display the encryption key in hexadecimal format.
    

## Conclusion:

Secure Your Files, developed by Script Maker, provides a convenient and secure solution for file encryption and decryption using AES encryption. With its user-friendly GUI, users can easily protect their sensitive data and maintain control over their files. Give Secure Your Files a try and experience the ease and security it offers for encrypting and decrypting files.