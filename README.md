# Shor's Period Finding (N=15, a=7)

> **Category**: cryptography &nbsp;|&nbsp; **Difficulty**: advanced &nbsp;|&nbsp; **Qubits**: 8 &nbsp;|&nbsp; **Gates**: 22 &nbsp;|&nbsp; **Depth**: 14

Shor's algorithm factors integers exponentially faster than any known classical algorithm. The key subroutine is quantum period finding: given f(x) = a^x mod N, find the smallest r such that a^r ≡ 1 (mod N). For a=7, N=15: r=4 (7¹=7, 7²=4, 7³=13, 7⁴=1 mod 15). QPE on the modular exponentiation unitary gives output 0, 2, 4, 6 (multiples of 2^n/r=4). This circuit is a pedagogical simplification; production circuits require full modular exponentiation with ~100 gates for N=15.

## Expected Output

Evaluation register: 0, 4, 8, or 12 (multiples of 16/4); r=4 from post-processing

## Circuit

The OpenQASM 2.0 circuit is in [`circuit.qasm`](./circuit.qasm).

```
OPENQASM 2.0;
include "qelib1.inc";
// Shor's period finding: f(x) = 7^x mod 15, period r=4
// ev[0..3]: evaluation register, tg[0..3]: target register
qreg ev[4];
qreg tg[4];
creg c[4];
// Superpose evaluation register
h ev[0]; h ev[1]; h ev[2]; h ev[3];
// Initialize target to |1> (= |0001> in binary with tg[0]=LSB)
x tg[0];
// Controlled modular multiplications: 7^(2^k) mod 15
// 7^1 mod 15 = 7 = |0111>: controlled from ev[3] (LSB of evaluation)
cx ev[3],tg[0]; cx ev[3],tg[1]; cx ev[3],tg[2];
// 7^2 mod 15 = 4 = |0100>: controlled from ev[2]
cx ev[2],tg[2];
// 7^4 mod 15 = 1 (identity): controlled from ev[1] — no gates needed
// 7^8 mod 15 = 1 (identity): controlled from ev[0] — no gates needed
// Inverse QFT on evaluation register
swap ev[0],ev[3];
swap ev[1],ev[2];
h ev[0];
cu1(-1.5707963267948966) ev[1],ev[0];
h ev[1];
cu1(-0.7853981633974483) ev[2],ev[0];
cu1(-1.5707963267948966) ev[2],ev[1];
h ev[2];
cu1(-0.3926990816987242) ev[3],ev[0];
cu1(-0.7853981633974483) ev[3],ev[1];
cu1(-1.5707963267948966) ev[3],ev[2];
h ev[3];
measure ev[0] -> c[0];
measure ev[1] -> c[1];
measure ev[2] -> c[2];
measure ev[3] -> c[3];
```

## Tags

`shor` `factoring` `period-finding` `cryptography` `exponential-speedup`

## References

- [Shor, P.W. (1994). Algorithms for Quantum Computation: Discrete Logarithms and Factoring. FOCS '94](https://doi.org/10.1109/SFCS.1994.365700)

## License

MIT — part of the [OpenQC Algorithm Catalog](https://github.com/openqc-algorithms).
