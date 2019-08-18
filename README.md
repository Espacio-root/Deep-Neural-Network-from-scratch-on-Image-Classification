# Deep Neural Network from scratch on Image Classification - Dogs or Cats?

## **Phuong T.M. Chu and Hai Nguyen**

**Deep Neural Network from scratch on Image Classification - Dogs or Cats?** is the project that we finished after the 5th week of studying **Machine Learning**.

![](https://storage.googleapis.com/kaggle-competitions/kaggle/3362/media/woof_meow.jpg)

## INTRODUCTION
**Dogs vs. Cats** [dataset](https://www.kaggle.com/c/dogs-vs-cats/data) provided by  Microsoft Research contains 25,000 images of dogs and cats with the labels 
* 1 = dog
* 0 = cat 

### Project goals:
1. Building a **deep neural network from scratch** to classify dogs and cats images

2. **Tunning the hyperparameters** of the model in order to achieve high accuracy. This project explores the applicability of deep neural network by tunning these hyperparameters:
    * Learning rate
    * Number of hidden layers
    * Number of nodes in each hiden layers
    * Number of iterations

## BUILDING DEEP NEURAL NETWORK FROM SCRATCH

In this notebook, we implemented all the functions required to build a deep neural network.
![](https://i.imgur.com/ivhZhmx.png)

**Notation**:
- Superscript $[l]$ denotes a quantity associated with the $l^{th}$ layer. 
    - Example: $a^{[L]}$ is the $L^{th}$ layer activation. $W^{[L]}$ and $b^{[L]}$ are the $L^{th}$ layer parameters.
- Superscript $(i)$ denotes a quantity associated with the $i^{th}$ example. 
    - Example: $x^{(i)}$ is the $i^{th}$ training example.
- Lowerscript $i$ denotes the $i^{th}$ entry of a vector.
    - Example: $a^{[l]}_i$ denotes the $i^{th}$ entry of the $l^{th}$ layer's activations).
    
The initialization for a deeper L-layer neural network is more complicated because there are many more weight matrices and bias vectors. When completing the `initialize_params`, we made sure that our dimensions match between each layer. Given $n^{[l]}$ is the number of units in layer $l$. Thus for example if the size of our input $X$ is $(12288, 209)$ (with $m=209$ examples) then:

| |**Shape of W**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |**Shape of b**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|**Activation**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|**Shape of Activation**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|:-|:-|:-|:-|:-|
|**Layer 1**|$(n^{[1]},12288)$|$(n^{[1]},1)$|$Z^{[1]} = W^{[1]}  X + b^{[1]} $|$(n^{[1]},209)$|
| **Layer 2**|$(n^{[2]}, n^{[1]})$|$(n^{[2]},1)$|$Z^{[2]} = W^{[2]} A^{[1]} + b^{[2]}$|$(n^{[2]}, 209)$|
|$\vdots$| $\vdots$ | $\vdots$|$\vdots$|$\vdots$|
|**Layer L-1** | $(n^{[L-1]}, n^{[L-2]})$ | $(n^{[L-1]}, 1)$ | $Z^{[L-1]} =  W^{[L-1]} A^{[L-2]} + b^{[L-1]}$ | $(n^{[L-1]}, 209)$|
|**Layer L** | <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$(n^{[L]},&space;n^{[L-1]})$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$(n^{[L]},&space;n^{[L-1]})$" title="\large $(n^{[L]}, n^{[L-1]})$" /></a>| <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$(n^{[L]},&space;1)$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$(n^{[L]},&space;1)$" title="\large $(n^{[L]}, 1)$" /></a>|  <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$Z^{[L]}&space;=&space;W^{[L]}&space;A^{[L-1]}&space;&plus;&space;b^{[L]}$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$Z^{[L]}&space;=&space;W^{[L]}&space;A^{[L-1]}&space;&plus;&space;b^{[L]}$" title="\large $Z^{[L]} = W^{[L]} A^{[L-1]} + b^{[L]}$" /></a>|<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$(n^{[L]},&space;209)$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$(n^{[L]},&space;209)$" title="\large $(n^{[L]}, 209)$" /></a> |

Remember that when we compute <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$W&space;X&space;&plus;&space;b$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$W&space;X&space;&plus;&space;b$" title="\large $W X + b$" /></a> in python, it carries out broadcasting. For example, if: 

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$&space;W&space;=&space;\begin{bmatrix}&space;j&space;&&space;k&space;&&space;l\\&space;m&space;&&space;n&space;&&space;o&space;\\&space;p&space;&&space;q&space;&&space;r&space;\end{bmatrix}\;\;\;&space;X&space;=&space;\begin{bmatrix}&space;a&space;&&space;b&space;&&space;c\\&space;d&space;&&space;e&space;&&space;f&space;\\&space;g&space;&&space;h&space;&&space;i&space;\end{bmatrix}&space;\;\;\;&space;b&space;=\begin{bmatrix}&space;s&space;\\&space;t&space;\\&space;u&space;\end{bmatrix}$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$&space;W&space;=&space;\begin{bmatrix}&space;j&space;&&space;k&space;&&space;l\\&space;m&space;&&space;n&space;&&space;o&space;\\&space;p&space;&&space;q&space;&&space;r&space;\end{bmatrix}\;\;\;&space;X&space;=&space;\begin{bmatrix}&space;a&space;&&space;b&space;&&space;c\\&space;d&space;&&space;e&space;&&space;f&space;\\&space;g&space;&&space;h&space;&&space;i&space;\end{bmatrix}&space;\;\;\;&space;b&space;=\begin{bmatrix}&space;s&space;\\&space;t&space;\\&space;u&space;\end{bmatrix}$$" title="\large $$ W = \begin{bmatrix} j & k & l\\ m & n & o \\ p & q & r \end{bmatrix}\;\;\; X = \begin{bmatrix} a & b & c\\ d & e & f \\ g & h & i \end{bmatrix} \;\;\; b =\begin{bmatrix} s \\ t \\ u \end{bmatrix}$$" /></a>

Then <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$WX&space;&plus;&space;b$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$WX&space;&plus;&space;b$" title="\large $WX + b$" /></a> will be:

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$&space;WX&space;&plus;&space;b&space;=&space;\begin{bmatrix}&space;(ja&space;&plus;&space;kd&space;&plus;&space;lg)&space;&plus;&space;s&space;&&space;(jb&space;&plus;&space;ke&space;&plus;&space;lh)&space;&plus;&space;s&space;&&space;(jc&space;&plus;&space;kf&space;&plus;&space;li)&plus;&space;s\\&space;(ma&space;&plus;&space;nd&space;&plus;&space;og)&space;&plus;&space;t&space;&&space;(mb&space;&plus;&space;ne&space;&plus;&space;oh)&space;&plus;&space;t&space;&&space;(mc&space;&plus;&space;nf&space;&plus;&space;oi)&space;&plus;&space;t\\&space;(pa&space;&plus;&space;qd&space;&plus;&space;rg)&space;&plus;&space;u&space;&&space;(pb&space;&plus;&space;qe&space;&plus;&space;rh)&space;&plus;&space;u&space;&&space;(pc&space;&plus;&space;qf&space;&plus;&space;ri)&plus;&space;u&space;\end{bmatrix}$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$&space;WX&space;&plus;&space;b&space;=&space;\begin{bmatrix}&space;(ja&space;&plus;&space;kd&space;&plus;&space;lg)&space;&plus;&space;s&space;&&space;(jb&space;&plus;&space;ke&space;&plus;&space;lh)&space;&plus;&space;s&space;&&space;(jc&space;&plus;&space;kf&space;&plus;&space;li)&plus;&space;s\\&space;(ma&space;&plus;&space;nd&space;&plus;&space;og)&space;&plus;&space;t&space;&&space;(mb&space;&plus;&space;ne&space;&plus;&space;oh)&space;&plus;&space;t&space;&&space;(mc&space;&plus;&space;nf&space;&plus;&space;oi)&space;&plus;&space;t\\&space;(pa&space;&plus;&space;qd&space;&plus;&space;rg)&space;&plus;&space;u&space;&&space;(pb&space;&plus;&space;qe&space;&plus;&space;rh)&space;&plus;&space;u&space;&&space;(pc&space;&plus;&space;qf&space;&plus;&space;ri)&plus;&space;u&space;\end{bmatrix}$$" title="\large $$ WX + b = \begin{bmatrix} (ja + kd + lg) + s & (jb + ke + lh) + s & (jc + kf + li)+ s\\ (ma + nd + og) + t & (mb + ne + oh) + t & (mc + nf + oi) + t\\ (pa + qd + rg) + u & (pb + qe + rh) + u & (pc + qf + ri)+ u \end{bmatrix}$$" /></a>

### **Mathematical expression of the algorithm**:

![](https://i.imgur.com/FPjpVDX.png)

### **Foward propagation:**

The linear forward module (vectorized over all the examples) computes the following equations:

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$Z^{[l]}&space;=&space;W^{[l]}A^{[l-1]}&space;&plus;b^{[l]}$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$Z^{[l]}&space;=&space;W^{[l]}A^{[l-1]}&space;&plus;b^{[l]}$$" title="\large $$Z^{[l]} = W^{[l]}A^{[l-1]} +b^{[l]}$$" /></a>

where <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$A^{[0]}&space;=&space;X^T$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$A^{[0]}&space;=&space;X^T$" title="\large $A^{[0]} = X^T$" /></a>. And the activation functions:

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$A&space;=&space;RELU(Z)&space;=&space;max(0,&space;Z)$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$A&space;=&space;RELU(Z)&space;=&space;max(0,&space;Z)$$" title="\large $$A = RELU(Z) = max(0, Z)$$" /></a>
<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$A^{[L]}&space;=&space;sigmoid(Z^{[L]})$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$A^{[L]}&space;=&space;sigmoid(Z^{[L]})$$" title="\large $$A^{[L]} = sigmoid(Z^{[L]})$$" /></a>

### **Cost function**

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$J&space;=&space;-\frac1m\sum&space;\bigg(&space;Y&space;\odot&space;log(A^{[L]})&space;&plus;&space;(1-Y)&space;\odot&space;log(1-A^{[L]})&space;\bigg)$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$J&space;=&space;-\frac1m\sum&space;\bigg(&space;Y&space;\odot&space;log(A^{[L]})&space;&plus;&space;(1-Y)&space;\odot&space;log(1-A^{[L]})&space;\bigg)$$" title="\large $$J = -\frac1m\sum \bigg( Y \odot log(A^{[L]}) + (1-Y) \odot log(1-A^{[L]}) \bigg)$$" /></a>

> Note that <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$\odot$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$\odot$" title="\large $\odot$" /></a> denotes elementwise multiplication.

### **Backward propagation**

The three outputs <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$(dZ^{[l]},&space;dW^{[l]},&space;db^{[l]})$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$(dZ^{[l]},&space;dW^{[l]},&space;db^{[l]})$" title="\large $(dZ^{[l]}, dW^{[l]}, db^{[l]})$" /></a> are computed using the input <a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$dZ^{[l]}$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$dZ^{[l]}$" title="\large $dZ^{[l]}$" /></a>.Here are the formulas we need:

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$dZ^{[l]}&space;=&space;W^{[l&plus;1]^T}dZ^{[l&plus;1]}&space;\odot&space;g^{[l]'}(Z^{[l]})$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$dZ^{[l]}&space;=&space;W^{[l&plus;1]^T}dZ^{[l&plus;1]}&space;\odot&space;g^{[l]'}(Z^{[l]})$$" title="\large $$dZ^{[l]} = W^{[l+1]^T}dZ^{[l+1]} \odot g^{[l]'}(Z^{[l]})$$" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$&space;dW^{[l]}&space;=&space;\frac{\partial&space;\mathcal{L}&space;}{\partial&space;W^{[l]}}&space;=&space;\frac{1}{m}&space;dZ^{[l]}&space;A^{[l-1]&space;T}$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$&space;dW^{[l]}&space;=&space;\frac{\partial&space;\mathcal{L}&space;}{\partial&space;W^{[l]}}&space;=&space;\frac{1}{m}&space;dZ^{[l]}&space;A^{[l-1]&space;T}$$" title="\large $$ dW^{[l]} = \frac{\partial \mathcal{L} }{\partial W^{[l]}} = \frac{1}{m} dZ^{[l]} A^{[l-1] T}$$" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;\dpi{120}&space;\large&space;$$&space;db^{[l]}&space;=&space;\frac{\partial&space;\mathcal{L}&space;}{\partial&space;b^{[l]}}&space;=&space;\frac{1}{m}&space;\sum_{i&space;=&space;1}^{m}&space;dZ^{[l](i)}$$" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\inline&space;\dpi{120}&space;\large&space;$$&space;db^{[l]}&space;=&space;\frac{\partial&space;\mathcal{L}&space;}{\partial&space;b^{[l]}}&space;=&space;\frac{1}{m}&space;\sum_{i&space;=&space;1}^{m}&space;dZ^{[l](i)}$$" title="\large $$ db^{[l]} = \frac{\partial \mathcal{L} }{\partial b^{[l]}} = \frac{1}{m} \sum_{i = 1}^{m} dZ^{[l](i)}$$" /></a>



## MODEL PERFORMANCE

## CONCLUSION
The current literature suggests machine classifiers can score above [80% accuracy](chrome-extension://cbnaodkpfinfiipjblikofhlhlcickei/src/pdfviewer/web/viewer.html?file=http://xenon.stanford.edu/~pgolle/papers/dogcat.pdf) on this task.
We successfully achieved the accuracy of % with these detailed hyperparameters:
