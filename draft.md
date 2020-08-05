## Abstract 

Not all neural network architectures are created equal, some perform much better than others for certain tasks. But how important are the weight parameters of a neural network compared to its architecture? In this work, we question to what extent neural network architectures alone, without learning any weight parameters, can encode solutions for a given task. We propose a search method for neural network architectures that can already perform a task without any explicit weight training. To evaluate these networks, we populate the connections with a single shared weight parameter sampled from a uniform random distribution, and measure the expected performance. We demonstrate that our method can find minimal neural network architectures that can perform several reinforcement learning tasks without weight training. On supervised learning domain, we find architectures that can achieve much higher than chance accuracy on MNIST using random weights.

______

## Introduction

In biology, precocial species are those whose young already possess certain abilities from the moment of birth <dt-cite key="bbc_islands"></dt-cite>. There is evidence to show that lizard <dt-cite key="miles1995morphological"></dt-cite> and snake <dt-cite key="burger1998antipredator,mori2000does"></dt-cite> hatchlings already possess behaviors to escape from predators. Shortly after hatching, ducks are able to swim and eat on their own <dt-cite key="starck1998patterns"></dt-cite>, and turkeys can visually recognize predators <dt-cite key="goth2001innate"></dt-cite>. In contrast, when we train artificial agents to perform a task, we typically choose a neural network architecture we believe to be suitable for encoding a policy for the task, and find the weight parameters of this policy using a learning algorithm. Inspired by precocial behaviors evolved in nature, in this work, we develop neural networks with architectures that are naturally capable of performing a given task even when their weight parameters are randomly sampled. By using such neural network architectures, our agents can already perform well in their environment without the need to learn weight parameters.

<div style="text-align: center;">
<video class="b-lazy" data-src="https://storage.googleapis.com/quickdraw-models/sketchRNN/wann/mp4/square_biped.mp4" type="video/mp4" autoplay muted playsinline loop style="width:50%;" ></video><video class="b-lazy" data-src="https://storage.googleapis.com/quickdraw-models/sketchRNN/wann/mp4/square_racer.mp4" type="video/mp4" autoplay muted playsinline loop style="width:50%;" ></video>
<br/><br/>
<img class="b-lazy" src=data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw== data-src="https://storage.googleapis.com/quickdraw-models/sketchRNN/wann/png/rl_cover_left.png" style="width: 50%;"/><img class="b-lazy" src=data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw== data-src="https://storage.googleapis.com/quickdraw-models/sketchRNN/wann/png/rl_cover_right.png" style="width: 50%;"/>
<br/>
<figcaption style="text-align: left;">
<b>Examples of Weight Agnostic Neural Networks: Bipedal Walker (left), Car Racing (right)</b><br/>
We search for architectures by deemphasizing weights. In place of training, networks are assigned a single shared weight value at each rollout. Architectures that are optimized for expected performance over a wide range of weight values are still able to perform various tasks without weight training.
</figcaption>
</div>
<!--<div style="text-align: center;">
<br/><br/>
<video class="b-lazy" data-src="assets/mp4/square_swingup.mp4" type="video/mp4" autoplay muted playsinline loop style="width:33.33%;" ></video><video class="b-lazy" data-src="assets/mp4/square_biped.mp4" type="video/mp4" autoplay muted playsinline loop style="width:33.33%;" ></video><video class="b-lazy" data-src="assets/mp4/square_racer.mp4" type="video/mp4" autoplay muted playsinline loop style="width:33.33%;" ></video>
<br/><br/>
<img class="b-lazy" src="assets/png/rl_cover_all.png" style="display: block; margin: auto; width: 100%;"/>
<br/>
<figcaption style="text-align: left;">
<b>(all three demos)</b><br/>
We search for architectures by deemphasizing weights. In place of training, networks are assigned a single shared weight value at each rollout. Architectures that are optimized for expected performance over a wide range of weight values are still able to perform various tasks without weight training.
</figcaption>
</div>-->

Decades of neural network research have provided building blocks with strong inductive biases for various task domains. Convolutional networks <dt-cite key="lecun1995convolutional,fukushima1982neocognitron"></dt-cite> are especially suited for image processing <dt-cite key="cohen2016inductive"></dt-cite>. Recent work <dt-cite key="he2016powerful,ulyanov2018deep"></dt-cite> demonstrated that even randomly-initialized CNNs can be used effectively for image processing tasks such as superresolution, inpainting and style transfer. <dt-cite key="evolino">Schmidhuber et al.</dt-cite> have shown that a randomly-initialized LSTM <dt-cite key="lstm"></dt-cite> with a learned linear output layer can predict time series where traditional RNNs trained using reservoir methods <dt-cite key="jaeger2004harnessing,reservoir"></dt-cite> fail. More recent developments in self-attention <dt-cite key="vaswani2017attention"></dt-cite> and capsule <dt-cite key="sabour2017dynamic"></dt-cite> networks expand the toolkit of building blocks for creating architectures with strong inductive biases for various tasks. Fascinated by the intrinsic capabilities of randomly-initialized CNNs and LSTMs, we aim to search for *weight agnostic neural networks*, architectures with strong inductive biases that can already perform various tasks with random weights.

