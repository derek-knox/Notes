# Neural Networks and Deep Learning by Michael Nielsen

## Intro

- *Neural network* - biologically inspired programming paradigm enabling a computer to learn through observation
- *Deep learning* - techniques for learning in neural networks

## Chapter 1 - Using neural nets to recognize handwritten digits

- Neural networks leverage supplied *training examples* against a programmed system that learns from the examples by inferring rules for pattern recognition.
- *Σ* - sigma (sum) - lowercase is `σ`
- *≡* - xi (identity) - both sides are true for all values
- Artificial neurons
    - *perceptron* - an artificial neuron with x binary inputs and 1 binary output. A *weight* is a real number associated with each input where the *weighted sum (Σ)* meets a real number *threshold* value to determine the output.
        - Varying the weights and threshold result in different *decision making models*
        - Layering sets of perceptrons enables abstract and sophisticated decision making as the outputs of a perceptron may be an input in any number of perceptrons in a subsequent layer
        - Forumla (`w` = weight vector, `x` = input vector, `b` = bias)
            - `output = w⋅x+b > 0 ? 1 : 0`
            - *bias* - measue of ease for perceptron to fire
        - Can mimic NAND gate and thus a neural network can mimic circuits and achieve computational universality. Better than conventional circuit design however, *learning algorithms* can automatically tune weights and biases of a neural network resulting in a learned and potentially superior design.
    - *sigmoid* - an artificial neuron with x unit fraction inputs and 1 unit fraction output. A *weight* is a real number associated with each input where the *weighted sum (Σ)* meets a real number *threshold* value to determine the output.
        - AKA - logistic neuron
        - Formula (`σ` = sigmoid function, `w` = weight vector, `x` = input vector, `b` = bias)
            - `z = w⋅x+b`
            - `output = σ(z)≡1/1+e^−z`
