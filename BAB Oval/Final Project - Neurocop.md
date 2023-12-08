**CS 695 - AI AND SAFETY ASSURANCE**

**Group Name:**

NEUROCOP

**Group Members:**

Anjali Maddila - G01381846

Kavya Tolety - G01378070 

Anjali Jayadeva Murthy - G01373209

**Project**

**Algorithm Description**

- The verification problem can be rephrased as a global optimization problem

- Seeks global solution(s) of a constrained optimization model

- This paper will present an approach to estimate the global minimum, based on the Branch-and-Bound paradigm

**BaB Pseudocode**

- **A search strategy,** defined by the pick\_out function, which chooses the next problem to branch on

- **A branching rule**, defined by the split function, which takes a problem prob and returns its partition into subproblems such that Ui subprob\_i = prob and that (subprob\_i ∩ subprob\_j) = ∅, ∀i ̸= j.

- **Bounding methods**, defined by the compute\_{UB,LB} functions. 

These procedures estimate respectively upper bounds (prob\_ub) and lower bounds (prob\_ub) over the minimum output that the network net can reach over a given (sub-)problem.

**Algorithm 1 Branch and Bound**

1: function BaB(net, problem, )

2: global\_ub ← inf

3: global\_lb ← − inf

4: probs ← \[(global\_lb, problem)]

5: while global\_ub − global\_lb >  do

6: (\_ , prob) ← pick\_out(probs)

7: \[subprob\_1, . . . , subprob\_s] ← split(prob)

8: for i = 1 . . . s do

9: prob\_ub ← compute\_UB(net, subprob\_i)

10: prob\_lb ← compute\_LB(net, subprob\_i)

11: if prob\_ub < global\_ub then

12: global\_ub ← prob\_ub

13: prune\_problems(probs, global\_ub)

14: end if

15: if prob\_lb < global\_ub then

16: problems.append((prob\_lb, subprob\_i))

17: end if

18: end for

19: global\_lb ← min{lb | (lb, prob) ∈ probs}

20: end while

21: return global\_ub

22: end function

- Its generic form is described in Algorithm 1. Line 7 breaks the original problem into multiple sub-problems (either splitting the input domain into subdomains or splitting an unfixed ReLU activation unit 1 into different phases), and then in lines 9 & 10 the  lower and upper bounds are computed.

* The current best upper-bound is a candidate for the global minimum. In lines 13, 15 & 17, any subproblem whose lower limit is greater than the present global upper bound, it can be pruned since a subproblem cannot contain the global minimum.

- Tighter lower bounds can be computed by repeatedly dividing the (sub-)problems.  In line 19, by taking the minimum over the lower bounds of all sub-problems We keep track of the global lower bound on the minimum.In line 5, We consider that we have converged when the difference between the global upper bound and the global lower bound is less than an extremely small scalar.

* How to optimize and find the global minimum is demonstrated by the Branch & Bound Algorithm. The method can be made simpler if we just care about the satisfiability problem, in which case we can start the global upper bound at 0 as given in line 2. Line 15 will remove any sub-problem that has a lower bound greater than 0 and is hence ineligible to include a counterexample.

- Therefore, without altering the algorithm, the computation of the lower bound can be substituted with the feasibility problem (or its relaxation) imposing the constraint that the result is less than zero. There may still be a counterexample if it is feasible, in which case more branching is required else the sub-problem can be pruned out. 

* Furthermore, In line 11 the process can be terminated if any upper bound improving on 0 is discovered on a sub-problem, as this already suggests the existence of a counterexample.

- Both the pseudocode of Algorithm 1 and the description of the verification problem as optimization are generalized and would apply to verification problems other than the particular situation of PL-NN. A practical algorithm requires the specification of multiple elements like the search strategy, branching rule and Bounding methods.

* A **search strategy** that selects the subsequent issue to branch on based on the pick\_out function given in the algorithm. There are various heuristics available, such as those that rely on the outcomes of preceding bound computations. This enables us to find appropriate upper bounds for satisfiable issues or optimization problems, allowing for early pruning. 

- A **branching rule** represented by the split function divides a problem (prob) into subproblems so that S i subprob\_i = prob and (subprob\_i ∩ subprob\_j) = ∅, ∀i 6= j. This will ascertain the characteristics of the (sub-)problems, hence influencing the computational bounds' hardness. Furthermore, the quality of the generated bounds can be significantly affected by selecting the appropriate partition. 

* The **Bounding methods** are defined by compute\_{UB, LB} functions. For a given (sub-)problem, these methods estimate upper bounds (prob\_ub) and lower bounds (prob\_ub) over the minimum output that the network net can reach. 

- In order to facilitate the pruning of this (sub-)problem, we want to maximize the lower bound. Usually, convex relaxations of the (sub-)problem are introduced, and then they are minimized. 

