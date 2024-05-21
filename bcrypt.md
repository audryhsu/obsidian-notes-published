# bcrypt

A [password hashing](password%20hashing.md) function designed by Neils Provos and David Mazieres based on the Blowfish cipher in 1999. Uses salt to protect against rainbow table attacks and over time can increase the iteration count to remain resistant to brute-force search attacks.

Input: takes password string, a numeric cost, and 16-byte salt value.

Result:

`$2a$12$R9h/cIPz0gi.URNNX3kh2OPST9/PgBkqquzi.Ss7KIUgO2t0jWMUW
\__/\/ \____________________/\_____________________________/
Alg Cost      Salt                        Hash`


---
Related: [Authentication](./Authentication.md) - [Programming](Programming.md)