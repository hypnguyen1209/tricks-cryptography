# tricks-cryptography

Cre: https://lilthawg29.wordpress.com/2021/10/17/tricks-nhap-mon-mat-mat-ma-hoc

### I. Mã cổ điển

#### 1. Mã dịch vòng(Shift cipher)

https://www.dcode.fr/caesar-cipher

#### 2. Mã Affine

+ nếu cho sẵn khoá và bản rõ rồi bắt mã hoá hoặc cho sẵn khoá và bản mã rồi bắt giải mã thì : https://www.dcode.fr/affine-cipher

+ nếu bài cho 2 cặp như ở dưới thì giải tay là nhanh nhất. Sau khi tìm được khoá dùng tool ở trên.

![](https://lilthawg29.files.wordpress.com/2021/08/image-46.png?w=620&zoom=2)

#### 3. Mã Vigenere VÀ mã khoá chạy

https://www.dcode.fr/vigenere-cipher

#### 4. Mã Hill

+ Mã hoá

```python
import string
table = string.ascii_lowercase
R = IntegerModRing(26)
M = MatrixSpace(R,2,2)
  
Key = M([[11,8],[3,7]])
message = "thang"
  
message = message.lower() 
if len(message)%2 != 0:
    message+='z'
      
def encrypt(message):
    ciphertext = ''
    for i in range(0,len(message),2):
        M1 = MatrixSpace(R,1,2)
        matrix_tmp = M1([[table.index(message[i]),table.index(message[i+1])]])
        res_matrix_tmp = matrix_tmp*Key
        ciphertext += table[int(res_matrix_tmp[0][0])] + table[int(res_matrix_tmp[0][1])]
    return ciphertext
  
ciphertext = encrypt(message)
print(f'ciphertext : {ciphertext}')
```

+ Giải mã

```python
import string
table = string.ascii_lowercase
R = IntegerModRing(26)
M = MatrixSpace(R,2,2)
  
Key = M([[11,8],[3,7]])
ciphertext = 'wtnnlp'
ciphertext = ciphertext.lower() 
def decrypt(ciphertext):
    plaintext = ''
    for i in range(0,len(ciphertext),2):
        M1 = MatrixSpace(R,1,2)
        matrix_tmp = M1([[table.index(ciphertext[i]),table.index(ciphertext[i+1])]])
        res_matrix_tmp = matrix_tmp*Key.inverse()
        plaintext += table[int(res_matrix_tmp[0][0])] + table[int(res_matrix_tmp[0][1])]
    return plaintext   
  
plaintext = decrypt(ciphertext)
print(f'plaintext : {plaintext}')
```

#### 5. Mã hoán vị

https://www.dcode.fr/columnar-transposition-cipher

(mode Write by rows, read by rows)

#### 6. xtime

```python
def xtime(a):
    a = int(a,16)
    if (a & 0x80):
        return "{:02x}".format((((a << 1) ^ 0x1B) & 0xFF))
    else:
        return "{:02x}".format((a << 1))
  
print(xtime('1d'))
```

![](https://lilthawg29.files.wordpress.com/2021/09/image-41.png?w=620&zoom=2)

```python
def convert_hex_to_coefficients(a):
    a = str(a)
    a = bin(int(a,16))[2:]
    return list(a)[::-1]
  
R.<x> = PolynomialRing(GF(2))
F.<t> = R.quotient(x^8 + x^4 + x^3 + x + 1)
  
def multiplicate_2_polynomias(a,b):
    coefficients_a = convert_hex_to_coefficients(a)
    coefficients_b = convert_hex_to_coefficients(b)
    a = F(coefficients_a)
    b = F(coefficients_b)
    res = a*b
    res = ''.join(str(x) for x in res.list())[::-1]
    return "{:02x}".format(int(res,2))
  
a = "57"
b = "13"
res = multiplicate_2_polynomias(a,b)
print(f'{a}*{b} = {res}(16) = {bin(int(res,16))[2:].zfill(8)}(2)')

```

#### 7. Tìm phần tử x00 trong MixColumn

```python
def convert_hex_to_coefficients(a):
    a = str(a)
    a = bin(int(a,16))[2:]
    return list(a)[::-1]
  
R.<x> = PolynomialRing(GF(2))
F.<t> = R.quotient(x^8 + x^4 + x^3 + x + 1)
  
def multiplicate_2_polynomias(a,b):
    coefficients_a = convert_hex_to_coefficients(a)
    coefficients_b = convert_hex_to_coefficients(b)
    a = F(coefficients_a)
    b = F(coefficients_b)
    res = a*b
    res = ''.join(str(x) for x in res.list())[::-1]
    return "{:02x}".format(int(res,2))
  
res1 = multiplicate_2_polynomias('02','63')
res2 = multiplicate_2_polynomias('03','2f')
res3 = multiplicate_2_polynomias('01','af')
res4 = multiplicate_2_polynomias('01','a2')
res = int(res1,16)^^int(res2,16)^^int(res3,16)^^int(res4,16)
 
print(hex(res)[2:].zfill(2))
```

#### 8. Tìm bậc các phần tử trong nhóm Zn

```python
def list_element_Zn(n):
    return [i for i in range(1,n) if gcd(i,n)==1]
def order_of_element_a_in_Zn(a,n):
    phi_n = euler_phi(n)
    for i in range(1,phi_n+1):
        if phi_n%i == 0:
            if pow(a,i,n) == 1:
                return i
if __name__ == '__main__':
    n = 20
    print(f"trong Z{n}")
    for i in list_element_Zn(n):
        print(f'{i} có bậc là {order_of_element_a_in_Zn(i,n)}')
```

#### 9. Tìm phần tử sinh trong nhóm Zn

```python
def list_generators_of_Zn(n): 
    Zn = [i for i in range(1,n) if gcd(i,n) == 1]
    gens = []
    for a in Zn:
        g = []
        for i in range(euler_phi(n)):
            g.append(pow(a,i,n))
        if sorted(list(set(g))) == Zn:
            gens.append(a)
    if not gens:
        return f"Z{n} không có phần tử sinh"
    return f"tập các phần tử sinh của Z{n} là {gens}"

n = 37
list_generators_of_Zn(n)
```

#### 10. Phần dư trung hoa

```python
crt([10,19,20], [11,21,26])
```

#### 11. legendre và jacobi

```python
kronecker(7411,9283)
```

#### 12. Tìm căn bậc 2 modulo n

```python
def sqrt_mod(c,n):
    if kronecker(c,n) != -1:
        if is_prime(n):
            res = mod(c,n).sqrt(extend=False)
            return res,-res%n
        else:
            p,q = factor(n)
            p = p[0]
            q = q[0]
             
            assert p%4==3 and q%4==3
            d,a,b = xgcd(p,q)
            assert a*p+b*q == 1
            r = int(pow(c,(p+1)//4,p))
            s = int(pow(c,(q+1)//4,q))
            x = (a*p*s+b*q*r) %n
            y = (a*p*s-b*q*r) %n
            return x%n,-x%n,y%n,-y%n
    return f'{c} không có căn bậc 2 mod {n}'
sqrt_mod(4,21)
```

#### 13. Logarit rời rạc

```python
n = 19
a = mod(2,n)
b = mod(7,n)
#solve a^x = b => x=loga(b) 
discrete_log(b,a)
```

#### 14. RSA

+ Mã hoá

```python
n = 37*41
e = 211
m = 47
c = pow(m,e,n)
c
```

+ Giải mã

```python
n = 37*41
e = 211
c = 602
phi = euler_phi(n)
d = pow(e,-1,phi)
m = pow(c,d,n)
m
```

#### 15. Hệ mật Rabin

+ Mã hoá

```python
n = 19*23
m = 329
c = pow(m,2,n)
c
```

+ Giải mã

```python
def sqrt_mod(c,n):
    if kronecker(c,n) != -1:
        if is_prime(n):
            res = mod(c,n).sqrt(extend=False)
            return res,-res%n
        else:
            p,q = factor(n)
            p = p[0]
            q = q[0]
             
            assert p%4==3 and q%4==3
            d,a,b = xgcd(p,q)
            assert a*p+b*q == 1
            r = int(pow(c,(p+1)//4,p))
            s = int(pow(c,(q+1)//4,q))
            x = (a*p*s+b*q*r) %n
            y = (a*p*s-b*q*r) %n
            return x%n,-x%n,y%n,-y%n
    return f'{c} không có căn bậc 2 mod {n}'
sqrt_mod(302,437)
```

#### 16. ElGamal

+ Khoá công khai, khoá bí mật & Mã hoá

```python
alpha = 39
p = 211
a = 113 #khoá bí mật
beta = pow(alpha,a,p)
ke = (p,alpha,beta) #khoá công khai
print(ke)
def encrypt(x,ke):
    k = 23
    p = ke[0]
    alpha = ke[1]
    beta = ke[2]
    y1 = pow(alpha,k,p)
    y2 = x*pow(beta,k,p)%p
    return (y1,y2)
encrypt(34,ke)
```

+ Giải mã

```python
def decrypt(y,a,p):
    y1 = y[0]
    y2 = y[1]
    x = y2*pow(y1,-a,p)%p
    return x
y = (145, 140)
a = 113
p = 211
decrypt(y,a,p)
```

#### 17. Hệ mật Merkle – Hellman

+ Khoá công khai, khoá bí mật & Mã hoá

```python
def gen_key_Merkle_Hellman(n,M,W,daysieutang,hoanvi):
    arr = ['']*n
    for i in range(n):
        arr[i] = daysieutang[hoanvi[i] - 1]
    for i in range(n):
        arr[i] = arr[i]*W%M
    ke = arr
    kd = hoanvi,M,W,daysieutang
    return ke,kd
  
def encrypt_Merkle_Hellman(ke,m):
    c = 0
    for i in range(len(m)):
        if m[i] == "1":
            c += ke[i]
    return c
   
n = 6
M = 737
W = 635
daysieutang = [12,17,33,74,157,316]
hoanvi = [3,6,1,2,5,4]
ke,kd = gen_key_Merkle_Hellman(n,M,W,daysieutang,hoanvi)
print(f'Khoá công khai : {ke}')
print(f'Khoá bí mật : {kd}')
m = "101101"
c = encrypt_Merkle_Hellman(ke,m)
print(f'bản mã : {c}(10)')
```

+ Giải mã

```python
n = 6
M = 737
W = 635
daysieutang = [12,17,33,74,157,316]
hoanvi = [3,6,1,2,5,4]
ke = [319, 196, 250, 477, 200, 559]
kd = ([3, 6, 1, 2, 5, 4], 737, 635, [12, 17, 33, 74, 157, 316])
c = 1605
def decrypt_Merkle_Hellman(kd,c):
    hoanvi, M, W, daysieutang = kd
    d = c*pow(W,-1,M)%M
    #giải bài toán xếp balo
    S = d
    arr = []
    for i in range(len(daysieutang)):
        if S >= daysieutang[i]:
            arr.append('1')
            S = S-daysieutang[i]
        else:
            arr.append('0')
    a = ['']*len(arr)
    for i in range(len(arr)):
        a[i] = arr[hoanvi[i]-1]
    return ''.join(a)
m = decrypt_Merkle_Hellman(kd,c)
print(f'bản rõ : {m}(2)')
```

#### 18. Elliptic Curve

+ Liệt kê các điểm trên đường cong E

```python
E = EllipticCurve(GF(11),[1,6])
print(E)
print(f'có {len(E.points())} điểm trên đường cong E')
E.points()
```

+ Tính khoá công khai

```python
E = EllipticCurve(GF(11),[1,6])
G = E.point([2,7])
nB = 7
PB = nB*G #khoá công khai của B
PB
```

+ Mã hoá

```python
E = EllipticCurve(GF(11),[1,6])
G = E.point([2,7])
PB = E.point([7,2])
PM = E.point([10,9])
def encrypt(PB,PM):
    k = 3
    P1 = k*G
    P2 = PM+k*PB
    Pc = (P1,P2)
    return Pc
encrypt(PB,PM)
```

+ Giải mã

```python
E = EllipticCurve(GF(11),[1,6])
G = E.point([2,7])
P1 = E.point([8,3])
P2 = E.point([10,2])
nB = 7
def decrypt(P1,P2):
    return P2-nB*P1
decrypt(P1,P2)
```

#### 19. Xác thực chữ ký số ElGamal

```python
p = 467
alpha = 2
a = 127
k = 213
beta = int(pow(alpha,a,p))
def sign(x):
    Y = int(pow(alpha,k,p))
    S = (x-a*Y)*int(pow(k,-1,p-1))%(p-1)
    return Y,S
def verify(x,Y,S):
    return pow(beta,Y,p)*pow(Y,S,p) == pow(alpha,x,p)

x = 100
Y,S = sign(x)
print(f'chữ kí của x là : {Y,S}')
verify(x,Y,S)
```

#### 20. Tìm nhị phân của dãy siêu tăng

```python
S = 3301
d = [12,17,33, 74, 157, 316, 620, 1230, 2460] # dãy siêu tăng
result = ''
for i in d[::-1]:
  if S - i >= 0:
    result += '1'
    S -= i
  else: result += '0'
print(result[::-1])
```