# **Assignment: Introduction to Python**

## **Objective**

Test your understanding of the majority of the concepts covered in the Python fundamentals lessons.

## **Instructions**

Complete the following tasks and submit your code in a Python script.

## **Task 1: Data Types and Variables**

Create a Python program that demonstrates the use of the following data types:

- Integers
- Floats
- Strings
- Boolean
- Lists
- Tuples
- Dictionaries

Use variables to store and print them.

### Examples

```python
x = 5  # integer
print(x)  # output: 5

y = 3.14  # float
print(y)  # output: 3.14
```

## **Task 2: Functions and Classes**

Complete the following Python codes at the "replace with your code" comments.

```python
def count_and_return_vowels(text):
    """
    Counts the number of vowels (a, e, i, o, u) in the given text (case-insensitive) and returns the count and list of vowels.
    """
    return 0, [] # replace with your code


print(count_and_return_vowels("Hello World")) # output: (3, ['e', 'o', 'o'])
print(count(count_and_return_vowels("Programming")) # output: (3, ['o', 'a', 'i'])
print(count_and_return_vowels("OpenAI")) # output: (2, ['O', 'e', 'A', 'I'])


def sum_of_even_numbers(limit):
    """
    Calculates the sum of even numbers up to a given limit using a while loop.
    """
    return 0 # replace with your code


print(sum_of_even_numbers(10)) # output: 30
print(sum_of_even_numbers(5)) # output: 6
print(sum_of_even_numbers(1)) # output: 0


class BankAccount:
    """
    Create a BankAccount class with:
    - Constructor that sets initial balance
    - deposit() method that adds money
    - withdraw() method that removes money if sufficient funds exist
    - get_balance() method that returns current balance
    """
    pass  # replace with your code


account = BankAccount(100)
print(account.get_balance())  # output: 100
account.deposit(50)
print(account.get_balance())  # output: 150
account.withdraw(30)
print(account.get_balance())  # output: 120
account.withdraw(200)  # Should print: "Insufficient funds"
print(account.get_balance())  # output: 120
```

## **Deliverable:**

Submit your code in a Python script (`.py` file). You can submit as a single file (`assignment.py`) or multiple files based on the tasks (`task1.py`, `task2.py`). Include comments to explain your code (optional).
