# COMP4651 Assignment 01: EC2 Measurement (8 marks)

### Deadline: 23:59, Oct. 10, Thursday

---

### Name: TAHA, Mohammad
### Student Id: 20740046

### Email: mtaha@connect.ust.hk

---



## Question 1: Measure the EC2 CPU and Memory performance

1. (0.5 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    > The used measurement tool is sysbench. which is an open-source modular, cross-platform and multi-threeaded benchmarking tool.
    >
    > For the CPU test, the configuration involves verifying prime numbers by performing standard division of numbers up to 50,000 (the square root of which is approximately 224) across 1,000 threads. This means calculating prime numbers concurrently 1,000 times using the command: sysbench --test=cpu --num-threads=1000 --cpu-max-prime=50000 run. This setup will allow for a significant evaluation of the computing power of various EC2 instance sizes, putting stress on specific CPU features. The total time taken for the test will be measured from the start of the request until completion, with negligible overhead assumed for shared memory access among threads.
    >
    > For the memory test, write memory operations will be conducted with a total memory size of 100GB, ensuring that operations are not cached in memory and are executed with a local memory scope across 20 threads. The memory benchmarking will be performed concurrently through the command: sysbench --test=memory --num-threads=20 --memory-total-size=100G --memory-scope=local run. Differences in memory performance can be observed through throughput measured in MiB/sec.

2. (1 mark) Run your measurement tool on general purpose `t2.small`, `t3.medium`, and `c3.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **N. Virginia** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

    In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

    | Size        | CPU performance | Memory performance |
    | ----------- | --------------- | ------------------ |
    | `t2.small`  | 10.4012s         |      542.18 MiB/s  |
    | `t3.medium` |14.2350s          |      5277.96 MiB/s |
    | `c3.large`  | 12.2232s          |   730.08 MiB/s    |

    > Region: `N. Virginia`. Use `Ubuntu Server 20.04 LTS (HVM)` as AMI.

## Question 2: Measure the EC2 Network performance

1. (2 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t2.small` - ``t2.small`` |       2057     |    0.065 |
    | `t3.medium` - `t3.medium` |       4740     |   0.011  |
    | `c3.large` - `c3.large`   |       1620     |  0.110   |
    | `t2.small` - `c3.large`   |      1780      |    0.076 |
    | `t3.medium` - `c3.large`  |        3158    |   0.059  |
    | `t3.medium` - `t2.small`  |     3276       |   0.057  |

    > Region: `N. Virginia`. Use `Ubuntu Server 20.04 LTS (HVM)` as AMI. You should launch **6** instances in total.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      |       3768     |    0.03  |
    | N. Virginia - N. Virginia |     4740       |    0.011  |
    | Oregon - Oregon           |       4740     |    0.011  |

    > Region: `N. Virginia`/`Oregon`. Use `Ubuntu Server 20.04 LTS (HVM)` as AMI. All instances are `t3.medium`.
    
3. (1 mark) Is network performance consistent over time? You can do measurements at different times of a day and compare the results. Please give at least 2 possible reasons why network performance is inconsistent.

    > 1) Network Congestion: When multiple users or devices access the network simultaneously, especially during peak usage times (e.g., evenings or weekends).
    > 2) Service Priority: The cloud service provider may prioritize users who have reserved the servers. This will affect users who are accessing the service on spot. They may also be prioritizing certain types of traffic over others (e.g., video streaming).


## Question 3：Decision Trees

We would like to construct a decision tree for n vectors each with m attributes.

1. (0.5 mark) Assume that there exists i and j such that for ALL vectors $X$ in our training data, these attributes are equal ($x_i=x_j$ for all vectors where xi is the i’th entry in the vector $X$). Assume that we break ties between them by using $x_i$ (that is, if both lead to the same conditional entropy we would use $x_i$). Can removing attribute $j$ from our training data change the decision tree we learn for this dataset? Explain briefly.

Answer: It will not make a difference because attribute j provides no additional information beyond what attribute i does. When constructing the decision tree, if a split decision involves these attributes, the algorithm will always choose ( x_i ) due to the tie-breaking rule. Therefore, ( x_j ) is redundant, and its removal does not affect the structure or the decisions of the tree.

2. (0.5 mark) Assume we have two equal vectors $X$ and $Z$ in our training set (that is, all attributes of $X$ and $Z$ including the labels are exactly the same). Can removing $Z$ from our training data change the decision tree we learn for this dataset? Explain briefly.

Answer: Yes, the decision tree can vary. The conditional entropy at each split is influenced by the set of samples, and duplicating a vector could alter the distribution, resulting in the selection of a different attribute for the split.

For the next set of questions consider a dataset with continuous attributes. For such attributes we can use threshold splits to determine the best partition for a set of vectors. Assume we are at the root and we have n vectors, all with different (continuous) values for attribute $x_1$.

3. (0.5 mark) Assume we would like to use binary splits. For such splits we need to choose a value a and split the data by propagating all vectors with $x_1<a$ to the left and those with $x_1>=a$ to the right. For any value of $a$ we consider we would like to have at least one vector assigned to each of the two branches of the split. How many values of a do we need to consider?

Answer: To ensure that each split has at least one vector on both sides, we need to consider the values of (a) that lie between each pair of consecutive values of (x_1). Given that there are (n) vectors with different values for (x_1), there are (n-1) gaps between these values. Therefore, we need to consider (n-1) possible values for (a).

4. (0.5 mark) Assume we would like to use three way splits. For such splits we need to chose values $a$ and $b$ such that $a$ < $b$ and split the data into three sets: $x_1 < a, a <= x_1 < b,
x_1 >= b$. Again we require that for any value of $a$ and $b$ that we consider at least one vector would be assigned to each of the three branches. How many $\{a,b\}$ do we need to consider?

Answer: Any two values selected as part of question 3 can be used for three-way splits. This results in considering ((n-1)(n−2))/2 combinations.


5. (0.5 mark) For a given three way split at the root (parameterized by an $\{a,b\}$ pair) can we reconstruct the same split with a tree that uses only binary splits?
If no briefly explain why.
If yes, show the tree that leads to the same set of leaves with the same nodes in each leaf as the three way split.

<img width="304" alt="image" src="https://github.com/user-attachments/assets/ae6e1278-696d-4e0c-b3c2-24fbfd787f81">


