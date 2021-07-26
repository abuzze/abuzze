---
title: "Python Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# Python cheatsheet

## shebang for pythonscripts

#!/usr/bin/env python


### variable type

#### strings

concatinate strings like a pro

	foobar = ''.join(['foo', 'bar'])


#### dictionary

	test = {'name':'John','job':'teacher'}
	print test['job'] # prints teacher
	print test.key() #prints ['name','job']
	print test.values() #prints ['John','teacher']
	print test.items() #prints [('name','teacher'),('nextkey','nextvalue')]

	for key in test:
    print key,test[key] #prints name John \n job teacher

#### lists

	zoo_animals = ["pangolin", "cassowary", "sloth"]
	zoo_animals.append("ape")
	first = zoo_animals[0:2]   # The first two items;second parm ends -before- index b
	zoo_animals.insert(1,"cobra") #change cassowary to cobra
	zoo_animals.index("cobra") # returns 1

##### list comprehension

	evens_to_50 = [i for i in range(51) if i % 2 == 0]
	print evens_to_50 #prints [0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48, 50]

	new_list = [x for x in range(1,6)] #prints [1, 2, 3, 4, 5]
	new_list = [x*2 for x in range(1,6)] #prints [2, 4, 6, 8, 10]


	look for items in my_list and put them into my_other_list if they are not already there

	my_list=[2,2,3,40]
	my_other_list =[]
    for i in my_list:
        if i not in my_other_list:
            no_dups.append(i)

#### list slicing
	l = [i ** 2 for i in range(1, 11)] # Should be [1, 4, 9, 16, 25, 36, 49, 64, 81, 100
	print l[2:9:2] #[start:end:stride] stride describes the space between items in the sliced list.  # [9, 25, 49, 81]
    print l[::5]   # [1,25,100] # every 5th

return vars greater then 4

	a = [3, 4, 5]
		b = [i for i in a if i > 4]
	or
		b = filter(lambda x: x > 4, a)

enumerate a list

	a = [3, 4, 5]
	a = [i + 3 for i in a]
Or:
	a = map(lambda i: i + 3, a)

	for index, item in enumerate(list):
    	print index, item


### functions

#### using functions the right way

def make_complex(x, y):
    return {'x': x, 'y': y} # return a list instead of just a var



for schleife 5x durchlaufen

	for i in range(5):

Userinpt

	x = raw_input("prompt")

print without a newline

	print something,

### files
open files the right way

	with open('file.txt') as f:
    	for line in f:
        	print line

    with open("text.txt", "w") as textfile:
		textfile.write("Success!")

### binary

print 0b111   #7
print 0b1 + 0b11 #4
print int("11001001",2) #201 because int(string,base)
print bin(2) #0b10
print ~2 # prints -3 because 1 is added and then the numbers turns negative

check if bit is set:

mask = 0b100 #check if the 3rd bit is on
a=0b110
desired = a & mask #if desired is greater than 0 == true

turning bits on:

a = 0b110 # 6
mask = 0b1 # 1
desired =  a | mask # 0b111, or 7

bitflipping:

a = 0b110 # 6
mask = 0b111 # 7 (always fill all needed bits with 1)
desired =  a ^ mask # 0b1


