# Cogent

[![Crates.io](https://img.shields.io/crates/v/cogent)](https://crates.io/crates/cogent)
[![lib.rs.io](https://img.shields.io/crates/v/cogent?color=blue&label=lib.rs)](https://lib.rs/crates/cogent)
[![docs](https://img.shields.io/crates/v/cogent?color=yellow&label=docs)](https://docs.rs/cogent)

## A note

I continue working on GPU stuff, I've made some interesting things there, but ultimately it made me realise this is far too monumental a task, I am not Nvidia, I am not Google, I'm just some random sole dev. Right now I'm working on other things and while I would love to continue this project, I think honestly, it's not a good use of time. I could not feasibly make this project good enough to be worth using professionally.

So for now, this project is no longer in active development.

(I still think it's kinda neat so don't let me put you off playing around with it, it's still really fast and easy to use)

## Introduction

Cogent is a very basic library for training basic neural networks for classification tasks.
It is designed to be as simple as feasible.
Hyperparameters in neural network training can be set automatically, so why not?
Ideally you could simply do:

```rust
let net = NeuralNetwork::Train(&data);
```

This is the most basic and not quite there yet implementation of that idea.

Training a network to classify MNIST:

```rust
// Uses
use cogent::{
    NeuralNetwork,
    EvaluationData,MeasuredCondition
};
use ndarray::{Array2,Axis};

// Setup
// ----------
// 784-ReLU->800-Softmax->10
let mut net = NeuralNetwork::new(784,&[
    Layer::Dense(800,Activation::ReLU),
    Layer::Dense(10,Activation::Softmax)
]);

// Setting training and testing data.
// `get_mnist_dataset(bool)` simply gets MNIST dataset.
// The boolean specifies if it is the MNIST testing data (`true`) or training data (`false`).

// Sets training and testing data.
let (mut train_data,mut train_labels):(Array2<f32>,Array2<usize>) = get_mnist_dataset(false);
let (test_data,test_labels):(Array2<f32>,Array2<usize>) = get_mnist_dataset(true);

// Execution
// ----------
// Trains until no notable accuracy improvements are being made over a number of iterations.
// By default this would end training if 0.5% accuracy improvement was not seen over 12 iterations/epochs.

net.train(&mut train_data,&mut train_labels)
    .evaluation_data(EvaluationData::Actual(&test_data,&test_labels)) // Sets evaluation data
    .l2(0.1) // Implements L2 regularisation with a 0.1 lambda vlaue
    .tracking() // Prints backpropgation progress within each iteration
    .log_interval(MeasuredCondition::Iteration(1)) // Prints evaluation after each iteration
    .go();

// If evaluation data is not set manually it will simply shuffle and split off a random group from training data to be evaluation data.
// In the case of MNIST where training and evaluation datasets are given seperately, it makes sense to set it as such.

// Evaluation
// ----------
let (cost,correctly_classified):(f32,u32) = net.evaluate(&test_data,&test_labels,None); // (cost,examples correctly classified)
println!("Cost: {:.2}",cost);
println!(
    "Accuracy: {}/{} ({:.2}%)",
    correctly_classified,
    test_data.len_of(Axis(1)),
    correctly_classified as f32 / test_data.len_of(Axis(1)) as f32
);
```

While a huge amount of my work has gone into making this and learning the basics of neural networks along the way, I am immensely (and I cannot stress this enough) amateur in inumerable ways.

If you find any issues I would really appreciate if you could let me know (and possibly suggest any solutions).

## Features

- GPU compute using [ArrayFire Rust Bindings](https://github.com/arrayfire/arrayfire-rust)
- Optimisers: Stochastic gradient descent.
- Layers: Dense, Dropout
- Activations: Softmax, Sigmoid, Tanh, ReLU.
- Loss functions: Mean sqaured error, Cross entropy.
- Misc: L2 regularisation

## Installation

1. [Setup ArrayFire Rust bindings](https://github.com/arrayfire/arrayfire-rust#use-from-cratesio--) (Ignore step 4).
2. Add `cogent = "^0.6"` to `Cargo.toml`.

## TODO ([See note](https://github.com/JonathanWoollett-Light/cogent/blob/master/README.md#a-note))

1. Convolutional layers.
2. Meticulous testing (making sure things work).
3. Optimise usage of VRAM.
4. Meticulous benchmarking (making sure things are fast).
5. Benchmarking against other popular neural network libraries (Keras etc.)
6. Automatic net creation: Bayesian optimisation of everything.

Please note that things may not be developed inorder, it is only my estimation.
