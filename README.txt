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






