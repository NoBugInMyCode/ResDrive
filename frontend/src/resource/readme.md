<p align="center">Still under developing</p>
<p align="center">
  <img src="readme_images/logo.png" alt="ResChat" width="200"/>
</p>

<p align="center">A ResilientDB blockchain based chatting system</p>
<p align="center">By Jiazhi(Kenny) Sun</p>


## 1. Project Description
### 1.1 Overview
In today's life, when we try to send a message on most chat software, the message will first be sent to the central server, 
and then forwarded to the target user by the central server. 
The disadvantage of this is that all data will be captured and stored by the central server, 
which greatly increases the risk of data leakage and leakage of private information. 
Now, we will create a decentralized chat system based on the ResilientDB blockchain. 
This decentralized chat system does not store any personal information, 
and only the sender and recipient can encrypt and decrypt the message during the transmission of the message.

### 1.2 Key Features
1. Decentralized Architecture: Our system avoids the need for a central server. All messages are transmitted through ResilientDB blockchain.
2. Security: By using the combined encryption algorithm(RSA + AES), we can ensure that information cannot be easily cracked during blockchain transmission. Only the sender and recipient of the message can use their keys to encrypt and decrypt
3. Privacy-first Approach: User data is never stored on any central server. No chat history will be stored.
4. Open-source: To ensure utmost transparency and security, our system is fully open-source, allowing community participation and review.
5. Flexibility: Users can create their own ResilientDB blockchain and use ResChat as an intranet chatting software, or users can connect to the main blockchain and use ResChat as an internet chatting software.
6. Extremely low disk space usage: Users only need to store their private key locally, and everything else is stored in the blockchain.

## 2. Overall Idea
### 2.1 Message and Page
The key of messages is a custom class called `Page`. `Page` serves as a container to store and transfer messages. 
Each `Page` has 20 messages and each message has 7 different fields. When a page is full, a new page will be created.
1. Receiver's public key: Who will receive this message, base on this we can identify who is the sender and receiver of this message.
2. Message type: `TEXT` or `FILE` to identify how to process this message.
3. Time stamp: When this message been sent.
4. Message type extension: Only in use when message type is `FILE` to store file name and extension such like `testFile.pdf`
5. Encrypted message: If message type is `TEXT`, this field will store the encrypted text string. 
   If message type is `FILE`, this field will store a key(A ResilientDB key) and the corresponding value is encrypted file string.
   In this way, it will not give a high pressure to the internet, and computing power. Users can choose to download the file or not.
6. Encrypted AES key with sender's RSA public key: Use sender's RSA public key to encrypt randomly generated AES key. 
   So, sender can decrypt AES key with his/her RSA private key.
7. Encrypted AES key with receiver's RSA public key: Use receiver's RSA public key to encrypt the AES key. Only receiver can decrypt this message.
In this approach, sender and receiver can both encrypt and decrypt certain message with their own RSA private keys without expose the keys to each other.

Both sender and receiver will obtain some shared pages. This project is using kv service of the ResilientDB, 
and the command line instructions are set `key` `value` and get `key`. 
The `key` part constructed with two fields `page name` and `page number`. 
The `page name` is constructed by sender and receiver's username(sorted in ASCII order) to ensure that both receiver and sender will have the same page name. 
Page name will never change throughout the chatting. On the other hand, `page number` starts at 1, 
and it will increase by 1 everytime the page is full. 
ResChat will load most recent two pages everytime user start up the client(current page and current page -1) In this way, 
all chat history are stored on the chain(ResilientDB) and user can load as many as previous chatting history as he/she wants.

### 2.2 File Transfer
As mentioned above, when user want to download a file. System will first read the corresponding value(encrypted file string)
of the file location. However, to avoid overload the RAM during the encryption and decryption process, a large file will be break into 
different small chunks. For example, if the file location is `123456 654321 FILE 1`, system will first get the value from this key, 
which is a checker to show this file has been uploaded completely or not. If this field is `FINISHED` means file has been uploaded completely,
then, system will check `123456 654321 FILE 1 1` which is the first file chunk next is `123456 654321 FILE 1 2` which is the second file chunk... 
Such process will keep going until a key's corresponding value is none.

Example:
Let's assume my username is 123456 and the friend I am currently chatting with has username 654321. Below image shows
how pages and files are stored in the ResilientDB in a key value pair form.

![page and file image](readme_images/page_and_file.png)

### 2.2 Encryption
This project uses RSA + AES as the encryption algorithm. The message will be first encrypted by a randomly generated 16 bytes(128 bits) AES key. 
Then, this AES key will be encrypted by 2048 bits RSA public key(AES key will be encrypted twice, one with sender's public key, another with receiver's public key).
In this approach, ResChat can achieve not only secure text messages transfer but also secure file transfer(RSA can not encrypt a string that is too long).
![encryption diagram](readme_images/encryption.png)

### 2.3 Decryption
TODO

## 3. Functions
TODO

## 4. How to run
1. Anaconda [Download Link](https://www.anaconda.com/download#downloads)
```
sudo apt-get update
cd Download
bash Anaconda3-2022.05-Linux-x86_64.sh
source ~/.bashrc
```

2. Setup Anaconda Environment
```
conda create --name YOUR_ENV_NAME python=3.8
conda activate YOUR_ENV_NAME
pip install pandas pycrypto pycryptodome numpy Django
```
3. Install ResCHat
```angular2html
cd ResChat
bazel build :pybind_kv_so
```

4. Run Service
```angular2html
TODO
```



