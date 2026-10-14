# Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code

This issue brings you the Adaptive T-distribution Variation Slime Molding Optimization Algorithm (DTSMA). Tested on CEC2005, CEC2017, CEC2021, and CEC2022, the DTSMA has shown excellent performance! You can apply the improvement strategies mentioned in this article to enhance other intelligent algorithms.
References: Yin S, Luo Q, DU Y. DTSMA: Dominant Swarm with Adaptive T-distribution Mutation-based Slime Mould Algorithm. Mathematical Biosciences and Engineering: MBE, 01 Jan 2022, 19(3):2240-2285 https://doi.org/10.3934/mbe.2022105
The detailed principle explanation is included in the reference document.
Slime Molding Algorithm (SMA) is a metaheuristic algorithm inspired by slime mold vibration. Like other algorithms, SMA also has disadvantages such as an unbalanced exploration and exploitation and easy to fall into local optima. This paper proposes an improved SMA based on adaptive t-distribution mutation. In DTSMA, the superior group is used to improve the convergence speed of SMA, and the adaptive t-distribution mutation balances the exploration and exploitation capabilities of the algorithm. Additionally, a new development mechanism is mixed in, increasing the diversity of the population.
There are three improvements:
Improvement point 1
In the process of solving optimization problems, SMA does not utilize the information of the optimal positions of individual spores to update the solution, which may miss the opportunity to find a global optimum. In DTSMA, to record the historical optimal positions of individuals, advantageous groups X and their fitness values S are defined to store the historical optimal information. After updating the position, the updated position X is compared with the position Xgood in the advantageous group, and a greedy selection strategy retains the better position for the advantageous group. During the exploration phase, DTSMA uses the historical optimal positions of individuals found so far and the historical optimal positions of the population to update the search individual's position X. The formula for updating the spore position is shown as follows:
SMA sorts the fitness values of each individual during each iteration to find the best and worst fitness. The sorting process is time-consuming. To better utilize the sorted individual positions and fitness values, DTSMA divides the sorted population into two subpopulations, goodA X and goodB X. The values of A and B are:
Improvement point 2
SMA has strong development capabilities but weak exploration capabilities. The algorithm easily falls into local optima and experiences premature convergence. To balance exploration and exploitation, a mutation mechanism is added after the regeneration of the advantageous group. Gaussian and Cauchy mutation transitions between t distribution mutations are applied to SMA. The degrees of freedom of the t distribution mutation adaptively change with the number of iterations, which can well balance SMA's exploration and exploitation. When the degrees of freedom are large, the t distribution approaches the Gaussian distribution, and when it is 1, it is the Cauchy distribution.
The mathematical expression of the t distribution mutation operator is:
In DTSMA, the degree of freedom parameter tn increases nonlinearly with the number of iterations t.
The degree of freedom parameter tn enables DTSMA to approximate using the Cauchy mutation in early iterations to enhance the exploration capability, and use the Gaussian mutation in later iterations to concentrate on exploitation capability. During the iteration process of DTSMA, as the degree of freedom tn increases, the algorithm gradually shifts from focusing on global exploration capability to focusing on local exploitation capability.
1. Develop search operators during the development phase of DTSMA to increase the diversity of the myxozoan population. The developed operator is represented as follows:
This operator indicates that the search agents of the myxozoans will eventually stop at the current optimal position found, and in some cases, individual optimal positions may converge to positions outside the current global optimal position goodb X. Based on the above principle, the mathematical formula for position update is:
Results show  
Detailed tests were conducted in this period on CEC2005, CEC2017, CEC2021, and CEC2022. The test results are as follows:
CEC2005: Here are a few classic test functions directly.
CEC2017:
CEC2021:
CEC2022:
It can be seen that whether it is from whichever test set of CEC, the improved myxozoan algorithm performs quite well!
Code Exhibits  
Below is the MATLAB code for the myxozoan algorithm.
% Mucoid Optimization Algorithm (SMA)\n% max_iter: maximum number of iterations, N: population size, convergence curve: convergence curve.\nfunction [Destination_fitness,bestPositions,Convergence_curve]=SMA(N,Max_iter,lb,ub,dim,fobj)\n​\n%% Initialization location\nbestPositions=zeros(1,dim);\nDestination_fitness=inf;% Change this to -inf to resolve the maximization issue\nAllFitness = inf*ones(N,1);%Record the fitness of all Dictyostelium\nweight = ones(N,dim);% The fitness weight of each Dictyostelium\n%% Initialize random solution set\nX=initialization(N,dim,ub,lb);\nConvergence_curve=zeros(1,Max_iter);\nit=1;  % Iteration count\nlb=ones(1,dim).*lb; % Lower limit of variable\nub=ones(1,dim).*ub; % Variable Limit\nz=0.03; % Parameters\n​\n%% Main loop\nwhile  it <= Max_iter\n    \n    %=====Adaptive Sorting ========\n    for i=1:N\n        % Check if the solution is out of search space and bring it back\n        Flag4ub=X(i,:)>ub;\n        Flag4lb=X(i,:)<lb;\n        X(i,:)=(X(i,:).*(~(Flag4ub+Flag4lb)))+ub.*Flag4ub+lb.*Flag4lb;\n        AllFitness(i) = fobj(X(i,:));\n    end\n    \n    [SmellOrder,SmellIndex] = sort(AllFitness); \n    worstFitness = SmellOrder(N);\n    bestFitness = SmellOrder(1);\n​\n    S=bestFitness-worstFitness+eps;  % Add eps to avoid a denominator of zero\n​\n    %==== Calculating the fitness weight of each myxozoan ========\n    for i=1:N\n        for j=1:dim\n            if i<=(N/2) \n                weight(SmellIndex(i),j) = 1+rand()*log10((bestFitness-SmellOrder(i))/(S)+1);\n            else\n                weight(SmellIndex(i),j) = 1-rand()*log10((bestFitness-SmellOrder(i))/(S)+1);\n            end\n        end\n    end\n    \n    %====Update the best fitness value and best position=====\n    if bestFitness < Destination_fitness\n        bestPositions=X(SmellIndex(1),:);\n        Destination_fitness = bestFitness;\n    end\n    \n    a = atanh(-(it/Max_iter)+1);  \n    b = 1-it/Max_iter;\n    \n    %====Update the location of search agents=====\n    for i=1:N\n        if rand<z    \n            X(i,:) = (ub-lb)*rand+lb;\n        else\n            p =tanh(abs(AllFitness(i)-Destination_fitness)); \n            vb = unifrnd(-a,a,1,dim); \n            vc = unifrnd(-b,b,1,dim);\n            for j=1:dim\n                r = rand();\n                A = randi([1,N]);  % Randomly select two positions from the overall\n                B = randi([1,N]);\n                if r<p    \n                    X(i,j) = bestPositions(j)+ vb(j)*(weight(i,j)*X(A,j)-X(B,j));\n                else\n                    X(i,j) = vc(j)*X(i,j);\n                end\n            end\n        end\n    end\n    Convergence_curve(it)=Destination_fitness;\n     % display(['At iteration ', num2str(it), ' the best solution fitness is ', num2str(Destination_fitness)]);\n    it=it+1;   \nend\nend\n  

## Images

![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](01.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](02.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](03.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](04.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](05.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](06.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](07.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](08.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](09.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](10.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](11.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](12.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](13.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](14.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](15.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](16.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](17.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](18.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](19.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](20.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](21.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](22.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](23.jpg)
![Adaptive t-distribution mutation swarm optimization algorithm, MATLAB code](24.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
