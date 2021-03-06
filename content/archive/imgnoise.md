---
author: Ray Heberer
date: 2017-06-08
linktitle: Applying Noise to Images with imgnoise
title: Applying Noise to Images with imgnoise
tags: ["R", "Image Noise", "iXperience", "Code Sharing"]
highlight: false
---

### Introduction

It would feel strange to just begin writing a blog post as if it's something I've always been doing. As someone who enjoys working with computers and code, there's a little habit that is nagging at me... An itch that requires scratching. So why fight it? Here goes:

```
print("Hello World!")

## [1] "Hello World!"
```

There. Much better! I can now breathe a sigh of relief, and move on to the topic of today's post: the [imgnoise R package](https://github.com/rayheberer/imgnoise) I am developing.

Sometimes it's hard to believe that I have only been working with R for a single week, and am already building a package. Granted, I do have about half a year's worth of experience using Python recreationally to solve [Project Euler](https://projecteuler.net/) problems, but I don't think that takes away from the excellent instruction I have received from [iXperience](http://ixlink.co/honest-owl) and [Andrew Collier](http://www.exegetic.biz/blog/) (check out his blog, it's great brain-food for any data enthusiast).

Notice how I'm sneaking in personal details, so that I can both introduce myself and talk about a topic in this first blog post? Clever? Or clunky? You tell me!

Anyways, the purpose of the imgnoise package is to provide a convenient method for people to distort images in R. It acts on arrays, since that is typically how images are represented in R. I found the [PNG package](https://cran.r-project.org/web/packages/png/index.html) to be a nice and lightweight tool to use in conjunction with the function I was developing.

Let's see the PNG package in action.

```
library(png)
profile = readPNG("~/R/publicblog/img/profile.png")

writePNG(profile, "~/R/publicblog/img/imgnoise/profile_unaltered.png")
```
![](/img/profile_unaltered.png)

Before moving further, I would like to quickly acknowledge [Rustum Choksi](http://www.math.mcgill.ca/rchoksi/) as the mathematician and researcher who inspired me to make this package. His work in deblurring and denoising sometimes makes use of functions in MATLAB that apply noise to images in the first place so that the efficacy of his algorithms can be tested. This is what gave me the idea to implement such a function in R. I also had the distinct pleasure of failing his Partial Differential Equations course while attending McGill University.

### Demonstration

Now, I'd like to demonstrate a typical use of the imgnoise() function - the star player of the imgnoise package.

```
devtools::install_github("rayheberer/imgnoise")
library(imgnoise)
profile_salt_pepper = imgnoise(profile, "salt_and_pepper", noise_density = 0.1)

writePNG(profile_salt_pepper, "~/R/publicblog/img/imgnoise/profile_salt_pepper.png")
```
![](/img/profile_salt_pepper.png)

Well, while I'm at it I might as well show what all of the different types of noise might look like.

```
profile_uniform = imgnoise(profile, "uniform")
profile_gaussian = imgnoise(profile, "gaussian", variance = 0.01)
profile_speckle = imgnoise(profile, "speckle")

writePNG(profile_uniform, "~/R/publicblog/img/imgnoise/profile_uniform.png")
writePNG(profile_gaussian, "~/R/publicblog/img/imgnoise/profile_gaussian.png")
writePNG(profile_speckle, "~/R/publicblog/img/imgnoise/profile_speckle.png")
```
![](/img/profile_uniform.png)
![](/img/profile_gaussian.png)
![](/img/profile_speckle.png)

Hurrah, it works! I've probably never been so excited over low-fidelity in my life.

### Reflection

As with any sort of introspection, I may end up saying the word "I" quite a bit in the next couple paragraphs. That's why I would like to acknowledge my two classmates that contributed to the imgnoise package, for the sake of the reader understanding that I did not create this package alone. [Timothy](https://github.com/timweichong) and [Rice](https://github.com/rice-tyler) were my collaborators this past week, and they are both bright and promising individuals worth following.

Overall, I would say that the exercise of making a small R package was highly instructive, and also very empowering. I can't think of any better way to get acquainted with a new language, so major props to Andrew and whoever else may have had a hand in designing the rigorous data science curriculum I am now participating in.

My biggest personal success with imgnoise was figuring out a way to apply the helper functions - that act on numerics and return numerics altered by some value generated through a random method - to every entry in an array. I'd like to walk the reader through my missteps here, in case it might help someone else who is just starting with R.

Of course, the first thing I tried was simply passing in arrays as the first argument to my helper functions to see what would happen. Let's take a look at the helper function for speckle noise.

```
speckle_noise = function(image, variance = 0.04) {
  try(if(class(image)!="numeric") stop("array contained non-numerics", call. = FALSE))

  upper_bound = sqrt(variance*12) / 2
  lower_bound = upper_bound * (-1)

  random_noise = runif(1, min = lower_bound, max = upper_bound)
  image = image + random_noise*image

  return(image)
}
```

Since image arrays are in practice quite large, I'll just use a 2D, 3X3 array (a matrix) for my testing.

```
test.matrix = matrix(rep(c(1,1,1),3), nrow = 3)
print(test.matrix)

##      [,1] [,2] [,3]
## [1,]    1    1    1
## [2,]    1    1    1
## [3,]    1    1    1
```

Now, how does the speckle_noise() function behave on matrices?

```
print(speckle_noise(test.matrix))

##          [,1]     [,2]     [,3]
## [1,] 1.248684 1.248684 1.248684
## [2,] 1.248684 1.248684 1.248684
## [3,] 1.248684 1.248684 1.248684
```

As you can see, the function generated a single random number and then applied the algebraic expression containing that number to every entry in the matrix. Clearly, this is not what I wanted, since for an image this would just mean a shift in color. In order to produce noise, a different random number would need to be generated for every entry.

My first instinct was to iterate over every entry in an array using 2 nested for loops. The one I drafted looked something like this, before I gave up on it.

```
for (col in 1:ncol(test.matrix)) {
  for (row in col:nrow(test.matrix)) {
    #apply the function to test.matrix
  }
}
```

There were many problems with this. The most significant of these was that the for loops could not accomodate arrays of an arbitrary amount of dimensions. Plus, since images read by the PNG package are represented by four-dimensional arrays, I would need to incorporate 2 extra layers of nesting just to accomodate that specific case.

I was fortunate enough to find the solution I was looking for in some notes that Andrew had made for our class, but that we did not have time to go over. They were related to functional programming, contained instructions on how to use the apply() function. In the context of the notes, apply() was being used to map functions to either the rows or the columns of a matrix, determined by setting the second argument to 1 or 2. I took a leap of faith and tried passing in a vector of both 1 and 2.

```
print(apply(test.matrix, c(1,2), imgnoise, "speckle"))

##           [,1]     [,2]      [,3]
## [1,] 1.0893884 0.985291 1.3265607
## [2,] 1.0910289 1.211241 0.8051002
## [3,] 0.7357669 1.040077 1.2134051
```

It worked! The function was applied to every element of the matrix. From here all I needed to do was find a way to accomodate arrays of all dimensions. I followed my instincts, and was quickly rewarded with an idea that worked.

```
print(apply(test.matrix, 1:length(dim(test.matrix)), imgnoise, "speckle"))

##          [,1]      [,2]      [,3]
## [1,] 1.192606 0.7026806 0.7304188
## [2,] 1.262119 1.3136017 0.9245222
## [3,] 1.010564 1.1100736 0.6991730
```

And that is part of the story of imgnoise's genesis. I plan to continue tweaking this package, to the point where some day it may be worthy of submission to CRAN. Any input and guidance from more experienced (or just more clever) developers would be highly valued.

```
print("Goodbye World!")

## [1] "Goodbye World!"
```