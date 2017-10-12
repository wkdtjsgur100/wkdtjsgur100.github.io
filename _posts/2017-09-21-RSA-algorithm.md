---
layout: post
section-type: post
title: RSA 알고리즘 개념 및 구현(python)
category: encryption
tags: [ 'rsa' ]
---

# RSA 알고리즘이란?

RSA 알고리즘은 현대 컴퓨터 시스템 및 다른 전자기기에서 데이터를 암호화/복호화 할 때 사용됩니다.
그리고 RSA 알고리즘은 암호화/복호화를 목적으로 두개의 다른 key를 사용하는 비대칭(asymmetric) 암호학 알고리즘입니다. 관련된 key중 공개된 것 하나는 public key 암호화입니다.

## 오일러 피 함수(Euler's totient function)

RSA를 이해하기 위해서는 먼저 오일러의 피 함수(totient function)에 대해서 알아야합니다. 피 함수는 **1부터 n까지의 양의 정수 중에 n과 서로소인 것의 개수를 나타내는 함수입니다.** 예를 들어, `n=9`일 때 피 함수 `φ(9) = 6`인데, 이는 1~9까지 자연수 k중에서 `gcd(n,k)=1`인(즉, 최대공약수가 1인) 자연수 `k={1,2,4,5,7,8}` 이므로 `φ(9)=6` 입니다.

## RSA 알고리즘의 구현

RSA 시스템은 매우 큰 소수 p와 q를 선택하고, `n = pq` 그리고 `k = φ(n)`을 구한 후, `e*d = 1+k` 에서 e와 d를 구합니다. n과 e(암호화 키)는 public으로 릴리즈되고, d는 private로 릴리즈됩니다. `0<m<n`인 정수 m으로 표현되는 메세지는, 계산에 의해서 암호화됩니다.

``` text
c = m^e mod n.
```

암호화된 c는 계산을 통해 복호화됩니다:
``` text
t = c^d mod n
```

복호화된 t는 오리지날 메세지 m과 같아야만 합니다.  
즉, RSA 알고리즘은 아래과 같은 7단계를 거칩니다.

- 단계 1. 두개의 소수 p, q를 정합니다.(여기서 소수 p,q가 너무 작을 시 제대로 암/복호화가 이루어지지 않습니다.)
- 단계 2. 두 키의 계수인 n=pq를 구합니다.  
- 단계 3. 오일러의 피 함수(Euler's totient function) 값, 즉 totient =(p-1)(q-1)를 계산합니다.  
- 단계 4. 1<e<totient, gcd(e, totient)가 1인 e를 구합니다. e는 바로 public key가 됩니다.  
- 단계 5. d*e를 totient로 나누었을 때의 나머지가 1이 되는 d를 구합니다. d는 private key가 됩니다.  
- 단계 6. 암호화 된 메세지 `c = m^e % n`을 구합니다. m은 메세지 각각의 단일 문자를 아스키 코드로 변환한 값을 말합니다. 예를 들어, "ABCD"라는 문자를 암호화 한다고 하면(e=2, n=7이라고 가정하겠습니다.), 각각의 아스키 코드 값 `65 66 67 68`은 `65^2%7 66^2%7 67^2%7 68^2%7`로 암호화 되어 최종 암호화된 메세지는 `4 2 2 4`가 됩니다.
- 단계 7. 각각의 메세지 값을 복호화한 값 `m = (c*d)%n`을 이용해 복호화 된 메세지를 구합니다.

위의 알고리즘을 구현한 소스는 다음과 같습니다.(python)

``` python
def gcd(a, b):
    while b!=0:
        a, b = b, a%b
    return a

def decrypt(pk, ciphertext):
    #Unpack the key into its components
    key, n = pk
    #Generate the plaintext based on the ciphertext and key using a^b mod m
    plain = [chr((char ** key) % n) for char in ciphertext]
    #Return the array of bytes as a string
    return ''.join(plain)

def encrypt(pk, plaintext):
    #Unpack the key into it's components
    key, n = pk
    #Convert each letter in the plaintext to numbers based on the character using a^b mod m
    cipher = [(ord(char) ** key) % n for char in plaintext]
    #Return the array of bytes
    return cipher

def get_private_key(e, tot):
    k=1
    while (e*k)%tot != 1 or k == e:
        k+=1
    return k

def get_public_key(tot):
    e=2
    while e<totient and gcd(e, totient)!=1:
        e += 1
    return e

# Input message to be encrypted
m = input("Enter the text to be encrypted:")

# Step 1. Choose two prime numbers
p = 13
q = 23

print("Two prime numbers(p and q) are:", str(p), "and", str(q))

# Step 2. Compute n = pq which is the modulus of both the keys
n = p*q
print("n(p*q)=", str(p), "*", str(q), "=", str(n))

# Step 3. Calculate totient
totient = (p-1)*(q-1)
print("(p-1)*(q-1)=", str(totient))

# Step 4. Find public key e
e = get_public_key(totient)
print("Public key(n, e):("+str(n)+","+str(e)+")")

# Step 5. Find private key d
d = get_private_key(e, totient)
print("Private key(n, d):("+str(n)+","+str(d)+")")

# Step 6.Encrypt message
encrypted_msg = encrypt((e,n), m)
print('Encrypted Message:', ''.join(map(lambda x: str(x), encrypted_msg)))

# Step 7.Decrypt message
print('Decrypted Message:', decrypt((d,n),encrypted_msg))
```

위의 소스를 실행한 결과는 다음과 같습니다.

``` shell
jang@jang:~$ python3 rsa.py                                      
Enter the text to be encrypted:to be or not to be that is a question
Two prime numbers(p and q) are: 13 and 23
n(p*q)= 13 * 23 = 299
(p-1)*(q-1)= 264
Public key(n, e):(299,5)
Private key(n, d):(299,53)
Encrypted Message: 116115423223854542101111654116115423223854116156158116542746541585419878238461162711210
Decrypted Message: to be or not to be that is a question
```

p와 q를 랜덤한 소수로 초기화 시키거나, e를 2가 아닌 랜덤한 숫자부터 시작해 더 다양한 암호화/복호화를 수행할 수 있습니다.
