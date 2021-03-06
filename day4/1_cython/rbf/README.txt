1. Speed optmization using Cython
---------------------------------
---------------------------------

b. Take a look at the example rbf.py which uses Gaussian Radial Basis Functions (RBFs) as an approximation scheme for some given data. How much speed up do you gain by using existing Python packages like Scipy?
-----------------------------------

Performance:

Python:  38.75555396080017
Scipy:  0.1566295623779297

Scipy is more than 200 times faster than the pure Python implementation.

---------------------------------------------------------------------------------------------------------------------------------------
c. In the above example, why is Scipy faster than the naive Python implementation? Which part of the Python code is slowing things down?
---------------------------------------------------------------------------------------------------------------------------------------
 
The naive Python implementation is slowed down because it has to iterate through X using indexing, while the Scipy version is in-place (see below).

Total time: 27.4525 s
File: rbf.py
Function: rbf_network at line 6

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     6                                           @profile
     7                                           def rbf_network(X, beta, theta):
     8
     9         1          4.0      4.0      0.0      N = X.shape[0]
    10         1          2.0      2.0      0.0      D = X.shape[1]
    11         1          7.0      7.0      0.0      Y = np.zeros(N)
    12
    13      1001       1011.0      1.0      0.0      for i in range(N):
    14   1001000    1038637.0      1.0      3.8          for j in range(N):
    15   1000000     945302.0      0.9      3.4              r = 0
    16   6000000    6361290.0      1.1     23.2              for d in range(D):
    17   5000000   13420725.0      2.7     48.9                  r += (X[j, d] - X[i, d]) ** 2
    18   1000000    1644466.0      1.6      6.0              r = r**0.5
    19   1000000    4041073.0      4.0     14.7              Y[i] += beta[j] * exp(-(r * theta)**2)
    20
    21         1          1.0      1.0      0.0      return Y

Total time: 0.156567 s
File: rbf.py
Function: rbf_scipy at line 24

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
    24                                           @profile
    25                                           def rbf_scipy(X, beta):
    26
    27         1          8.0      8.0      0.0      N = X.shape[0]
    28         1          3.0      3.0      0.0      D = X.shape[1]
    29         1     118263.0 118263.0     75.5      rbf = Rbf(X[:,0], X[:,1], X[:,2], X[:,3], X[:, 4], beta)
    30                                               #Xtuple = tuple([X[:, i] for i in range(D)])
    31         1         28.0     28.0      0.0      Xtuple = tuple([X[:, i] for i in range(D)])
    32
    33         1      38265.0  38265.0     24.4      return rbf(*Xtuple)


-------------------------------------------------------------------
d. How much can you improve the performance of rbf.py using Cython?
-------------------------------------------------------------------

Performance: 

Python:  38.75555396080017
Scipy:  0.1566295623779297
Cython:  5.663907051086426

Cython is about seven times faster than the pure Python implementation, but still much slower than Scipy.
