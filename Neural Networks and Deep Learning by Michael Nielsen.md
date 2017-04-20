# Neural Networks and Deep Learning by Michael Nielsen

## Intro

- *Neural network* - biologically inspired programming paradigm enabling a computer to learn through observation
- *Deep learning* - techniques for learning in neural networks

## Chapter 1 - Using neural nets to recognize handwritten digits

- Neural networks leverage supplied *training examples* against a programmed system that learns from the examples by inferring rules for pattern recognition.
- common formulaic symbols
    - *Σ* - sum (sigma uppercase)
        - *σ* - sigmoid (sigma lowercase)
    - *≡* - equivalence
    - *∂* - partial derivative
    - *η* - learning rate
    - *Δ* - delta
        - *δ* - delta error (lowercase)
    - *∇* - gradient (∇C as gradient vector) (nabla pronunication)
    - *ϵ* - epsilon (small quantity)
    - *⊙* - Hadamard product (multiplies two matrices of same dimensions into a 3rd matrix)
    - *λ* - regularization parameter
- artificial neurons
    - *perceptron* - an artificial neuron with n binary inputs and 1 binary output. A *weight* is a real number associated with each input where the *weighted sum (Σ)* meets a real number *threshold* value determining the output.
        - Varying the weights and threshold result in different *decision making models*
        - Layering sets of perceptrons enables abstract and sophisticated decision making as the outputs of a perceptron may be an input in any number of perceptrons in a subsequent layer
        - Forumla (`w` = weight vector, `x` = input vector, `b` = bias)
            - `output = w⋅x+b > 0 ? 1 : 0`
            - *bias* - measue of ease for perceptron to fire
        - Can mimic NAND gate and thus a neural network can mimic circuits and achieve computational universality. Better than conventional circuit design however, *learning algorithms* can automatically tune weights and biases of a neural network resulting in a learned and potentially superior design.
    - *sigmoid* - an artificial neuron with n inputs and 1 output where each input and output is 0, 1, or a fraction between. A *weight* is a real number associated with each input where the *weighted sum (Σ)* meets a real number *threshold* value determining the output.
        - AKA - logistic neuron
        - Formula (`σ` = sigmoid function, `w` = weight vector, `x` = input vector, `b` = bias)
            - `z = w⋅x+b`
            - `output = σ(z)≡1/1+e^−z`
- *activation function* - defines a neuron's output given a set of input(s) (*linear perceptron* and *sigmoid function* are two examples mentioned above)  
- neural network
    - anatomy
        1. input layer comprised of input neuron(s)
        2. hidden layer(s) comprised of neuron(s) informing the next layer
        3. output layer comprised of output neuron(s)
    - types
        - feedforward (no loops)
        - recurrent (looping allowed where select neuron(s) only fire for a limited duration)
    - *cost function* - quantifies accuracy of the weights and biases of a desired output
    - *gradient descent* - minimization algorithm aiding neural net learning by minimizing the cost function
        - Ball in valley analogy for cost function of two variables 
            - x,z as vars (floor plane) and y as function result (vertical valley depth)
        - By choosing a random point (function variables) an output results. The algorithm determines another point just slightly offset from the original one and compares the two outputs to learn which result is deeper in the valley (using the update rule `v → v′ = v−η∇C`). This informs the algorithm's direction to continue in to find the deepest point in the valley. This is the "minimization" of gradient descent as we seek the global minimum output.
    - The goal of training a neural net is to find weights and biases which minimize the cost function so that inputs not in the training set result in accurate output
    - *stochastic gradient descent* - optimized approach of gradient descent to approximate the gradient by sampling random test inputs in mini-batches
    - *mini-batch* - random sample of inputs from a training set
    - *training epoch* - time to complete a training
    - *online/incremental learning* - gradient descent with a mini-batch of 1 input
    - *hyper-parameters* - exclude the weights and biases and include the epoch count, mini batch size, and learning rate
        - I imagine you could get meta and use an algorithm that learns the best hyper-parameters
    - *shallow neural network* - neural network with one hidden layer
    - *deep neural network* - neural network with two or more hidden layers (layers of abstraction)

## Chapter 2 - How the backpropagation algorithm works

- *backpropagation* - a fast algorithm composed of four equations used to compute the gradient of the cost function
    - Equation descriptions
        - An equation for the error in the output layer
        - An equation for the error `δˡ` in terms of the error in the next layer
        - An equation for the rate of change of the cost with respect to any bias in the network
        - An equation for the rate of change of the cost with respect to any weight in the network
    - "Think of backpropagation as a way of computing the gradient of the cost function by systematically applying the chain rule from multi-variable calculus."
    - "The backpropagation algorithm is a clever way of keeping track of small perturbations to the weights (and biases) as they propagate through the network, reach the output, and then affect the cost"
