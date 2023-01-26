# How to calculate π, which is as accurate as math.pi?


```python
import math, random
math.pi
```




    3.141592653589793




```python
def correct(calculated: float) -> float:
    correct = ""
    for a, b in zip(str(calculated), str(math.pi)):
        if a == b:
            correct += a
        else:
            break
    if correct:
        return float(correct)
    return 0.0
```

## Leibniz formula for π
${\displaystyle 1-{\frac {1}{3}}+{\frac {1}{5}}-{\frac {1}{7}}+{\frac {1}{9}}-\cdots ={\frac {\pi }{4}}}$


ref: https://en.wikipedia.org/wiki/Leibniz_formula_for_%CF%80


```python
def leibniz(terms: int) -> float:
    pi = 0
    numerator = 4
    denominator = 1
    for i in range(terms):
        if i % 2 == 0:
            pi += numerator / denominator
        else:
            pi -= numerator / denominator
        denominator += 2
    return pi
```


```python
print(f"terms - 10\t\t correct: {correct(leibniz(10))}")
print(f"terms - 1,000\t\t correct: {correct(leibniz(1_000))}")
print(f"terms - 1,000,000\t correct: {correct(leibniz(1_000_000))}\n")
```

    terms - 10		 correct: 3.0
    terms - 1,000		 correct: 3.14
    terms - 1,000,000	 correct: 3.14159
    


## Monte Carlo integration
$ {\pi } = {\frac {\textrm{points inside circle}}{\textrm{all points}}} $  
ref: https://en.wikipedia.org/wiki/Monte_Carlo_integration


```python
def monte_carlo(points: int) -> float:
    inside_circle = 0
    for _ in range(points):
        x = random.uniform(0, 1)
        y = random.uniform(0, 1)
        if x * x + y * y <= 1:
            inside_circle += 1
    return 4 * inside_circle / points
```


```python
print(f"points - 10\t\t correct: {correct(monte_carlo(10))}")
print(f"points - 1,000\t\t correct: {correct(monte_carlo(1_000))}")
print(f"points - 1,000,000\t correct: {correct(monte_carlo(1_000_000))}\n")
```

    points - 10		 correct: 3.0
    points - 1,000		 correct: 3.0
    points - 1,000,000	 correct: 3.14
    


## Euler's Machin-like formula
${\frac  {\pi }{4}}=\arctan {\frac  {1}{2}}+\arctan {\frac  {1}{3}}$  
ref: https://en.wikipedia.org/wiki/Machin-like_formula


```python
def machin() -> float:
    return 4 * (math.atan(1 / 2) + math.atan(1 / 3))
```


```python

print(f"identical to math.pi: {correct(machin()) == math.pi}\n")
```

    identical to math.pi: True
    


## Bailey–Borwein–Plouffe formula
${\displaystyle \pi =\sum _{k=0}^{\infty }\left[{\frac {1}{16^{k}}}\left({\frac {4}{8k+1}}-{\frac {2}{8k+4}}-{\frac {1}{8k+5}}-{\frac {1}{8k+6}}\right)\right]}$  
ref: https://en.wikipedia.org/wiki/Bailey%E2%80%93Borwein%E2%80%93Plouffe_formula


```python
def bbp(K: int) -> float:
    pi = 0
    for k in range(K):
        pi += (1 / 16 ** k) * (
            (4 / (8 * k + 1))
            - (2 / (8 * k + 4))
            - (1 / (8 * k + 5))
            - (1 / (8 * k + 6))
        )
    return pi
```


```python
print(f"terms - 10\t\t correct: {correct(bbp(10))}")
print(f"terms - 11\t\t identical to math.pi: {correct(bbp(11)) == math.pi}\n")
```

    terms - 10		 correct: 3.14159265358979
    terms - 11		 identical to math.pi: True
    


## Summary
Leibniz formula converges extremely slow, only 5 decimal digits are correct after summing $10^6$ terms.

Monte Carlo integration is non-deterministic since it depends on the random points distribution. It doesn't necessarily converge and even with $10^6$ points I didn't observe it outperform Leibnitz.

Machin-like formula was the most straightforward way for calculating π but it uses the math library for trigonometry, which in turn uses π behind the scenes. 

Bailey-Borwein-Plouffe formula converges way faster than Leibnitz and yields π equal to math.pi after summing just 11 terms.

All above calculation methods are strictly limited by the precison of float numbers: https://en.wikipedia.org/wiki/IEEE_754

This small piece of work was inspired by failing to complete this task:
https://www.codewars.com/kata/6357205000fba205ed189a52/python

## Calculating π with higher precision:
- https://gavalas.dev/blog/spigot-algorithms-for-pi-in-python/
- https://www.craig-wood.com/nick/articles/pi-chudnovsky/
- https://giordano.github.io/blog/2017-11-21-hexadecimal-pi/
- https://observablehq.com/@rreusser/computing-with-the-bailey-borwein-plouffe-formula