* Conversely, the estimated upper bound ought to be as small as feasible to enable the pruning of additional (sub-)problems or the identification of counterexamples. Heuristic approaches suffice since any viable point corresponds to an upper bound on the minimum.

**Example Illustration**

**BaB Reformulations**

- ReluVal does not rely on SMT (Satisfiability modulo theories) Solvers

- Reluplex and Planet depend on SMT Solvers

**SMT Based methods:** 

- Reluplex (Reluplex -> ReLU + Simplex Algorithm) - SMT Solver uses the splitting-on-demand framework

- Planet - Divide the problem until you reach infeasibilities, then backtrack

**Branch and Bound:**

This framework deploys a search strategy. The strategy is to create a feasibility problem for the existence of a counterexample. If the problem is feasible, then a counterexample is shown and the property is False. If the problem is infeasible, then no counterexample can exist and the property is True.

It utilizes the relu and planet strategies to find the solution to the optimization problem.

1. Reluplex - This algorithm always maintains an assignment to all variables even if the constraints are violated. Now there are 2 types of constraints- linear constraints and relu constraints . Reluplex always prioritizes the linear constraints over the relu constraints. Step by step demonstration will be done in the toy example below.

2. Planet -This algorithm divides the problem until you reach infeasibilities and then backtracks.

3. Branch and Bound approximations - Here our goal is to evaluate the worst case and split the problem into further subproblems. We use Planet approximation and compute the minimum of y.

Consider a concrete example also known as the toy example. The constraints are as defined below:

