% Initializing distance matrix
distances = xlsread('C:\Users\Abdou\Desktop\distances'); %Veuillez mettre le chemin correspondant au fichier distance
                                                         %sur votre ordinateur
distances = distances(:, [4 14 16 22]);
vec_distances = reshape(distances,88,1);

% Defining global variables
epsilon = 0; %Choose the value of epsilon to apply the linear combination model: the bigger is epsilon the more 
             %weight we give to disruption
a = 0.2; %Distance fairness
b = 0.3;
alpha = 0.8; %Workload fairness
beta = 1.2;

% Initializing past assignments matrix
past = zeros(22,4);
past([4 5 6 7 8 15],1)=1;
past([10 11 12 13 14],2)=1;
past([9 16 17 18],3)=1;
past([1 2 3 19 20 21 22],4)=1;
vec_past = reshape(past,88,1);

% Initializing index values matrix
index11 = [0.1609 0.1164 0.1026 0.1516 0.0939 0.1320 0.0687 0.0930 0.2116 0.2529 0.0868];
index22 = [0.0828 0.0975 0.8177 0.4115 0.3795 0.0710 0.0427 0.1043 0.0997 0.1698 0.2531];
s_index = [index11, index22];
index = repmat([index11, index22]',1,4);
vec_index = reshape(index,88,1);

% Defining cost matrix
cost_matrix = zeros(1, 88);
for i = 1:88
    cost_matrix(i) = vec_distances(i)+epsilon*vec_index(i)/2*(1-2*vec_past(i));
end

% Validation of cost matrix and past assignments
%cost_matrix*vec_past;              % answer = 187.41

% Defining left-hand equality matrix
upperE = zeros(4,88);
upperE(1,4)=1;
upperE(2, 22+14)=1;
upperE(3, 2*22+16)=1;
upperE(4, 3*22+22)=1;
lowerE = repmat(eye(22),1,4);
E = [upperE; lowerE];

% Defining right-hand equality matrix
e = ones(26,1);

% Defining left-hand inequality matrix
I1 = zeros(4,88);
I1(1, 1:22) = s_index;
I1(2, 22+1:2*22) = s_index;
I1(3, 2*22+1:3*22) = s_index;
I1(4, 3*22+1:4*22) = s_index;
I2 = -I1;

I3 = repmat(-b*vec_distances,1,4)';
I3(1, 1:22)        = I3(1, 1:22)        + distances(:,1)';
I3(2, 22+1:2*22)   = I3(2, 22+1:2*22)   + distances(:,2)';
I3(3, 2*22+1:3*22) = I3(3, 2*22+1:3*22) + distances(:,3)';
I3(4, 3*22+1:4*22) = I3(4, 3*22+1:4*22) + distances(:,4)';
I3;

I4 = repmat(a*vec_distances,1,4)';
I4(1, 1:22)        = I4(1, 1:22)        - distances(:,1)';
I4(2, 22+1:2*22)   = I4(2, 22+1:2*22)   - distances(:,2)';
I4(3, 2*22+1:3*22) = I4(3, 2*22+1:3*22) - distances(:,3)';
I4(4, 3*22+1:4*22) = I4(4, 3*22+1:4*22) - distances(:,4)';
I4;

I = [I1; I2; I3; I4];
size(I);

% Defining right-hand inequality matrix
i1 = beta*ones(4,1);
i2 = -alpha*ones(4,1);
i3 = zeros(2*4,1);

i = [i1; i2; i3];
size(i);


%-------------------------------------------------------------------------
% Resolving the problem
options = optimoptions('intlinprog', 'RelativeGapTolerance', 10^(-20), 'ObjectiveImprovementThreshold', 10^(-20));
[optimal_x,optimal_f,exitflag2,output2]= intlinprog(cost_matrix, 1:88, I, i, E, e, zeros(4*22,1), ones(4*22,1), options);
solution_reshaped = reshape(optimal_x, 22, 4);
size(solution_reshaped);


disp('Distance totale');
disp(optimal_f);

disruption = 0.5*abs(optimal_x' - vec_past')*vec_index;
disp('Disruption');
disp(disruption);

alt_sol = zeros(22,23);
alt_sol(4,23)=1;
alt_sol(4, [3 4 5 6 7 9 19 20])=1;
alt_sol(14, [12 14 17 21])=1;
alt_sol(16, [2 8 10 11 13 16 18])=1;
alt_sol(22, [1 15 22])=1;
alt_sol(14,23)=1;
alt_sol(16,23)=1;
alt_sol(22,23)=1;
alt_sol = reshape(alt_sol', 22*23, 1);

alt_sol2 = zeros(22,4);
alt_sol2([2 4 5 6 7 19 21],1)=1;
alt_sol2([3 8 14 18 20],2)=1;
alt_sol2([9 10 11 12 13 16 17],3)=1;
alt_sol2([1 15 22],4)=1;
alt_sol2 = reshape(alt_sol2, 22*4, 1);

y=size(I);
u=size(E);
sum(I*alt_sol2<=i)==y(1);
sum(E*alt_sol2==e)==u(1);
