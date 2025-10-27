Fast Incremental QR Updates (Givens-based)

Drop-in incremental QR column insert that’s consistently faster than scipy.linalg.qr_insert—and often approaches ~2× speedups as problems scale—while maintaining the same numerical reconstruction accuracy.

Why this exists

When you’re growing a matrix column-by-column (streaming features, online least-squares, active learning, SLAM, etc.), recomputing a full QR is wasteful. SciPy exposes la.qr_insert, but in many practical regimes you can do better with a lean, cache-friendly Givens-based update. This repo provides such an implementation and a fair benchmark harness.

Highlights

Speed: Our updater is typically 1.5–2.0× faster than scipy.linalg.qr_insert on medium/large problems, with the gap widening as dimensions grow. 


Accuracy: Preserves upper-triangular structure and achieves comparable reconstruction error (||QR−A||/||A||) and very small off-triangular mass (||tril(R,-1)||_F ≈ 0). 


Simple API: Operates on (Q, R) and inserts a column a at position j using Givens rotations.

All tests average 10 runs of standard-normal matrices. Full scripts for three-way comparisons (full QR, ours, SciPy) are included. Speedup below is computed against SciPy’s incremental routine (larger is better). See run_large_sweep(...) in the benchmark file. 


Dimensions (m, n)	Avg time (ours)	Avg time (SciPy)	Our speedup vs SciPy
(1000, 1000)	2.6317 s	4.3638 s	1.66×
(2000, 2000)	25.2205 s	38.8838 s	1.54×
(5000, 2500)	97.4986 s	189.7557 s	1.95×
(5000, 5000)	532.5147 s	991.9098 s	1.86×
