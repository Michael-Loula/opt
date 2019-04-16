---
layout: post
title:  "Optimization Series: #1 Divine Proportions"
date:   2019-03-29 13:37:44 -0500
categories: stuff
---
## Golden Section Search
{% include mathjax_support %}
Golden section search is a method for finding extrema of a function $$f:[a,b] \rightarrow \mathbb{R}$$ that is strictly unimodal throughout it's domain.


```python
def golden_section_search(a,b,func,tol):
    # tau is the golden section
    tau = ((5**.5) - 1)/2.0
    while (b - a) > tol :
        x1 = a + (1 - tau)*(b-a)
        x2 = a + tau*(b-a)
        if (func(x1) > func(x2)):
            a = x1
        else:
            b = x2
    return (a+b)/2
```

Let's test this on a basic function:

$$f(x) = (x-4)^2$$

 on the interval $$[0,10]$$.


```python
import numpy as np
import matplotlib.pyplot as plt
import time
x_values = np.linspace(0,10,1000)
y_values = (x_values-4)**2
t = time.time()
x_min = golden_section_search(0,10,lambda x: (x-4)**2,1e-15)
t2 = time.time()
plt.plot(x_values,y_values)
plt.plot(x_min,(x_min - 4)**2,'ro')
plt.xlabel('x axis')
plt.ylabel('y axis')
print('time to find min '+str(t2-t) + 's')
```

    time to find min 0.00012683868408203125s



<img src="{{site.baseurl}}/assets/output_4_1.png">


## Need speed?

This method is very inefficient, since it requires two function calls per iteration. But since we only update one endpoint of the interval per iteration, we only have to evaluate one function call per iteration.


```python
def gss_fast(a,b,func,tol):
    tau = ((5**.5) - 1)/2.0
    x1 = a + (1 - tau)*(b-a)
    x2 = a + tau*(b-a)
    f1 = func(x1)
    f2 = func(x2)
    while (b-a) > tol:
        if (f1 > f2):
            a = x1
            x1 = x2
            f1 = f2
            x2 = a + tau*(b-a)
            f2 = func(x2)
        else:
            b = x2
            x2 = x1
            f2 = f1
            x1 = a + (1 - tau)*(b-a)
            f1 = func(x1)
    return (a+b)/2
```


```python
x_values = np.linspace(0,10,1000)
y_values = (x_values-4)**2
t = time.time()
x_min = gss_fast(0,10,lambda x: (x-4)**2,1e-15)
t2 = time.time()
plt.plot(x_values,y_values)
plt.plot(x_min,(x_min - 4)**2,'ro')
plt.xlabel('x axis')
plt.ylabel('y axis')
print('time to find min '+str(t2-t) + 's')
```

    time to find min 0.00011539459228515625s



<img src="{{site.baseurl}}/assets/output_8_1.png">


Evaluating $$(x-4)^2$$ does not take very much work, so the time difference is miniscule, but it should provide a large advantage when the majority of work being done is function evaluations.

Big thanks to Andreas Klöckner and Michael Heath for their lecture notes.
