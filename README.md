# CuDNN v.s. non-CuDNN RNN benchmarks (implementation by Theano)

This is a toy benchmark between cudnn and non-cudnn version rnn implemented by theano.

## 1.Environment
|        |                                          |
|--------|------------------------------------------|
|   CPU  | Intel(R) Core(TM) i7-5930K CPU @ 3.50GHz |
|   GPU  | Nvidia GTX 1080, 8GB DDR5                |
| CUDA   | 8.0                                      |
| CuDNN  | verison 5105                             |
| Theano | 0.9rc2                                   |

All the test scripts run on the latest **GpuArray** backend of theano. 

## 2. Benchmarks settings
Non-CuDNN GRU implementation refers to the [dl4mt-tutorial](https://github.com/nyu-dl/dl4mt-tutorial)

All the GRU layer use 512 dimension input size and 1024 dimension hidden size.

Input data is random data with shape [50, 50, 512] generated by ```numpy.random.randn```.

Batch numbers are all 500.

## 3.Benchmarks

### a. Bidirectional GRU

|           | Compile foward | Compile forward + backward | Run forward       | Run forward + backward |
|-----------|----------------|----------------------------|-------------------|------------------------|
| CuDNN     | 0.0546s        | 3.5215s                    | 2630.67 samples/s | 563.69 samples/s       |
| non CuDNN | 7.2786s        | 29.4867s                   | 1321.97 samples/s | 242.58 samples/s       |

### b. Unidirectional GRU

|                  | Compile foward | Compile forward + backward | Run forward       | Run forward + backward |
|------------------|----------------|----------------------------|-------------------|------------------------|
| CuDNN GRU cell   | 2.4797s        | 14.4189s                   | 1901.69 samples/s | 330.43 samples/s       |
| non CuDNN        | 5.8573s        | 22.1874s                   | 2507.09 samples/s | 511.18 samples/s       |
| + no GEMM fusion | 9.8278s        | 29.4779s                   | 2022.11 samples/s | 411.24 samples/s       |

**Note:** 

- Theano doesn't officially provide api for GRU cell, hence I implement GRU cell by setting

CuDNN GRU's length with 1 and using ```scan``` to unroll the GRU cell. This may not be the best implementation.

- GEMM fusion can accelerate RNN quite a lot, but in some cases, such as ```gru_cond_layer```
in dl4mt session2, we cannot always take advantage of this trick. 
