# WAP on Simple Calculator

num1 = int(input('Enter the first number: '))
num2 = int(input('Enter the second number: '))
op = input('Input an operator(+, -, * and /): ')

if op == '+':
    print(num1+num2)
elif op == '-':
    print(num1-num2)
elif op == '*':
    print(num1*num2)
elif op == '/':
    print(num1/num2)
else:
    print('Invalid operator')

##################################################################

# WAP on Menu driven program for a restro 

print("Menu : 1. Misal 2. Pizza 3. Momos 4. Noodles 5. Deserts")
choice = int(input("Enter your choice :- "))
print(choice)

match(choice):
    case 1:
        print("Misal is selected! Rs.100")
    case 2:
        print("Pizza is selected! Rs.120")
    case 3:
        print("Momos is selected! Rs.60")
    case 4:
        print("Noodles is selected! Rs.90")
    case 5:
        print("Deserts is selected! Rs.170")
    
    case _: #defalut case
        print("Item not available!")

##################################################################

# WAP on a given number is odd or even

num = int(input("Enter a Number:- "))

if num > 0: 

    if num % 2 == 0:
        print("Number is Even")

    else:
        print("Number is Odd")

else: 
    print("Please enter a positive number")


##################################################################

# WAP on a given number is prime or not 

num = int(input("Enter a number:- "))
n = 2
while n < num:
    if num % n == 0:
        print("Not a prime number")
        break
    n=n+1

else:
    print("prime")

##################################################################

# WAP to calculate the factorial of a number 

num = int(input("Enter a number:- "))

if num < 0:
    print("Factorial is not allowed for negative numbers..")

else: 
    fact = 1
    for i in range(num,0,-1):
        fact *= i

    print("Factorial of", num , "is" , fact)

###################################################################

# Pattern Programming 

# "*"
# "**"
# "***"
# "****"
# "*****"

for i in range(5):
    print("*" * (i+1))


############################################################

# "*****"
# "****"
# "***"
# "**"
# "*"

print("--------------------------")
for n in range(5, 0, -1):
    print("*" * (n))


########################################################


# "*"
# "***"
# "*****"
# "*******"

print("--------------------------")
for n in range(1, 8, 2):
    print("*" * (n))


#########################################################

# """
#     *
#   * * *
# * * * * *
# """
print("--------------------------")

n_spaces = 2
for n_stars in range(1,6,2):
    print(" " * n_spaces + "*" * n_stars)
    n_spaces = n_spaces - 1

##############################################################


# """
#     *
#   * * *
# * * * * *
#   * * *
#     *
# """

rows = 3 
for i in range(1, rows + 1):
    print("  " * (rows - i) + "* " * (2 * i - 1))
for i in range(rows - 1, 0, -1):
    print("  " * (rows - i) + "* " * (2 * i - 1))

###############################################################

# n=7
#    *
#   ***
#  *****
# *******
#  *****
#   ***
#    *

n = int(input("Enter no of lines:- "))

n_spaces = n//2

for n_stars in range (1,n+1,2):
    print(" " * n_spaces + "*" * n_stars)
    n_spaces = n_spaces-1

n_spaces = 1

for n_stars in range (n-2,0,-2):
    print(" " * n_spaces + "*" * n_stars)
    n_spaces = n_spaces+1


###############################################################

# Write a menu driven program for calculator using functions

import math

# Function definitions
def add1(a, b):
    """Addition of a and b"""
    return a + b

def sub1(a, b):
    """Subtraction of a and b"""
    return a - b

def mul1(a, b):
    """Multiplication of a and b"""
    return a * b

def div1(a, b):
    """Division of a and b"""
    if b == 0:
        return "Cannot divide by zero"
    return a / b

def sq_root(x):
    """Square root of x"""
    if x < 0:
        return "Cannot find square root of negative number"
    return math.sqrt(x)

def power(y, z):
    """Power of y raised to z"""
    return math.pow(y, z)

