### AES

高级加密标准（英语：Advanced Encryption Standard，缩写：AES），在密码学中又称Rijndael加密法，是美国联邦政府采用的一种区块加密标准。这个标准用来替代原先的DES，已经被多方分析且广为全世界所使用。经过五年的甄选流程，高级加密标准由美国国家标准与技术研究院（NIST）于2001年11月26日发布于FIPS PUB 197，并在2002年5月26日成为有效的标准。2006年，高级加密标准已然成为对称密钥加密中最流行的算法之一。

AES加密也叫对称加密：A用密码对数据进行AES加密后，B用同样的密码对密文进行AES解密。


**基本加密解密文本命令**
```shell

openssl enc -aes-256-cbc -in plain.txt -out secure.txt -p
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:
salt=3A46ADF7C82AE59A
key=67280929EB69F4C49733A32E4850A3B65A50C1860158F6B9BBF2CE9F9AACBB59
iv =72EBD765FEB94E3760EFC7E1FFBB5EB6

  
openssl enc -aes-256-cbc -d -in secure.txt -out secure.txt.out -p
enter aes-256-cbc decryption password:
salt=3A46ADF7C82AE59A
key=67280929EB69F4C49733A32E4850A3B65A50C1860158F6B9BBF2CE9F9AACBB59
iv =72EBD765FEB94E3760EFC7E1FFBB5EB6

```
