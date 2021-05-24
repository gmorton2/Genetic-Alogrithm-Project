• Class number
	481-03

• Project number and name
	Project 2: GA + ANN

• Team name and members
	The A Team:
		Garinn Morton II
		Kenny Giang
		Gabriel Magallanes

• Intro (including the algorithm used)
	
	In this project we will seed a Genetic Algorithm (GA) with a population of Multi-Layer Perceptrons (MLP).  Then, rather than use backpropagation learning for a single MLP with a fixed node layout, we will use the GA mating and survival mechanism to attempt to learn a good MLP architecture over several generations of MLP "critters".
	To do this, we will judge the fitness of individual MLP critters through a set of pre-classed Feature vectors.  These fitness results will help determine which of the current generation of critters should mate to generate new kids for the next generation, and which older critters should be purged from the next generation.

• Contents: Files in the .zip submission
	-Project.el
	-README.txt
	-Output.pdf

• External Requirements
	'cl-lib'

• Setup and Installation (if any)
	N/A

• Features (both included and missing)
	Global Variables:
		'data'
		'test'

	Fucntions:
		(defun run-critter (rx-vec rcritter))
		(defun make-easy-crit-wgts (rwgts))
		(defun calc-h-vec (rx-vec rzh-wgt-pairs))
		(defun get-h-in-wgts-fm-pair (rzh-in-pair))
		(defun calc-swixie (rA-vec rB-vec))
		(defun calc-hj-out (rhj-swixie))
		(defun get-y-in-wgts-fm-pair (rzy-in-pair))
		(defun calc-yj-out (rx-vec rzh-wgt-pairs rzy-wgt-pairs))
		(defun calc-y-vec (rh-vec rzy-wgt-pairs))
		(defun get-dist-twx-2-vecs (rveca rvecb))
		(defun ga-doit (rgen-max rgen-1))
		(defun do-gen-stats (gen))
		(defun ga-do-gen (rgen))
		(defun get-critters (rgen))
		(defun get-max-fit (old-critters))
		(defun sort-old-critters(old-critters))
		(defun split (list count))
		(defun split-crits-by-win-lose (old-critters))
		(defun get-winner-crits (winners-losers))
		(defun get-loser-crits (winners-losers))
		(defun swap (list elem1 elem2))
		(defun shuffle (list))
		(defun mate-crits (winner-crits))
		(defun get-class-num (sample-x))
		(defun class-num-to-vector(class-x))
		(defun get-critter-fm-sample-x (sample-x))
		(defun calc-fitness (sample-x rcritter))
		(defun add-fit-to-crit (sample-x rcritter))
		(defun mate-two-critters (rcrit-a rcrit-b))
		(defun get-critter-wgts (rcritter))
		(defun get-wgts-split-pair (wgts crossover-pt))
		(defun get-random-wgt-in-range ())
		(defun mutate-wgts (k-wgts))
		(defun make-kid-critter-w-wgts (k-wgts-mut))
		(defun get-random-wgts-pt (rlen))

• Bugs (if any)
	N/A
	
Project 2 — GA + ANN
Introduction
	You will recall the lecture discussions of Genetic Algorithms (GAs) and “Feed-Forward” MultiLayer Perceptrons (MLPs), one of the variants of Artificial Neural Networks (ANNs). In this project we will seed a GA with a population of MLPs. Then, rather than use backpropagation learning for a 
single MLP with a fixed node layout, we will use the GA mating and survival mechanism to attempt to 
learn a good MLP architecture over several generations of MLP "critters".
	To do this, we will judge the fitness of individual MLP critters through a set of pre-classed Feature 
vectors. These fitness results will help determine which of the current generation of critters should 
mate to generate new kids for the next generation, and which older critters should be purged from the 
next generation.

Critter Fitness, Training Vectors, & Epochs
	To determine the fitness of an MLP critter for the current generation, the critter is checked against 
each vector in the training set. For each one of the pre-classed Feature training vectors; the critter's 
MLP representation is run with that (X) vector as input, and the "distance" of critter's (Y) output from 
the (C) class value of the training vector is used as the fitness (smaller is better). Note that here, 
distance is non-negative -- an absolute value. EX: (abs -5) = 5.
	A critter's fitness is the distance between the two vectors C and Y, treating them as 3D points. For 
the Manhattan (AKA Taxicab) distance (AKA the L1 Norm), this is the sum of the absolutes of the 
coordinate differences. You can instead use the Euclidean distance (AKA the L2 Norm). (There is 
even an L-infinity Norm, which is the maximum coordinate difference.)
	For the critters in the current generation's population, the fitness of each is computed by "running" 
that critter against all the training vectors in the set. Thus, one generation is an epoch.

Critter DNA
	There are many ways to represent a critter's DNA, but it is usually easier if the representation needs 
little or no housekeeping fixups to ensure that a new kid critter is well-formed (AKA legal). To that 
end, the DNA portion of a critter should just be a list of link weights.
	Once the number of inputs (training (X) vector length) and the number of outputs (Y vector length) 
