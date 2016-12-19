# BoltzmannMachines.jl

This Julia package implements algorithms for training and evaluating several types of Boltzmann Machines (BMs):

* Learning of Restricted Boltzmann Machines (RBMs) using Contrastive Divergence (CD)
* Greedy layerwise pre-training of Deep Boltzmann Machines (DBMs)
* Learning procedure for general Boltzmann Machines using mean-field inference and stochastic approximation. Applicable to DBMs and used for fine-tuning the weights after the pre-training
* Exact calculation of the likelihood of BMs (only suitable for small models)
* Annealed Importance Sampling (AIS) for estimating the likelihood of larger BMs

## Types of Boltzmann Machines

The package contains the following types of RBMs:

Type                    | Distribution of visible units    | Distribution of hidden units
------------------------|----------------------------------|-----------------------------
`BernoulliRBM`          | Bernoulli                        | Bernoulli
`GaussianBernoulliRBM`  | Gaussian                         | Bernoulli
`Binomial2BernoulliRBM` | Binomial distribution with n = 2 | Bernoulli
`BernoulliGaussianRBM`  | Bernoulli                        | Gaussian

It also contains the type `BasicDBM`, encapsulating the parameters of a DBM with Bernoulli-distributed units. (In the next release, it is planned to provide Multimodal DBMs.)


## Overview of functions

The following tables provide an overview of the functions of the package, together with a short description. You can find more detailed descriptions for each function using the Julia help mode (entered by typing `?` at the beginning of the Julia command prompt).

### Functions for Training

#### Training of RBMs

Function name    | Short description
---------------- | -----------------
`initrbm`        | Initializes an RBM model.
`trainrbm!`      | Performs CD-learning on an RBM model.
`fitrbm`         | Fits a RBM model to a dataset using CD. (Wraps `initrbm` and `trainrbm!`)
`samplevisible` (`samplehidden`) | Gibbs sampling of visible (hidden) nodes' states given the hidden (visible) nodes' states in an RBM.
`visiblepotential` (`hiddenpotential`) | Computes the deterministic potential for the activation of the visible (hidden) nodes of an RBM.
`visibleinput` (`hiddeninput`) | Computes the total input received by the visible (hidden) layer of an RBM.


#### Training of DBMs

Function name    | Short description
---------------- | -----------------
`addlayer!`      | Adds an additional layer of nodes to a DBM and pre-trains the new weights.
`fitdbm`         | Fits a DBM model to a dataset. This includes pre-training, followed by the general Boltzmann Machine learning procedure for fine-tuning.
`gibbssample!`   | Performs Gibbs sampling in a DBM.
`meanfield`      | Computes the mean-field inference of the hidden nodes' activations in a DBM.
`stackrbms`      | Greedy layerwise pre-training of a DBM model or a Deep Belief Network.
`traindbm!`      | Trains a DBM using the learning procedure for a general Boltzmann Machine.


### Partitioned training and joining of models

The following functions may be used to join models fitted on partitioned data sets. The weights cross-linking the models are initialized with zeros.

Function name | Short description
--------------|------------------
`joindbms`    | Joins two or more DBM models together.
`joinrbms`    | Joins two or more RBM models to form a joint RBM model of the same type.


### Functions for evaluating a trained model

Function name          | Short description
--------------         | -----------------
`aisimportanceweights` | Performs AIS on a BM and calculates the importance weights for estimating the BM's partition function.
`freeenergy`           | Computes the mean free energy of a data set in an RBM model.
`loglikelihood`        | Estimates the mean loglikelihood of a dataset in a BM model using AIS.
`logpartitionfunction` | Estimates the log of the partition function of a BM. 
`logproblowerbound`    | Estimates the mean lower bound of the log probability of a dataset in a DBM model.
`reconstructionerror`  | Computes the mean reconstruction error of a dataset in an RBM model.
`sampleparticles`      | Generates samples from the distribution defined by a BM model.


### Monitoring the learning process

The functions of the form `monitor*!` can be used for monitoring a property of the model during the learning process.
The following words, corresponding to the denominated properties, may stand in place of `*`: 

* `freeenergy`
* `exactloglikelihood`
* `loglikelihood`
* `logproblowerbound`
* `reconstructionerror`
* `weightsnorm`

The results of evaluations are stored in `Monitor` objects. The evaluations can be plotted by calling the function `plotevaluation` in the submodule `BMPlots` as `BMPlots.plotevaluation(monitor, key)`, with the key being one of the constants `monitor*` defined in the module.

For intended usage of these functions, best see the [examples](test/examples.jl).

## Examples

Prerequisite for running the [example code here](test/examples.jl) is that the `BoltzmannMachines` package is installed:

    Pkg.add("BoltzmannMachines")
    
If you want to use the plotting functionality in the submodule `BMPlots`, you are also required to have the Julia package [Gadfly](http://gadflyjl.org/stable/) installed.


<!--TODO: Two ways, fitdbm or addlayer! and traindbm!
 Small dbm, exact, big dbm loglikelihood am Schlus, logproblowerbound w�hrend training, alle 2 Schritte.
Partitioned Training-->


## References

[1] Salakhutdinov, R. (2015). *Learning Deep Generative Models*. Annual Review of Statistics and Its Application, 2, 361-385.

[2] Salakhutdinov, R. Hinton, G. (2012). *An Efficient Learning Procedure for Deep Boltzmann Machines*. Neural computation, 24(8), 1967-2006.
 
[3] Salakhutdinov. R. (2008). *Learning and Evaluating Boltzmann Machines*. Technical Report UTML TR 2008-002, Department of Computer Science, University of Toronto.

[4] Krizhevsky, A., Hinton, G. (2009). *Learning Multiple Layers of Features from Tiny Images*.

[5] Srivastava, N., Salakhutdinov R. (2014). *Multimodal Learning with Deep Boltzmann Machines*. Journal of Machine Learning Research, 15, 2949-2980.

