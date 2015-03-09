---
layout: post
title: "Supervised Learning - Binary Classification using Logistic Regression"
published: true
---

One of the main domains in classic machine learning is supervised learning. Where, we usually have a training data set with features and class labels and our objective is to predict the class of any new data, often known as the test data using its feature set based on some model which is built from the training data.

In simpler words, in supervised learning, each example is a pair consisting of an input object (typically a vector also known as the feature set) and a desired output value (also known as the class label). A supervised learning algorithm analyzes the training data and produces an inferred function, which can be used for mapping new examples ( from the test data). For more information and details regarding how to build ideal models, what trade-offs might be prevalent refer to [this Wikipedia article](http://www.wikiwand.com/en/Supervised_learning).

Today we will be looking at binary classification using binomial logistic regression. For my experiments, I will be using the popular language R, however you are free to use the language of your choice as long as you have necessary libraries and functions to perform supervised learning.

We consider the following case of ten points in a two-dimentional space with classes `red` and `blue`. For visualizing this, we use the following code segment.

```r
> color_vec1 <- c(rgb(1,0,0,1),rgb(0,0,1,1))
> x <- c(.4,.55,.65,.9,.1,.35,.5,.15,.2,.85)
> y <- c(.85,.95,.8,.87,.5,.55,.5,.2,.1,.3)
> z <- c(1,1,1,1,1,0,0,1,0,0)
> df <- data.frame(x,y,z)
> plot(x,y,pch=19,cex=2,col=color_vec1[z+1])
> head(df)
```

The data frame looks something like this,

```
     x    y z
1 0.40 0.85 1
2 0.55 0.95 1
3 0.65 0.80 1
4 0.90 0.87 1
5 0.10 0.50 1
6 0.35 0.55 0
```

The plot obtained is depicted below.

![](http://i.imgur.com/XgtPc1N.png)

Now, we build a generalized linear model using the data points to partition this space into the two classes so that we would be able to predict the class labels for future data points. For building the generalized linear model, we using the following code segment.

```r
> reg <- glm(z~x+y,data=df,family=binomial)
> summary(reg)
```

The details of the model using the `summary` function is depicted below.

```
Call:
glm(formula = z ~ x + y, family = binomial, data = df)

Deviance Residuals: 
    Min       1Q   Median       3Q      Max  
-1.6593  -0.4400   0.2564   0.5830   1.5374  

Coefficients:
            Estimate Std. Error z value Pr(>|z|)
(Intercept)   -1.706      1.999  -0.854    0.393
x             -5.489      5.360  -1.024    0.306
y              8.568      5.515   1.554    0.120

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 13.4602  on 9  degrees of freedom
Residual deviance:  8.1445  on 7  degrees of freedom
AIC: 14.144

Number of Fisher Scoring iterations: 5
```

Now, we build a prediction function to predict the class label given any data point in the two-dimensional space using the following code segment.

```r
> predict_2d <- function(x,y){
+         predict(reg,newdata=data.frame(x=x,y=y), type="response")>0.5
+ }
```

The threshold as you can see for predictions is `0.5` so the predicted class here is simply the one which is more likely. To visualize the partition of class labels in the two-dimensional space, we take sequence vectors ranging from `0` to `1` with an interval of `0.01` to cover the entire 2-D space between `0` and `1`. Then we use the `outer` function to apply our `predict_2d` prediction function on each of the data points.

```r
> x_vec <- seq(0,1,length=101)
> y_vec <- seq(0,1,length=101)
> z_vec <- outer(x_vec,y_vec,predict_2d)
> color_vec2 <- c(rgb(1,0,0,.2),rgb(0,0,1,.2))
> image(x_vec,y_vec,z_vec,col=color_vec2)
> points(x,y,pch=19,cex=2,col=color_vec1[z+1])
```

The visualization is depicted below.

![](http://i.imgur.com/hlr3eWI.png)

Since the logistic regression technique we used is a generalized linear model, the line that separate the two regions above is a straight line. To build a non-linear model, you can use the `nls` or `gmm` functions or add some higher order interaction terms to our existing model and rebuild it. I have used the latter approach in the following code snippet

```r
> reg <- glm(z~x+y+I(x**2)+I(y**2),data=df,family=binomial)
> z_vec <- outer(x_vec,y_vec,predict_2d)
> image(x_vec,y_vec,z_vec,col=color_vec2)
> points(x,y,pch=19,cex=2,col=color_vec1[z+1])
```

The plot obtained is shown below, you can clearly see that the regions are no longer separated by a straight line but it is a curve now.

![](http://i.imgur.com/UxDSSU0.png)

We will look at more examples of supervised learning problems in the future. Thanks a lot to [Arthur Charpentier](https://twitter.com/freakonometrics) and his awesome experiments without which this post would not have been possible.