# EX-11-ECC

## AIM:
To implement the Elliptic Curve Cryptography (ECC) key exchange algorithm and securely derive a shared secret key.

## DESIGN STEPS:
Step 1:
Choose a large prime number p and an elliptic curve defined by the equation 𝑦^2 = 𝑥^3 + 𝑎𝑥 +𝑏 mod p along with a base point G on the curve.

Step 2:
Alice and Bob choose private keys.

Step 3:
Compute public keys
```
public_key = private_key * G (point multiplication).
```
Step 4:
Exchange public keys.

Step 5:
Compute the shared secret
```
shared_secret = private_key * public_key_received.
```
## PROGRAM:
```c
#include <stdio.h>
typedef struct {
    long long int x, y;
} Point;
long long int modInverse(long long int a, long long int m) {
    long long int m0 = m, t, q;
    long long int x0 = 0, x1 = 1;
    if (m == 1) return 0;
    while (a > 1) {
        q = a / m;
        t = m;
        m = a % m, a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;
    }
    if (x1 < 0) x1 += m0;
    return x1;
}
Point pointAddition(Point P, Point Q, long long int a, long long int p) {
    Point R;
    long long int lambda;
    if (P.x == Q.x && P.y == Q.y) {
        lambda = (3 * P.x * P.x + a) * modInverse(2 * P.y, p) % p;
    } else { // Point addition
        lambda = (Q.y - P.y) * modInverse(Q.x - P.x, p) % p;
    }
    R.x = (lambda * lambda - P.x - Q.x) % p;
    R.y = (lambda * (P.x - R.x) - P.y) % p;
    R.x = (R.x + p) % p;
    R.y = (R.y + p) % p;

    return R;
}
Point scalarMultiplication(Point P, long long int k, long long int a, long long int p) {
    Point result = P;
    k--;

    while (k > 0) {
        result = pointAddition(result, P, a, p); 
        k--;
    }
    return result;
}

int main() {
    long long int p, a, b, privateA, privateB;
    Point G, publicA, publicB, sharedSecretA, sharedSecretB;
    printf("Enter the prime number (p): ");
    scanf("%lld", &p);
    printf("Enter the curve parameters (a and b) for equation y^2 = x^3 + ax + b: ");
    scanf("%lld %lld", &a, &b);
    printf("Enter the base point G (x and y): ");
    scanf("%lld %lld", &G.x, &G.y);

    printf("Enter shin's private key: ");
    scanf("%lld", &privateA);
    printf("Enter chan's private key: ");
    scanf("%lld", &privateB);

    publicA = scalarMultiplication(G, privateA, a, p);
    publicB = scalarMultiplication(G, privateB, a, p); 

    printf("shin's public key: (%lld, %lld)\n", publicA.x, publicA.y);
    printf("chan's public key: (%lld, %lld)\n", publicB.x, publicB.y);

    sharedSecretA = scalarMultiplication(publicB, privateA, a, p);
    sharedSecretB = scalarMultiplication(publicA, privateB, a, p); 

    printf("Shared secret computed by shin: (%lld, %lld)\n", sharedSecretA.x, sharedSecretA.y);
    printf("Shared secret computed by chan: (%lld, %lld)\n", sharedSecretB.x, sharedSecretB.y);

    if (sharedSecretA.x == sharedSecretB.x && sharedSecretA.y == sharedSecretB.y) {
        printf("Key exchange successful. Both shared secrets match!\n");
    } else {
        printf("Key exchange failed. Shared secrets do not match.\n");
    }

    return 0;
}

```
## OUTPUT:
![image](https://github.com/user-attachments/assets/ec26311f-1a3c-4db3-b725-fa727c95f9f7)


## RESULT:
The program for Elliptic Curve Cryptography (ECC) was executed successfully, and both Alice and Bob computed the same shared secret.
