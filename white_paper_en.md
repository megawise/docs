# MegaWise

## Introduction

Following the continuous development of digitalization, data is becoming increasingly important in our everyday life. It could be stated without exaggeration that we are in an era of data, which has a strong requirement for data analytics and intelligence. Our knowledge about the world and the society is driven by ubiquitous data. With the advent of 5G technologies, the storm of data is skyrocketing in this era.

- Data explosion is expected to increase the data quantity by at least 6 times from 2018 to 2025.
- The number of data connections grows exponentially and the network connection density is about 1,000 Connection / KM<sup>2</sup> for 4G and 1,000,000 Connection / KM<sup>2</sup> for 5G.


However, per the previous experience in big data processing, increase in data size does not necessarily lead to increased value. Also, as the performance increase of traditional processors is gradually slowing down and Moore' Law is no longer applicable, it is no longer appropriate to use traditional methods for data analytics.

To assist enterprise users in dealing with new challenges in the data era, ZILLIZ developed a new generation OLAP analytics engine, MegaWise, with full intellectual property. MegaWise is the first to use the massively parallel processing power of GPUs to speed up SQL operations. Compared with traditional solutions, MegaWise has higher throughput, higher cost-effectiveness, and lower latency. While significantly reducing the computation cost per unit, MegaWise can return query results for billion-scale datasets in subseconds.

## MegaWise analytics engine

### Native support for SQL

MegaWise supports standard SQL and PostgreSQL grammar. The following interfaces are provided for MegaWise:

- Command-line interface
- JDBC and ODBC

### Vectorized queries and concolic execution

Vectorized queries lay foundation to accelerated data analytics of MegaWise. Vectorized code allows the processor to compute multiple data items simultaneously. Currently, a single GPU contains thousands of compute units, which indicates that vectorized query in GPUs can process thousands of data items in parallel for a single period. Compared with CPUs, the concurrency of data processing is increased by 2 to 3 orders of magnitudes.

Vectorized queries can also be applied to CPUs. Modern CPUs usually provide advanced vector extensions which integrates wide-character execution units that can process multiple data items in parallel. The SQL engine of MegaWise can drive multiple GPUs and CPUs for vectorized queries. Even for CPUs, there is a performance increase in vectorized queries compared with traditional multi-threading methods.


### Multi-layer data cache


MegaWise completely optimized the memory and the computing layer for the best performance. MegaWise builds 3 layers of cache in each physical node, including GPU video memory, main memory, and SSD. Data can be intelligently placed based on data hotness and compute locality. Hot data that needs to be frequently and continuously processed is saved to the GPU video memory to avoid PCIe, thus reaching the highest access speed. MegaWise can also use NVIDIA NVLink to speed up data transfer from CPUs to GPUs and are faster by 2.5 times compared with systems without NVLink. This feature can be applied in IBM OpenPOWER.

The cache system of MegaWise supports data exchange with exterior systems. The data is compatible with the Apache Arrow standard. You can implement data exchange in both video memory and main memory and use zero-copy data transfer mode.

### Dynamic query compilation

MegaWise supports common SQL analysis operations for up-level applications, including filtering, clustering, connecting, and provides field-specific acceleration operands, including geospatial analysis, vector analysis, and time-series analysis. In order to screen the complexity and heterogeneity of algorithms and devices and exploit the computing power of the hardware accelerator to the fullest, ZILLIZ builds a JIT compilation system based on LLVM in MegaWise. LLVM enables MegaWise to convert queries to intermediate code that is independent from the architecture. The intermediate code then generates target code based on the hardware environment and performs specific optimization on hardware. In this way, MegaWise can be supported across different platforms and devices. Currently, MegaWise can run with high efficiency in NVIDIA GPU, x64 CPU, POWER CPU, and ARM CPU.


The JIT system also increases the compilation speed with less than 20 milliseconds per query. Combining with the query plan cache system, the average compilation time for queries is usually less than 10 milliseconds. With the support of the JIT system, you can conveniently customize data analytics with SQL and exploit the heterogeneous computation of CPUs/GPUs to the fullest without taking into consideration the differences in hardware and algorithm implementation.

### No complex indexing, downsampling, or pre-aggregation

Traditional data repositories often use indexing, downsampling, and pre-aggregation to support massive-scale data analytics. These technologies need a long pre-processing step and cannot meet the requirements to process massive-scale, streaming data in real time. In comparison, MegaWise does not need indexing, downsampling, or pre-aggregation. With the support of GPUs, MegaWise can perform real-time queries for billion-scale datasets in tens or hundreds of milliseconds.

Avoiding using indexing, downsampling, and pre-aggregation comes with the following benefits: first, you do not have to waste extra resources modelling data. You just need to import the data to perform data queries in real time. Second, less pre-processing means that MegaWise can load data faster, which is essential for scenarios such as data stream processing and high-frequency data processing.



### Performance/cost-effectiveness analysis

The following test uses data from New York taxi data (1.1 billion in total).

#### Comparing single-node MegaWise with other cluster products

- Test environment

|              | Number of physical nodes | Number of CPU cores | Number of GPUs | Memory volume（GB） | SSD volume（TB） |
| :------------: | ----------: | --------: | --------:| ---------------- | ------------------: |
| MegaWise     | 1          | 48       | 4        | 512              | 1.0                |
| Redshift     | 6          | 192      | 0        | 1464             | 15.4               |
| Spark 2.4    | 21         | 84       | 0        | 315              | 1.7                |
| Presto 0.214 | 21         | 84       | 0        | 315              | 1.7                |

> Data referenced from：https://tech.marksblogg.com/benchmarks.html

- Performance

|              | Q1   | Q2   | Q3   | Q4    |
| :------------: | ----: | ----: | ----: | -----: |
| MegaWise     | 0.01 | 0.12 | 0.13 | 0.24  |
| Redshift     | 1.56 | 1.25 | 2.25 | 2.97  |
| Spark 2.4    | 2.36 | 3.56 | 4.02 | 20.4  |
| Presto 0.214 | 3.54 | 6.29 | 7.66 | 11.92 |

> Data referenced from: https://tech.marksblogg.com/benchmarks.html

#### Comparing single-node MegaWise with other single-node products

- Test environment

|              | Number of physical nodes | Number of CPU cores | Number of GPUs | Memory volume（GB） | SSD volume（TB） |
| :------------: | ----------: | --------: | --------:| ---------------- | ------------------: |
| MegaWise      | 1          | 48       | 4        | 512              | 1.0                |
| Oracle 12.2   | 1          | 48       | 0        | 256              | 2.0                |
| PostgreSQL 10 | 1          | 128      | 0        | 1024             | 3.0                |

> Data referenced from：https://tech.marksblogg.com/benchmarks.html

- Performance

|              | Q1   | Q2   | Q3   | Q4    |
| :------------: | ----: | ----: | ----: | -----: |
| MegaWise     | 0.01 | 0.12 | 0.13 | 0.24  |
| Oracle 12.2     | 213.82 | 174.59 | 174.81 | 173.84  |
| PostgreSQL 10   | 56.55 | 56.92 | 89.90 | 94.37  |

> Data referenced from：https://tech.marksblogg.com/benchmarks.html


## Summary

The new data era brings new challenges. Traditional data analytics methods are trapped in the dilemma of low performance and high cost.

MegaWise is an OLAP acceleration engine that integrates traditional CPUs with new heterogeneous components. MegaWise can effectively increase performance through integration. With the cost under control, you can use MegaWise to exploit the value of data to the fullest.
