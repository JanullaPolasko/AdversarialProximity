# Adversarial Attack Visualization

This repository provides tools to train neural networks (FC, CONV, ResNet), generate adversarial examples (L0, L1, L2, Linf), and visualize how these attacks influence the internal behavior of neural networks using methods such as KNN counting and manifold proximity analysis.

## Features

- **Neural Network Training**: Train different types of neural networks including Fully Connected (FC), Convolutional (Conv), and ResNet architectures.
- **Adversarial Attacks**: Implement attacks such as L0, L1, L2, and Linf to evaluate the robustness of trained models.
- **Impact Visualization**: Use KNN counting and manifold proximity methods to visualize how adversarial attacks affect model predictions.

## Requirements

- Python 3.9 - 3.11
- PyTorch (if gpu is avaible use cuda version)
- NumPy
- Matplotlib 
- Pytorch Lighting
- [Adversarial Robustness Toolbox (ART)](https://github.com/Trusted-AI/adversarial-robustness-toolbox)  
- tqdm  
- pickkle
- gc

## Installation

Clone the repository to your local machine:
`git clone https://github.com/JanullaPolasko/Adversarial-Attack-visualization.git `

## Theory Behind the Methods

The analysis is based on two complementary techniques that help explain how adversarial examples propagate through a neural network.

The first method examines the nearest neighbors of adversarial examples within the activation space of each layer. By comparing how many of these neighbors belong to the original versus the predicted (incorrect) class, we can observe how the identity of the example shifts as it moves through the network. This technique gives an intuitive and statistical measure of how adversarial perturbations affect the internal representation of data.

The second method focuses on measuring distances from adversarial examples to class-specific manifolds, which are approximated using convex combinations of nearby training samples. By projecting the adversarial example onto these manifolds, we can determine whether it still lies close to its original class or has moved toward the predicted class. This geometric view provides a more continuous and structure-aware way of tracking how adversarial inputs diverge from expected behavior.

##  Usage

To use the repository, begin by editing the `get_dataset_mapping()` function in `network.py`. Here, you need to specify the model's name, its class, and the number of output classes. This function is called across all scripts to automatically locate the necessary components for the given configuration.

Next, either add your own neural network class to `network.py` or select one of the predefined models already available. If your model is pretrained using PyTorch, set the last parameter in `get_dataset_mapping()` to `True`; otherwise, set it to `False`.

### Saving
To control where models, adversarial examples, datasets, and visualization outputs are stored, adjust the paths in `datapath.py`.

###  0. Datasets

The project supports four datasets: SVHN, MNIST, CIFAR-10, and Fashion-MNIST. These are defined in `datasets.py`, where they are loaded, normalized, and prepared for training and evaluation.

###  1. Training

Training is handled by the `train.py` script and is implemented using the PyTorch Lightning library. The script will train model in  `get_dataset_mapping()`, save it into a subfolder named `/network` inside the defined `datapath()`, and plot the training progress using the `compute_dev_plot()` function from `visualization_utils.py`. To improve reliability the training process is repeated five times by default (this can be configured via the `run` parameter), with each model saved under a different identifier.

### 2. Crafting Adversarial Examples

To generate adversarial examples, configure the `run_adversarial_attacks()` function inside the `generate_adversarial.py` script. Here, you can specify the model, dataset, and the type of attack you wish to apply. The attacks are implemented in the `adversarial_utils.py` script. Specifically, L2, L1, and Linf attacks use the Adversarial Robustness Toolbox (ART), while the L0 attack is handled by a custom implementation in the `iterative_pixel_attack()` function.

Once the generation process is complete, the adversarial examples will be saved in the `/adversarials` subfolder, located within the directory defined by `datapath.py`.

At the end of `generate_adversarial.py`, you will find an example of how to call the attack generation function, along with how to visualize the resulting adversarial examples using the `plot_adversarials()` function from `plot_adversarials.py`.
![Example of AEs using L2 attack ](images/Example_Everything_L2_adversarials.png)

<img src="images/Example_Everything_L2_adversarials.png" alt="L2 Examples" width="400"/>

### 3. Visualizing Neural Network Behavior Under Adversarial Attacks

The core logic for visualizing how neural networks behave under adversarial attacks is implemented in `proximity_methods.py`, which defines two main analysis methods - `compute_ratio()` and `compute_priximity()`. These methods aim to examine how internal activations evolve across each layer when adversarial inputs are passed through the network.

The analysis begins by loading pre-generated adversarial examples and comparing their internal activations to those of clean training samples, layer by layer. This layerwise tracking helps reveal patterns of sensitivity and feature distortion under attack. In the end the result are also saved under subfolder named `/distanes` inside the defined `datapath()`.

The visualization process uses various helper functions from `proximity_utils.py`, including tools for KNN counting, activation extraction, and structured comparison. A full example of how to apply these methods can be found in the `save_methods.py`. It contains two functions, `using_projection` and `using_ratio`, which demonstrate how to call the analysis pipeline and visualize the results using tools from `visualization_utils.py`. These examples serve as practical guides for performing layerwise analysis and plotting model behavior under adversarial conditions.

![CIFAR10 L0](images/net_CONV_CIFAR10_attack_L0.png)
![MNIST L0 4→9](images/net_CONV_MNIST_attack_L0_(4, 9).png)
![RESNET Linf Dev](images/net_RESNET_MNIST_attack_Linf_Dev.png)
![Projection 2→8](images/projection_CONV_MNIST_attack_L0_(2, 8).png)