is known, then for a single hidden layer (H) node in the MLP, that node will need size-X (#X) X-to-H 
input link weights (one for each X feature/coordinate value) and that same H node will need size-Y 
(#Y) outputs (an output H-to-Y link weight for each Y node/class candidate). NB, as we will have 
more than a binary classification, we will use one Y node for each class, and it will output a value 
indicating the MLP critter's "belief" that that class is correct for the input vector (X).
	Thus, the number of weights (#W) for an MLP critter is (#X * #H) + (#H * #Y).
				#W = (#X * #H) + (#H * #Y)
				
	If there are 10 hidden nodes (#H = 10) in the hidden layer, and 3 input features per training vector, 
and 3 possible classes for each wildspace vector, then #W = (3*10) + (10*3) = 6*10 = 60 link weights. 
Note, in this case, that each hidden node HJ (the J-th H-node of the hidden layer) has #HJ = (#X + #Y) 
associated link weights: the input #X links and the output #Y links.
	Therefore, a simple format for a critter's DNA will be one large weights list where, for each hidden 
node (HJ), the weights will be listed in the order of X1-to-HJ, X2-to-HJ, ... XN-to-HJ, HJ-to-Y1, HJ-to-Y2, ... HJ-to-YM. (Here, N=#X and M=#Y.)
So, for a given HJ hidden node, its sub-list of #HJ weights will be all together in this format, X-to-HJ weights first followed by HJ-to-Y weights. And 
then we merely append each hidden node's sub-lists together to form the full MLP list of weights.
	Thus, our critter format will look something like this:
			((:crit crit-419) (:fit 241.73) (:wgts 1 2 3 4 ... ))
Crossover
	Given that a critter's DNA is a simple list, recall that we can do the usual crossover by picking a 
location (a list index) to begin the second DNA sub-string. Thus, for the parents, their DNA becomes A
= A1+A2 and B = B1+B2. So, their two kids will be K1 = A1+B2 and K2 = B1+A2.
	For 2-point crossover, the DNA is treated as a wraparound ring. So the two points are the index of 
the second sub-string and the index of the first sub-string (which may wraparound).
	Notice that if a crosspver point is a multiple of #HJ, then it is at the start of a hidden node's weights. 
Otherwise, it will split up a hidden node's weights -- the first part will go to one kid and the second part
to the other kid.
	We are attempting to learn a good number of hidden layer nodes to use. To this end, we may have 
more hidden nodes in one of the parents. To accomodate this length difference, we can choose a 
random crossover point using the minimum of the DNA lengths of the two parents. And once this is 
done, both kids will have a full set of weights (#HJ), one for each parent length. So mixed-length 
parents is not an issue in need of fixup.

Mutation
	Given that we are attempting to learn both a good set of weight values and also a good number of 
hidden nodes, we should use two types of mutations. The first kind is simple, a percentage of the time, 
one or more of the kid's DNA weights should be altered. As a weight, W, might go up or down, and it 
is not limited to a particular range, we will adjust the weight by picking a random number, Z, in the 
range R= (2*(ceiling (abs W)) + 1), and then adjust the weight as follows: W += Z - R/2.0. (If you 
give (random) a negative or float, you usually get a big integer result – hence the (abs).) For example,

W = 2, R = 5, Z = 5, W = 2 - 2.5 + 5 = 4.5
W = 2, R = 5, Z = 3, W = 2 - 2.5 + 3 = 2.5
W = 2, R = 5, Z = 0, W = 2 - 2.5 + 0 = -0.5
;; EX: using random, ceiling, & abs.
(let* ((wgt -2.5)
 (iwgt (ceiling (abs wgt))))
 (list iwgt (random iwgt)))
 
	The R/2.0 allows half the random values to be negative. The "+1" ensures that if W = 0, then we 
still get a non-trivial mutation value.
	Now, we also want to be able to alter a kid's number of hidden nodes by mutation. This mutation 
rate likely should be smaller than that for weights. Typically, for weights, we would expect one weight 
mutated in a kid for every 3 to 10 kids generated -- something low. For adding a full hidden node's 
worth of weights (to the end of the DNA string), or for subtracting one of the existing hidden node's 
weights, likely we might expect this should happen to a kid once for every 20 to 40 kids. We can pick 
the new (#X + #Y) "random" weights for this new node by, say duplicating a random node's weights in 
the kid and then running a single-weight mutation (as above) for each of those duplicated weights.

Training Set
	For our project we will fix #X = 3 and #Y = 3. The training data is shown below, at the end. Call 
the features P, Q, and R. The wildspace constrains the P and Q values to the range -5..5. And R is 
constrained to -40..40. These are float numbers, not integers -- though the training set only has integer 
P and Q values. The three class values are 1, 2 and 3. We can assume we only have to deal with this 
specific set of values.
	Thus, #HJ = #X + #Y = 6. That is, each hidden node will have 6 link weights, including any new 
(via mutation) hidden nodes in a critter's MLP architecture. We will generate and distribute a test set
of vectors for the winning critters later during the project.

Initial MLP Hidden Layer
	The initial number of hidden nodes should be at least 4, but this is likely not enough to create 
(through GA learning) a good architecture. We recommend 6 H nodes to start. The initial weight 
values can be any float values, but staying within the range -5..5 is likely a good idea.

Population Size
	Typically, a GA population is kept the same from one generation to the next. An initial number of 
100 critters is a reasonable start. Note that there will be a lot of computation going on just to get to the 
next generation to the next. By the same token, the population has to be large enough to avoid genetic 
"drift" where useful sub-strings of DNA become lost (due to not enough kids to keep them in the gene 
pool).

Output
	Keep track of the two fittest critters for each epoch, and the average fitness of the population for 
each epoch. When you are close enough to “declare a win”, prepare a list (in a pdf file) of these in age 
order (newest epoch winner first). Also, include a list of the parameters (eg, population size, mutation 
rates, 1-pt or 2-pt crossover, etc.) that you used. And, create a graph (X=epoch, Y=fitness, plot points 
connected by line segments for each of the curves top fitest, next fitest, and average fitness).






