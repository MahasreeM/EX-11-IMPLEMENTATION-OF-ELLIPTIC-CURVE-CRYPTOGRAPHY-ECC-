## EX 11 : IMPLEMENTATION OF ELLIPTIC CURVE CRYPTOGRAPHY (ECC)
## REG.NO:212224110035
## DATE:25-10-2025
## AIM:

To implement the Elliptic Curve Cryptography (ECC) algorithm for basic point addition and scalar multiplication on an elliptic curve over a finite field.


## ALGORITHM:
1.	Start the program.
2.	Select the parameters of the elliptic curve:
•	Choose a prime number p (field size).
•	Choose curve parameters a and b for the equation:
y2 = x3 + ax + b modp
2.	Define a base point (generator) G(x, y) on the curve.
3.	Choose private keys:
•	User A chooses a random private key a.
•	User B chooses a random private key b.
4.	Compute public keys:
•	User A computes public key A = a * G (scalar multiplication).
•	User B computes public key B = b * G.
5.	Exchange public keys.
6.	Each user computes the shared secret:
•	User A computes: S = a * B
•	User B computes: S = b * A
•	Since ECC is based on scalar multiplication, both S values are equal.
7.	The shared secret is now established.
8.	End the program.

## PROGRAM:
```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define PRIME_P 97  // prime modulus
#define A 2
#define B 3

typedef struct {
    int x;
    int y;
    int infinity; // 1 if point at infinity
} Point;


int modInverse(int k, int p) {
    int res = 1;
    int exp = p - 2;
    k = k % p;
    while (exp > 0) {
        if (exp % 2) res = (res * k) % p;
        k = (k * k) % p;
        exp /= 2;
    }
    return res;
}


Point pointAdd(Point P1, Point P2) {
    Point R;

    if (P1.infinity) return P2;
    if (P2.infinity) return P1;

    if (P1.x == P2.x && (P1.y + P2.y) % PRIME_P == 0) {
        R.infinity = 1;
        return R;
    }

    int lambda;
    if (P1.x == P2.x && P1.y == P2.y) {
        lambda = ((3 * P1.x * P1.x + A) * modInverse(2 * P1.y, PRIME_P)) % PRIME_P;
    } else {
        lambda = ((P2.y - P1.y) * modInverse(P2.x - P1.x, PRIME_P)) % PRIME_P;
    }

    R.x = (lambda * lambda - P1.x - P2.x) % PRIME_P;
    if (R.x < 0) R.x += PRIME_P;

    R.y = (lambda * (P1.x - R.x) - P1.y) % PRIME_P;
    if (R.y < 0) R.y += PRIME_P;

    R.infinity = 0;
    return R;
}


Point scalarMul(int k, Point P) {
    Point R = {0, 0, 1}; // point at infinity
    Point addend = P;

    while (k > 0) {
        if (k & 1) R = pointAdd(R, addend);
        addend = pointAdd(addend, addend);
        k >>= 1;
    }
    return R;
}

int main() {
    srand(time(NULL));

    Point G = {3, 6, 0}; 
    int privA = rand() % (PRIME_P - 1) + 1; 
    int privB = rand() % (PRIME_P - 1) + 1; 

    
    Point pubA = scalarMul(privA, G);
    Point pubB = scalarMul(privB, G);

    printf("Alice Private Key: %d\n", privA);
    printf("Alice Public Key: (%d, %d)\n", pubA.x, pubA.y);

    printf("Bob Private Key: %d\n", privB);
    printf("Bob Public Key: (%d, %d)\n", pubB.x, pubB.y);

    
    Point shared1 = scalarMul(privA, pubB);
    Point shared2 = scalarMul(privB, pubA);

    printf("Shared Secret (Alice): (%d, %d)\n", shared1.x, shared1.y);
    printf("Shared Secret (Bob): (%d, %d)\n", shared2.x, shared2.y);

    return 0;
}
```
## OUTPUT:

<img width="723" height="299" alt="Screenshot 2025-10-25 132816" src="https://github.com/user-attachments/assets/61dcd504-324f-40dd-8dd6-474b74ea7065" />

## RESULT:

The ECC program successfully demonstrates point addition and scalar multiplication on an elliptic curve over a finite field, and the simulation has been executed and verified successfully.
