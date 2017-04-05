# Neural Networks and Deep Learning by Michael Nielsen

## Intro

- *Neural network* - biologically inspired programming paradigm enabling a computer to learn through observation
- *Deep learning* - techniques for learning in neural networks

## Chapter 1 - Using neural nets to recognize handwritten digits

- Neural networks leverage supplied *training examples* against a programmed system that learns from the examples by inferring rules for pattern recognition.
- common formulaic symbols
    - *Σ* - sigma (sum) - lowercase is `σ`
    - *≡* - xi (identity) - both sides are true for all values
    - *∂* - partial derivative
    - *η* - learning rate
    - *Δ* - delta
    - *∇* - gradient (∇C as gradient vector)
- artificial neurons
    - *perceptron* - an artificial neuron with x binary inputs and 1 binary output. A *weight* is a real number associated with each input where the *weighted sum (Σ)* meets a real number *threshold* value to determine the output.
        - Varying the weights and threshold result in different *decision making models*
        - Layering sets of perceptrons enables abstract and sophisticated decision making as the outputs of a perceptron may be an input in any number of perceptrons in a subsequent layer
        - Forumla (`w` = weight vector, `x` = input vector, `b` = bias)
            - `output = w⋅x+b > 0 ? 1 : 0`
            - *bias* - measue of ease for perceptron to fire
        - Can mimic NAND gate and thus a neural network can mimic circuits and achieve computational universality. Better than conventional circuit design however, *learning algorithms* can automatically tune weights and biases of a neural network resulting in a learned and potentially superior design.
    - *sigmoid* - an artificial neuron with x inputs and 1 output where each input and output is 0, 1, or a fraction between. A *weight* is a real number associated with each input where the *weighted sum (Σ)* meets a real number *threshold* value to determine the output.
        - AKA - logistic neuron
        - Formula (`σ` = sigmoid function, `w` = weight vector, `x` = input vector, `b` = bias)
            - `z = w⋅x+b`
            - `output = σ(z)≡1/1+e^−z`
- *activation function* - defines a node's output given a set of input(s) (*linear perceptron* and *sigmoid function* are two examples mentioned above)  
- neural network
    - anatomy
        1. input layer comprised of input neuron(s)
        2. hidden layer(s) comprised of neuron(s) informing the output layer
        3. output layer comprised of output neuron(s)
    - types
        - feedforward (no loops)
        - recurrent (looping allowed where select neuron(s) only fire for a limited duration)
    - *cost function* - quantifies accuracy of the weights and biases of a desired output
    - *gradient descent* - minimization algorithm aiding learning in neural nets by minimizing the cost function
    - The goal of training a neural net is to find weights and biases which minimize the cost function so that inputs not in the training set provide accurate output

