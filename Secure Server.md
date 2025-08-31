# Description:

###### Author: NoobMaster

John Doe uses this secure server where plaintext is never shared. Our Forensics Analyst was able to capture this traffic and the source code for the server. Can you recover John Doe's secrets?

#crypto
# Solution:
First, let us observe the `pcap` file:
Upon Following the TCP stream we find:
```Text
With the Secure Server, sharing secrets is safer than ever!

Enter the secret, XORed by your key (in hex): >151e71ce4addf692d5bac83bb87911a20c39b71da3fa5e7ff05a2b2b0a83ba03

Double encrypted secret (in hex): e1930164280e44386b389f7e3bc02b707188ea70d9617e3ced989f15d8a10d70

XOR the above with your key again (in hex): >87ee02c312a7f1fef8f92f75f1e60ba122df321925e8132068b0871ff303960e

Secret received!
```
The prompts preceded by `>` means that was sent by us(In Wireshark it is red in color).

We then observe the `server.py` file given to us:
```python
import os
from pwn import xor

print("With the Secure Server, sharing secrets is safer than ever!")
enc = bytes.fromhex(input("Enter the secret, XORed by your key (in hex): ").strip())

key = os.urandom(32)
enc2 = xor(enc,key).hex()

print(f"Double encrypted secret (in hex): {enc2}")
dec = bytes.fromhex(input("XOR the above with your key again (in hex): ").strip())

secret = xor(dec,key)

print("Secret received!")
```

The Math goes like:
`enc` = Input converted to bytes
`key` = random 32 bit integer
`enc2` = `enc` $\oplus$ `key`
`secret` = `dec` $\oplus$ `key`
Where, $\oplus$(XOR) is defined as
$$
a\oplus b 
= 
\begin{cases}
   0 & \text{if } a = b \\
   1 & \text{if } a \neq b
\end{cases}
$$
Now, a property of $\oplus$ is that:
$$b \oplus b = 0$$
and 
$$a \oplus b = b \oplus a$$
So,
$$a \oplus b \oplus b = a$$
The Three hex codes are(in order):
1. `enc`
2. `enc2`
3. `dec`
Hence, we can see:
$$
\text{enc} \oplus \text{enc2} \oplus \text{dec}
$$
$$= 
\left(\text{enc}  
\oplus 
\left(\text{enc}  \oplus \text{key}\right)
\right)   
\oplus \text{dec}
$$
$$= 
\left( \text{key} \right)   
\oplus \text{dec}
$$
$$ = \text{flag}$$
The Python code for decryption of these can be very easily formulated as 
```Python title=solve.py
import binascii

enc = bytes.fromhex("151e71ce4addf692d5bac83bb87911a20c39b71da3fa5e7ff05a2b2b0a83ba03")
enc2 = bytes.fromhex("e1930164280e44386b389f7e3bc02b707188ea70d9617e3ced989f15d8a10d70")
dec = bytes.fromhex("87ee02c312a7f1fef8f92f75f1e60ba122df321925e8132068b0871ff303960e")

secret = bytes(a ^ b ^ c for a,b,c in zip(enc,enc2,dec))

print(secret.decode())
```

```bash
$ python ./solve.py
scriptCTF{x0r_1s_not_s3cur3!!!!}
```