def sin1(x):
    """Sine of angle in degrees"""
    return math.sin(math.radians(x))

def cos1(x):
    """Cosine of angle in degrees"""
    return math.cos(math.radians(x))

# Menu
print("Calculator Menu:")
print("1. ADD")
print("2. SUB")
print("3. MUL")
print("4. DIV")
print("5. SQRT")
print("6. POWER")
print("7. SIN")
print("8. COS")

# User input
choice = int(input("Enter your choice (1-8): "))

# Based on choice
if choice in [1, 2, 3, 4, 6]:
    a = float(input("Enter first number: "))
    b = float(input("Enter second number: "))

    if choice == 1:
        print("Result:", add1(a, b))
    elif choice == 2:
        print("Result:", sub1(a, b))
    elif choice == 3:
        print("Result:", mul1(a, b))
    elif choice == 4:
        print("Result:", div1(a, b))
    elif choice == 6:
        print("Result:", power(a, b))

elif choice == 5:
    x = float(input("Enter a number: "))
    print("Result:", sq_root(x))

elif choice == 7:
    x = float(input("Enter angle in degrees: "))
    print("Result:", sin1(x))

elif choice == 8:
    x = float(input("Enter angle in degrees: "))
    print("Result:", cos1(x))

else:
    print("Invalid choice!")

#####################################################################

# WA function to take string as parameter and Check if string is palindrome or not

s = "madam"

def string(s):
    
    if (s == s [::-1]):
        print("Palindrome")

    else:
        print("Not Palindrome")

string(s)

######################################################################

# Take two integer values in a & b. 
# Swap their values using tuple, using temparary variable and without tuple and without temparary variable.
# Ex. a=10 b=23
# After swapping a=23 b=10

# SYNTAX 1 

tuple1 = (10)
tuple2 = (23)
print("Before Swap")
print (tuple1,tuple2)

a = tuple1
tuple1 = tuple2
tuple2 = a

print("After Swap")
print (tuple1,tuple2)

# SYNTAX 2

tuple1 = (10)
tuple2 = (23)
print("Before Swap")
print (tuple1,tuple2)

tuple1,tuple2 = tuple2,tuple1

print("After Swap")
print (tuple1,tuple2)

###################################################################

# Take a list 'l1' of even nos between 150 to 250.
# Print its length.
# Then create another list 'l2' using 'l1', 
# containing only nos divisible by 4 from 'l1'.

l1 = []
for num in range(150, 251):
    if num % 2 == 0:
        l1.append(num)

print("Length of l1:", len(l1))

l2 = []
for num in l1:
    if num % 4 == 0:
        l2.append(num)

print("List l2 (numbers divisible by 4):", l2)

######################################

# Write a program to take a number from user.
# Check whether the last digit of that is divisible by 3 or not.  
# [hint: last digit of num is num%10]

num = int(input("Enter a number: "))

last_digit = num % 10

if last_digit % 3 == 0:
    print(f"The last digit {last_digit} of {num} is divisible by 3.")
else:
    print(f"The last digit {last_digit} of {num} is not divisible by 3.")

#################################

# A school has following rules for grading system:  
# a.	Below 25 - F  
# b.	25 to 45 - E  
# c.	45 to 50 - D  
# d.	50 to 60 - C  
# e.	60 to 80 - B  
# f.	Above 80 - A  
# Ask user to enter marks and print the corresponding grade.  


marks = int(input("Enter the marks:- "))

if marks < 0 or marks > 100:
    print("Please enter a valid positive number between 0 to 100.")

else:
    if marks < 25:
        print("Grade: F")
    elif marks >= 25 and marks < 45:
        print("Grade: E")
    elif marks >= 45 and marks < 50:
        print("Grade: D")
    elif marks >= 50 and marks < 60:
        print("Grade: C")
    elif marks >= 60 and marks < 80:
        print("Grade: B")
    else:  # marks above 80
        print("Grade: A")

###################################