<div style="text-align: center;">
<br/>
<img class="b-lazy" src=data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw== data-src="https://storage.googleapis.com/quickdraw-models/sketchRNN/wann/png/mnist_cover.png" style="display: block; margin: auto; width: 100%;"/>
<br/>
<figcaption style="text-align: left;">
<b>MNIST classification network evolved to work with random weights</b><br/>
<!--Networks that already work with random weight parameters are not only easily trainable, but as we will demonstrate, we can also use the same network architecture with an ensemble of different weights to increase performance, without the need to explicitly train the weight parameters.-->
Network architectures that already work with random weights are not only easily trainable, they also offer other advantages too. For instance, we can give the same network an ensemble of (untrained) weights to increase performance, without the need to explicitly train any weight parameters.
<br/><br/>
While a conventional network with random initialization will get ~ 10% accuracy on MNIST, this particular network architecture achieves a much better than chance accuracy on MNIST (> 80%) with random weights. Without any weight training, the accuracy increases to > 90% when we use an ensemble of untrained weights.
</figcaption>
</div>

In order to find neural network architectures with strong inductive biases, we propose to search for architectures by deemphasizing the importance of weights. This is accomplished by **(1)** assigning a single shared weight parameter to every network connection and **(2)** evaluating the network on a wide range of this single weight parameter. In place of optimizing weights of a fixed network, we optimize instead for architectures that perform well over a wide range of weights. We demonstrate our approach can produce networks that can be expected to perform various continuous control tasks with a random weight parameter. As a proof of concept, we also apply our search method on a supervised learning domain, and find it can discover networks that, even without explicit weight training, can achieve a much higher than chance test accuracy of $\sim$ 92\% on MNIST. We hope our demonstration of such weight agnostic neural networks will encourage further research exploring novel neural network building blocks that not only possess useful inductive biases, but can also learn using algorithms that are not necessarily limited to gradient-based methods.

<div style="text-align: center;">
<br/>
<div id="intro_demo" class="unselectable" style="text-align: center;"></div>
<br/>
<figcaption style="color:#FF6C00;">Interactive Demo</figcaption>
<figcaption style="text-align: left;">
A weight agnostic neural network performing <i>CartpoleSwingup</i> task. Drag the slider to control the weight parameter and observe the performance at various shared weight parameters. You can also fine-tune the individual weights of all connections in this demo.
</figcaption>
</div>

______

## A couple other examples

You can use latex-like syntax for math:

**1.**&nbsp; *Random weights*:&nbsp; individual weights drawn from $\mathcal{U}(-2,2)$.

**2.**&nbsp; *Random shared weight*:&nbsp; a single shared weight drawn from $\mathcal{U}(-2,2)$.

**3.**&nbsp; *Tuned shared weight*:&nbsp; the highest performing shared weight value in range $(-2,2)$.

**4.**&nbsp; *Tuned weights*:&nbsp; individual weights tuned using population-based REINFORCE <dt-cite key="williams1992simple"></dt-cite>.


Here is a simple js bit for swapping through images (see end of lib/controller.js).

<div style="text-align: center;">
<br/>
<img id="mnist_figure" src="https://storage.googleapis.com/quickdraw-models/sketchRNN/wann/png/mnist_all.png" style="display: block; margin: auto; width: 100%;"/>
<div class="btn-group">
  <button id="mnist_all">All</button>&nbsp;
  <button id="mnist_0">0</button>&nbsp;
  <button id="mnist_1">1</button>&nbsp;
  <button id="mnist_2">2</button>&nbsp;
  <button id="mnist_3">3</button>&nbsp;
  <button id="mnist_4">4</button>&nbsp;
  <button id="mnist_5">5</button>&nbsp;
  <button id="mnist_6">6</button>&nbsp;
  <button id="mnist_7">7</button>&nbsp;
  <button id="mnist_8">8</button>&nbsp;
  <button id="mnist_9">9</button>
</div>
<br/>
<figcaption style="color:#FF6C00;">Interactive Demo</figcaption>
<figcaption style="text-align: left;">
<b>MNIST Classifier</b><br/>
Not all neurons and connections are used to predict each digit. Starting from the output connection for a particular digit, we can map out the part of the network used to classify that digit. We can also see which parts of the inputs are used for classification.
</figcaption>
</div>


*If you would like to discuss any issues or give feedback, please visit the [GitHub](https://github.com/weightagnostic/weightagnostic.github.io/issues) repository of this page for more information.*
