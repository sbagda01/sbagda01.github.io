---
title: How to pretend you know Git.
layout: article
tags: FirstDayAtWork Git Part2
key: how_to_pretend_you_know_git_part_2
published: false
---

## Cherry-picking

The `cherry-pick` command allows us to pick one or more very specific commits
and apply them onto a selected branch. I find it very useful in two scenarios.

### Shared work

Let's go back to our initial program and add a couple of more features. First
we need a function that would calculate the product of all the numbers in a list
and a function that would print the product to the standard output. The second
feature is that we need a function that would calculate the product of all evens
in a list and a function that would print it to the standard output. I can probably see where am I going with this. One or two developers are asked to build the two features. Let's start on the first one. First I made sure that `master` was up to date, then I created a new branch called `calculate_and_print_the_product` based on master. First let's add a function what calculates a product of the numbers in a list.

```python
"""calculate_the_sum.py"""

import numpy

...

def calculate_the_product(list_of_numbers):
    return numpy.prod(list_of_numbers)
```

```bash
Sergiys-MacBook-Pro:code sbagda01$ git commit -am "Calculate the product."
[calculate_and_print_the_product 3cd3a83] Calculate the product.
 1 file changed, 6 insertions(+)
```

Now let's add a function that would print the product prepended by a message.

```python
"""calculate_the_sum.py"""

...

def print_number_to_standard_output(msg, number):
    print(f"{msg}:{number}")
```

```bash
Sergiys-MacBook-Pro:code sbagda01$ git commit -am "Print the product."
[calculate_and_print_the_product 6fcfa76] Print the product.
 1 file changed, 3 insertions(+)
```

We can now push our new branch to the origin and (probably) create a pull request.

```bash
Sergiys-MacBook-Pro:code sbagda01$ git push origin calculate_and_print_the_product
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
...
```

Make sure that everything looks like it should by running: `git log --oneline`.

To implement the second feature, switch back to master and create and select a new branch called `calculating_the_product_of_evens`. We are aware that a part of the functionality -printing a number- we are looking to implement, was already implemented as part of the first feature. To avoid coding it again we can cherry pick the commit we are interesting in but which one?

```bash
Sergiys-MacBook-Pro:code sbagda01$ git checkout calculate_and_print_the_product
Switched to branch 'calculate_and_print_the_product'
Sergiys-MacBook-Pro:code sbagda01$ git log --oneline
6fcfa76 (HEAD -> calculate_and_print_the_product, origin/calculate_and_print_the_product) Print the product.
3cd3a83 Calculate the product.
220c017 (origin/master, master, calculating_the_product_of_evens) merged calculating_an_average in.
...
```

The commit we need is the one with `6fcfa76` as a short hash, let's cherry pick it.

```bash
Sergiys-MacBook-Pro:code sbagda01$ git checkout calculating_the_product_of_evens
Switched to branch 'calculating_the_product_of_evens'
Sergiys-MacBook-Pro:code sbagda01$ git cherry-pick 6fcfa76
error: could not apply 6fcfa76... Print the product.
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'
```

But why? To understand this, we need to understand what cherry-picking does. We asked
Git to cherry-pick `6fcfa76`, but earlier we have seen that each commit is basically
a snapshot of how the file look like so what does that mean? Cherry-pick tries to apply the difference between the commit we are interested in and its parent to the
selected branch. We can see what that is by running:

```bash
Sergiys-MacBook-Pro:code sbagda01$ git diff 3cd3a83..6fcfa76
diff --git a/calculate_the_sum.py b/calculate_the_sum.py
index c9294fc..e3c67cb 100644
--- a/calculate_the_sum.py
+++ b/calculate_the_sum.py
@@ -25,3 +25,6 @@ def calculate_the_average_of_numbers_in_a_list(list_of_numbers):

 def calculate_the_product(list_of_numbers):
     return numpy.prod(list_of_numbers)
+
+def print_number_to_standard_output(msg, number):
+    print(f"{msg}:{number}")
```

The diff says that 3 lines have been extracted, starting from line 25 and
6 lines have been added, starting from line 25. The problem is, there is no line
25 in `calculate_the_sum.py` on `calculating_the_product_of_evens` branch. There is no logical way to apply this difference to our file, hence we get the conflict. We have already seen how to resolve conflicts and there is nothing special about this one so let's abort this cherry-picking and try something different.

```bash
Sergiys-MacBook-Pro:code sbagda01$ git cherry-pick --abort
```

Actually, we can reuse both commits from the `calculate_and_print_the_product` branch. You might say, and you will be right that we could as well merge the branch in, but the point of this exercise is to learn about cherry-picking so bear with me.

```bash
Sergiys-MacBook-Pro:code sbagda01$ git cherry-pick 3cd3a83
[calculating_the_product_of_evens 7bb6a39] Calculate the product.
 Date: Sun Oct 4 12:57:20 2020 +0100
 1 file changed, 6 insertions(+)
Sergiys-MacBook-Pro:code sbagda01$ git cherry-pick 6fcfa76
[calculating_the_product_of_evens 4c77a7c] Print the product.
 Date: Sun Oct 4 12:57:59 2020 +0100
 1 file changed, 3 insertions(+)
```

You can now check the file and you will see the expected changes. Here is something else:

```bash
Sergiys-MacBook-Pro:code sbagda01$ git log --oneline
4c77a7c (HEAD -> calculating_the_product_of_evens) Print the product.
7bb6a39 Calculate the product.
220c017 (origin/master, master) merged calculating_an_average in.
94cc0b4 Write the sum of evens to output.txt.
...
```

Notice that the hashes are different from the hashes in the original branch. Cherry-pick always **adds new** commits. Time to finish this feature.


```python
"""calculate_the_sum.py"""

...

def calculate_the_product_of_evens(list_of_numbers):
    return calculate_the_product([n for n in list_of_numbers if not n % 2])
```

> Note: You can use cherry-pick with ranges as well, check [this](https://stackoverflow.com/a/1994491/2942762) StackOverflow answer for more.


Commit, push and create a pull request!

### Working with different branches

The other scenario where I find cherry-picking useful is when you have parallel histories which will not be merged together and you need to develop a piece of work for both. You would start two feature branches based on the respective histories. You can do the work for one branch and then cherry pick the commits in the other feature branch. You will make the connection if you come across such a case.

> Note: The `calculate_the_sum.py` file's name is now completely out of sync with its content. I will let you figure out how to rename files using Git.

## Rebasing

Rebasing is similar to cherry-kicking in the sense that you can replay changes that
were committed to one branch, onto another. 


## Reverting


## How to keep the main history clean.
