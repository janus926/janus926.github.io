---
layout: post
title: "Running on a deep learning platform?"
published: true
---

What if it's a deep learning OS or VM, which optimize programs and user experience automitically? Seems quite interesting. I should study neural network, now!

Some to start:

- [Hacker's guide to Neural Networks](http://karpathy.github.io/neuralnets/)  
    - Forward & backward pass
    Imagine neuron is a function, which can be represted as gates with input and a single output. With inputs you can move forward along the gates to get output, and you can calculate gradients of each input to higher the ouput with +1 by moving backward (backpropogate).
    - Training
    Have a dataset to run the function (forward) and tune its parameters (backward) so it outputs high scores for positive examples and low scores for negative examples.

  So far, my understanding after reading this is it's pattern recogniztion by updating your function with the training data. It's more like to determine what's in front of you, an image or a speech. For me, learning is translating a thing to something I understand how/what it is, keep it as a representation (somehow) in my brain.

- [Deep Learning vs Machine Learning vs Pattern Recognition](http://quantombone.blogspot.com/2015/03/deep-learning-vs-machine-learning-vs.html)
- [Deep Learning, NLP, and Representations](http://colah.github.io/posts/2014-07-NLP-RNNs-Representations/)