![](https://lh7-us.googleusercontent.com/-dC0CvoMttdpEL44M6_WixczaTosrt3XX2UjlaT7cHJpbs6ihHC83Tg7AnnrcMcNm0vdAJM5q73ialJjtK02tn2Qk9gV2K2I7XvewhJU-UvZn_arXcbBIMH14z7xWHwmfgkeyhsIUnSdePXPogTDEtc)

Let's start by assigning values to the variables. 

Iteration 1, We initialize all the variables to 0.

|    |    |      |       |      |       |   |
| -- | -- | ---- | ----- | ---- | ----- | - |
| x1 | x2 | a in | a out | b in | b out | y |
| 0  | 0  | 0    | 0     | 0    | 0     | 0 |

This will violate the linear constraint y<=-5 .

Hence in Iteration 2 we assign the value to y.

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| 0  | 0  | 0    | 0     | 0    | 0     | -5 |

This assignment violates the linear constraint y = -aout -b out, hence needs to be fixed.

Iteration 3:

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| 0  | 0  | 0    | 1     | 0    | 4     | -5 |

Now the relu constraints are violated and need to be fixed .

Hence in Iteration 4  we make b in == b out :

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| 0  | 0  | 0    | 1     | 4    | 4     | -5 |

At this point both relu constraint as well the linear constraint is violated but the linear constraint b in = -x1 - x2 gets prioritized .

Hence the 5th iteration becomes as follows:

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| -2 | -2 | 0    | 1     | 4    | 4     | -5 |

The values for x1,x2 are assigned by calculating it from the above-mentioned linearity.

Iteration 6 : We assign a value to the a in from the linearity a in = x1 + x2

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| -2 | -2 | 4    | 1     | 4    | 4     | -5 |

Iteration 7 : Now at this point , relu constraint is violated and hence we need a feasible assignment to aout but we have worked on the same linear piece repeatedly without any progress. This is when we know that we need to divide the problem into subproblems and hence branching occurs based on the possible values from relu. a out can be a out <= 0 or a out <= a in. In Cases where no violated constraints exist , it means that the satisfiable assignment has been found and the algorithm terminates early since we found a counter example and hence the property is false and has no optimal solution.

![](https://lh7-us.googleusercontent.com/tbva-9kNU5CTv0JaKGNH4r64RqmYt55MZi8lgM0gLJekHJJsr23q81vJuO_NFBRydwyudtemF1-UfGvwSnecpr2jktwaq99j2KWAPeQQ7DUrIC_-evMumSrJTy8Jn0I1J8-X69p1EIhJ8CCd1YIu2EU)

Iteration 8 :This is where the planet comes into picture to build the linear approximations for the network. Below are the updated constraints as a part of the planet's network approximations. In planet the search strategy simply iterates over all the subproblems that havent been pruned and tries to find a complete assignment.![](https://lh7-us.googleusercontent.com/GmaFREqdM8-3O-PkQ1j_26vD75UOgXC5LLpIGXEcBIoyNSlRiSwrQ-i2JwPJnRmTHTKOz7mxYCCauVBNOpAKVq9tSmWi_VFb84rCeY5iMCsFYIpNG4lC5VmZVfcohvsqH3hhXP2WsKX0jaCD553Typ0)

Iteration 9: From iteration 7, we understood that a out can have two possible feasible solutions, 

Case 1 : When a out =0 , b out can have 2 possible values namely b out ! = 0 and b out = 0 but both these values are not feasible since it violates the linear constraints y = -a out - b out and y<= -5 on substitution. Hence we can prune this branch.

|    |    |      |       |      |          |    |
| -- | -- | ---- | ----- | ---- | -------- | -- |
| x1 | x2 | a in | a out | b in | b out    | y  |
| -2 | -2 | 4    | 0     | 4    | b in = 4 | -5 |
|    |    |      |       |      | 0        |    |

Case 2 : When a out = a in ,

We have 2 possible branches here, b out =0 and b out = b in. Lets start with : 

1. b out = 0 for the constraints mentioned about. Is there a feasible solution ? No.

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| -2 | -2 | 4    | 4     | 4    | 0     | -5 |

This violates the linear constraint y = -a out - b out => y <= -5 . -4 is not less than -5, Hence this is not a valid assignment 

2.  b out ! = 0. This path has an assignment for the variables. But it violates the relu constraint as well as the linear constraint hence it is not feasible as well.

|    |    |      |       |      |       |    |
| -- | -- | ---- | ----- | ---- | ----- | -- |
| x1 | x2 | a in | a out | b in | b out | y  |
| -2 | -2 | 4    | 4     | 4    | 2     | -5 |

This tells us that we cannot find a feasible assignment and hence no counter example can exist. In other terms, the property holds. 

![](https://lh7-us.googleusercontent.com/RgbwKD7OTlZx0WwsYnfD8SGdzZZhrRXVlhBq72Wuwq5NCfCIlEQ1DbW5syLl05ZUdBWRp318iXGYLHREMdipltflWPfajGBWha36zUHegOrCuMe3D_I-E-PEn5_cht28JQHjMJbHqAGMTbZ9RBgwwR8)

The algorithm can be terminated here in our case since we figured out that the property holds true and there exists no counter example however in some cases to obtain better bounds we deploy the branch and bound strategy where we evaluate the worst case and if it’s too bad , then we split the problem into subproblems. We use planet approximations here as well and compute the minimum y. On a newly generated (sub-)problem, we can refine all the lower and upper bounds by, for instance, formulating corresponding linear programming problems with added constraints from the split. Then, we solve the minimum for lower and maximum for upper. With refined upper and lower bounds, we are able to introduce smaller convex relaxation hulls to obtain tighter relaxations. However, it should be noted there is a trade-off between the benefits of tighter relaxation and the overall computational efficiency. Since updating all the lower and upper bounds could be computationally expensive.

**Evaluation**

1. _What benchmarks were used ?_

**Small Networks**

In this paper, the performance of small networks with low dimensional input are evaluated:

1. CollisionDetection

2. ACAS

**"CollisionDetection" Dataset**

- In this dataset, the goal is to predict whether two vehicles following parameterized trajectories are likely to collide or not.

- This prediction is based on various features and properties

- Feature Extraction: To make predictions, the dataset has 500 properties extracted from the problems. These properties are derived from a binary search process

* **Network Architecture:** The neural network is "relatively shallow." - it has fewer layers or parameters

* Even with a shallow network, the complexity of the dataset can make the decision boundaries challenging to determine Satisfiable (SAT) and Unsatisfiable (UNSAT): In the context of this dataset, "satisfiable" refers to properties for which a counterexample can be found. In other words, if the prediction is false (indicating that a collision is predicted), it is considered satisfiable because a counterexample can prove the prediction wrong."Unsatisfiable" properties are those for which no counterexample exists, meaning the prediction is true (no collision is predicted)

* **Accuracy of Methods**: The text mentions that the results presented in Figure 1 (a) highlight the accuracy of the methods used in predicting collisions. In this context, "methods" likely refers to the various techniques and algorithms employed for making these predictions, including the use of neural networks and the 500 extracted properties. The accuracy of these methods is a measure of how well they perform in correctly predicting whether collisions will occur or not

![](https://lh7-us.googleusercontent.com/YOL7_kupHV9TsLJqxXMl2gfX_ge4ILYjALXt0exiAkv6hxpVLIZKXl-YXgRegziINWQO1byreboi0Mx9omECNN5BVohDVFNCz7gI5Xgs8XcS9ba36PV7G8jxdbA9xEdOaG8ryGpt_nGwgcl9WcR3-Rk)

_Figure 1_

**Airborne Collision Avoidance System (ACAS) Dataset**

- This is used for a specific task related to aviation safety. The neural network in this dataset is deeper, which presents scalability challenges for the evaluation of various algorithms and methods

- ACAS is a system designed to enhance the safety of aircraft by providing recommendations for horizontal maneuvers to avoid potential collisions. It does so based on sensor measurements and other relevant data. The dataset is likely derived from this system

- **Neural Network-Based Advisory System:** The dataset uses a neural network as the basis for its advisory system. This neural network is responsible for providing recommendations regarding the choice of horizontal maneuvers to prevent collisions

- **Maneuvers and Scores:** The neural network assesses the situation and assigns a score to each of the five possible maneuvers that an aircraft can undertake to avoid a collision. The assigned scores reflect the desirability or effectiveness of each maneuver

- The recommendation is then made based on the maneuver with the minimum score, which is considered the most suitable action to take

- **Properties to Verify:** The dataset includes 188 properties that need to be verified. These properties are likely related to specifications or conditions describing various scenarios in which the advisory system is expected to provide safe and effective recommendations

**Large Networks**

The performance of large networks are also evaluated:

1. Reduced Robust MNIST Network 

2. Robust MNIST Network

**Robust MNIST Network**

- The network architecture consists of 2 convolution layers followed by 2 fully connected layers, resulting in a total of 4804 activation nodes

- **Verification challenge:** The primary task related to the Robust MNIST Network is to verify the robustness of the network's predictions

- In this context, robustness involves evaluating whether the predicted label for each input image changes when the image is perturbed within a specific ε-infinity norm ball. This disturbances assesses how resilient the network's predictions are to slight variations in the input images

- In order to know the computation there is a time limit of 1 hr for both the MNIST networks. 

**Reduced Robust MNIST Network**

- A simplified version of the Robust MNIST Network is presented in order to assess the effectiveness of a particular branching heuristic

- This reduced network has the same structure as the original one but with fewer hidden nodes on each hidden layer, resulting in a total of 1226 ReLU nodes

- **Verification:** Different ε values are used for both networks to assess the difficulty level of the verification properties. A larger ε value implies a more significant disturbance of the input image. The choice of ε values is adjusted based on the network's robustness, with a higher ε used for the larger network, which is expected to be more robust

_2) Main evaluation results_

**Small Networks**

- In figure 1(a) of CollisionDetection, most solvers succeed against all properties in about 10 Seconds

- Planet, Reluplex and the MIP - are extremely fast.

- reluBaB - much slower than black box

- All methods solve quickly in the time gap

- In Figure 1(b), no errors are observed but most methods timeout on the most

- challenging test cases

- **The best method** - Reluplex, which reaches 79.26% success rate at the two hours

- BaBSB, already achieves 98.40% with a budget of one hour

- To reach Reluplex’s success rate, the required runtime is two orders of magnitude

smaller

- Contains a limited number of verification properties or scenarios that need to be assessed for accuracy and reliability

**Large Networks**

- Provides set of methods and tools for analyzing the behavior and vulnerabilities of

neural networks

- The performance of various methods is evaluated on the reduced Robust MNIST

Network and the Robust MNIST Network, both of which involve a large number

of properties to cover a range of difficulties

- MIPplanet is considered the benchmark, and methods that couldn't perform at least as

well as MIPplanet on simple properties are excluded

- ERAN stands for "ETH Robustness Analyser for neural networks.” - tool for finding

the robustness of the Neural Network

- ERAN - interpretation-based tool for neural network verification

- Planet, ReluBaB, Reluplex, and Neurify couldn't solve any properties within a 100-

second time limit

- BaBSB managed to solve some properties but was not as effective as MIPplanet

- BaBSR and ERAN performed as well as MIPplanet and were tested on all properties of large networks

- In these comprehensive tests, BaBSR performed the best among all methods on both data sets

_3) Briefly describe the tools/techniques that were used for comparison_

- **Random Sampling:** Points on the subproblem are randomly sampled to estimate

upper bounds

- **Fast Bounds:** method or technique for quickly estimating the bounds on the variables

within a given optimization problem

- **Longest edge:** divide the input domain by selecting the dimension or direction with

the largest extent (i.e., the longest edge) and creating two subdomains based on this

  dimension

- **imb:** Stands for intermediate bounds, which are estimates of the lower and upper

bounds during the optimization process

- **LP Solver:** a software tool/ algorithm, designed to solve the linear programming

prob

- **Approximation strategies in neural networks (NN):** are used to handle complex

computations, particularly in situations where exact calculations are computationally

expensive or infeasible

- The fundamental baseline that the paper addresses is **BlackBox**, which is simply the constraints encoded directly into the Gurobi solver. This solution will perform its own relaxation, regardless of the problem's complexity. 

- The paper utilizes the publicly accessible versions of Reluplex and Planet, C++ is used to implement both the algorithms

- The Gurobi MIP solver was used in the paper's Python reimplementation of the method.

The paper presents results for a variation known as **MIPplanet**, which does not use simple interval arithmetic but instead uses bounds obtained from Planet's convex relaxation.