- *weighted input* - `zˡⱼ` is just the weighted input to the activation function for neuron `j` in layer `l`

## Chapter 3 - Improving the way neural networks learn

- Using the *cross-entropy* cost function instead of the *quadratic* cost function improves the speed of learning changes greatly. Essentially, when initial weight and bias values are far from their respective to-be-determined ideals, the quadratic cost function is slow. Replacing it with cross-entropy resolves this issue.
    - This is true in nets of sigmoid neurons, quadratic cost is fine for nets of perceptron/linear neurons.
    - "Roughly speaking, the idea is that the cross-entropy is a measure of surprise"
- *saturation* - state in which a neuron predominantly outputs values close to the asymptotic ends of the bounded activation function (negatively impacts learning)
- *overfitting* - overtraining a network
- *hold out* - validation data as a type of training data that helps us learn good hyper-parameters that's held out of the normal training data
- *regularization* - techniques to help reduce overfitting
    - weight decay - modification to a cost function in pursuit of small weights
    - dropout - modification to hidden layers by excluding random neuron sets during training
    - artificial training data expansion - duplicate training data and tweak it before appending it to the original training data set (rotating/translating/skewing images for example)
- *weight initialization* - weights input to a neuron are initialized as Gaussian random variables with mean 0 and standard deviation 1 divided by the square root of the number of connections input to the neuron
- *learning rate schedule* - dynamically adjusting the learning rate (as opposed to defining it constant) for improving network performance
- As an author of Neural Networks, it can be very time consuming for a few core reasons:
    1. Determining hyper-parameters that truly work for a given problem can take a lot of time
    2. Computation (in real-time) is high and that is why there are so many tricks/optimization-hacks in an effort to find the ideal hyper-parameters. Improvements in computation enable faster iteration toward finding ideals.

## Chapter 4 - A visual proof that neural nets can compute any function

- *universality theorem* - neural networks with a single hidden layer can be used to approximate any continuous function to any desired precision
    - the single hidden layer is composed of neuron pairs mimicking a *step function* where `s=−b/w`. Each neuron pair is mapped over 0-1 for the entire neuron count. So choosing a large weight in conjunction with the step neuron's 0-1 value results in a calculable bias (`b=−ws`). So the more pairs you have, the more accurate the approximation.
        - Example: 6 neurons as 3 pairs map to [[0,.33],[.33,.66],[.66,1]]
    - "In essence, we use a single-layer neural networks to build a lookup table for the function"
    
## Chapter 5 - Why are deep neural networks hard to train?

- *unstable gradient problem* - "if we use standard gradient-based learning techniques, different layers in the network will tend to learn at wildly different speeds"
    - *vanishing gradient problem* - later layers of deep nets learn faster
        - "...empirically it is typically found in sigmoid networks that gradients vanish exponentially quickly in earlier layers. As a result, learning slows down in those layers. This slowdown isn't merely an accident or an inconvenience: it's a fundamental consequence of the approach we're taking to learning."
    - *exploding gradient problem* - earlier layers of deep nets learn faster
- "...the choice of activation function, the way weights are initialized, and even details of how learning by gradient descent is implemented... choice of network architecture and other hyper-parameters" make it hard to train deep nets

## Chapter 6 - Deep learning

- *convolutional neural network* - a feed-forward net in which the connectivity pattern between its neurons is inspired by the organization of the animal visual cortex (spatial understanding)
    - "convolutional networks are well adapted to the translation invariance of images"
    - Three core ideas:
        - *local receptive field* - each neuron of a hidden layer consumes the input of a region of input neurons where a stride length helps determine how the regions are defined
            - traditional nets in contrast map every single input to each neuron in a subsequent layer
        - *shared weights and biases* - each neuron of a hidden layer shares the same weights (to the region) and bias enabling each hidden layer to detect a particular "feature" or "pattern" of the image as a whole
            - traditional nets in contrast do not share the weight and bias
        - *pooling layers* - layers immidiately succeeding convolutional layers responsible for simplifying the output
    - Convolutional nets have a fraction of the parameters required (due to shared weights and bias) for traditional nets and thus train and perform much faster
    - *feature map* - the input layer to the first hidden layer
    - *filter* - defined by a set of shared weights and bias
    - *convolutional layer* - a layer consisting of 1 or more feature maps
        - **
