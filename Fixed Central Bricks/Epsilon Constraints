% Initializing distance matrix
distances = xlsread('C:\Users\Abdou\Desktop\distances');
vec_distances = reshape(distances',22*22,1);

% Defining global variables
epsilon = 10^(-4); % epsilon-Constraint
a = 0.2; %Distance fairness
b = 0.3;
alpha = 0.8; %Workload fairness
beta = 1.2;
distance_first_iteration = 221.8331; %Fix the number of total distance that you have thanks to the first iteration
disruption_first_iteration = 1.66; %Fix the number of disruption that you have thanks to the first iteration
max_iterations = 5;
p = 4; % Number of available SRs

%--------------------------------------------------------------------------
% Data initialization
% Initializing past assignments matrix
past = zeros(22,23);
past(4,[4 5 6 7 8 15 23])=1;
past(14,[10 11 12 13 14 23])=1;
past(16,[9 16 17 18 23])=1;
past(22,[1 2 3 19 20 21 22 23])=1;
vec_past = reshape(past',22*23,1);

% Initializing index values matrix
index11 = [0.1609 0.1164 0.1026 0.1516 0.0939 0.1320 0.0687 0.0930 0.2116 0.2529 0.0868];
index22 = [0.0828 0.0975 0.8177 0.4115 0.3795 0.0710 0.0427 0.1043 0.0997 0.1698 0.2531];
s_index = [index11, index22];
index = [repmat([index11, index22],22,1); zeros(1, 22)];
%vec_index = reshape(index,88,1);


%--------------------------------------------------------------------------
% Defining cost matrix
cost_matrix = zeros(23*22,1);
for i = 1:22
    cost_matrix((23*(i-1)+1):(23*i-1)) = distances(i,:)';
end
cost_matrix;

% Validation of cost matrix and past assignments
%cost_matrix'*vec_past;              % answer = 187.41


%--------------------------------------------------------------------------
% Defining equality matrices
% Setting the number of SR
E1 = zeros(1,23*22);
for i = 1:22
    E1(1, 23*i)=1;
end
e1 = p;

% Each brick should be assigned to only one SR
E2 = repmat([eye(22) zeros(22,1)],1,22);
e2 = ones(22,1);

% Each SR with central brick i should be assigned brick i 
E3 = zeros(22, 23*22);
for i = 1:22
   E3(i, 23*(i-1)+i)=-1;
   E3(i, 23*(i-1)+23)=1;
end
e3 = zeros(22,1);

% Setting 4,14,16 and 22 as central bricks
E4 = zeros(1,22*23);
E4(23*4)=1;
E5 = zeros(1,22*23);
E5(23*16)=1;
E6 = zeros(1,22*23);
E6(23*14)=1;
E7 = zeros(1,22*23);
E7(23*22)=1;

%Reassembling left-hand equality matrix
E = [E1; E2; E3; E4; E5; E6; E7];

%Reassembling right-hand equality matrix
e = [e1; e2; e3; 1; 1; 1; 1];


%--------------------------------------------------------------------------
% Defining inequality matrices
% Right-hand term for workload fairness ('beta')
I1 = zeros(22,23*22);
for i = 1:22
   I1(i, (23*(i-1)+1):(23*i-1)) = s_index; 
end
i1 = beta*ones(22,1);

% Left-hand term for workload fairness ('alpha')
I2 = zeros(22,23*22);
for i = 1:22
   I2(i, (23*(i-1)+1):(23*i-1)) = -s_index; 
   I2(i, 23*i)=1;
end
i2 = (-alpha+1)*ones(22,1);

% Nullity of rows that are not considered as central bricks
I5 = zeros(22,22*23);
for i = 1:22
   I5(i,(23*(i-1)+1):(23*i-1))= ones(22,1);
   I5(i, 23*i)=-24;
end
i5 = zeros(22,1);

% Right-hand term for distance fairness ('b')
I6 = zeros(22,22*23);
for i = 1:22
    I6(i,(23*(i-1)+1):(23*i-1)) = distances(i,:);
    I6(i,:) = I6(i,:) - b*cost_matrix';  
end
i6 = zeros(22,1);

% Left-hand term for workload fairness ('a')
I7 = zeros(22,22*23);
for i = 1:22
   I7(i,(23*(i-1)+1):(23*i-1)) = - distances(i,:);
   I7(i,:) = I7(i,:) + a*cost_matrix';
   I7(i, 23*i)=I7(i, 23*i)+(4*10^3);
end
i7 = (4*10^3)*ones(22,1);

% Initializing arrays to stock results
Total_distances = [distance_first_iteration];
Disruptions = [disruption_first_iteration];
iterations = [1];
disruption = disruption_first_iteration;
for j = 1:max_iterations
    
    % Epsilon contraint for disruption
    I8 = zeros(1, 23*22);
    for k = 1:22
        I8((23*(k-1)+1):(23*k)) = 1/2*[s_index 0].*(1-2*past(k,:));
    end
    i8 = disruption - 2 - epsilon;

    % Reassembling left-hand inequality matrix
    I = [I1; I2; I5; I6; I7; I8];

    % Reassembling right-hand inequality matrix
    i = [i1; i2; i5; i6; i7; i8];


    %--------------------------------------------------------------------------
    % Resolving the problem
    options = optimoptions('intlinprog', 'RelativeGapTolerance', 10^(-20), 'ObjectiveImprovementThreshold', 10^(-20));
    [optimal_x,optimal_f] = intlinprog(cost_matrix, 1:506, I, i, E, e, zeros(23*22,1), ones(23*22,1), options); 
                % The 7-th and 8-th are a variant stating that our variables
                % are greater than 0 and lower than 1
    solution_reshaped = reshape(optimal_x, 23, 22)';
    optimal_f;
    disruption = I8*optimal_x + 2;
    %--------------------------------------------------------------------------
    % Results storage
    Total_distances = [Total_distances, optimal_f];
    Disruptions = [Disruptions, disruption];
    iterations = [iterations, j+1];

end

scatter(Total_distances,Disruptions,[],'filled')
