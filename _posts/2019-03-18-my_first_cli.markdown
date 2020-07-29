---
layout: post
title:      "Storing and Converting User Input"
date:       2019-03-18 03:05:47 -0400
permalink:  my_first_cli
---


I recently wrote my first CLI program for my CLI portfolio project.  For my program, I scraped the top coffee shops in the Cincinnati area.  (Github link [here](http://github.com/MarciClark/best_coffee_cinci)).  I learned a lot during the entire process of planning and writing this program.  While the curriculum itself offered plenty of information to implement into my program, I had no clue just how much coding you could do in one simple program.  This post is about just one line of code I wrote, but it is an important one.  

CLI programs rely on user input to direct the program on what to do.  While computer programs can gather, process, and return data, they need boundaries to ensure it works properly and efficiently.  The user has the ability to type in whatever they wish in the command line, so with that in mind, I needed to come up with a way to ensure that the user’s input would be recognized by my program.  Thankfully, Ruby has methods for this.  

Conversion methods are very useful when dealing with user input.  They convert objects - in my case, it will convert the user’s input (a string) into an integer.  My program asks the user to enter a number that corresponds with the coffee shop they would like more information on, so my program needs this input to be a number.  Here is the line of code I used to accomplish this:

					shop = @shops[input.to_i-1]  


And here is what this line does:

* First, I am setting shop (a local variable) equal to the shop the user picks.  This way, I can store the user’s input and call upon it later within my CLI class.  

* Next, I am converting the user’s input to an integer.  The to_i method allows for me to set up an implicit conversion of the string to an integer, and thus will allow my program to proceed on with retrieving the requested information.  

* Finally, I added “-1” because if I didn’t, my program would pick the next shop in line from the one the user picked.  So, if I typed in “1”, it would return the second shop, and so on.  Computers start counting with 0, and this was what my problem was: I needed to compensate for the fact that my shops were listed beginning with 1 and my user always starts counting with 1, while my program will start counting at 0.  In other words, the user sees a list and starts counting it by one, whereas my program sees a list and starts counting it by zero.  The -1 adjusts the numbers so that my program will now return the correct number the user enters.  

This little line of code does a lot for my program.  It stores the user’s input inside a variable so that I can refer to it later in my CLI without having to write additional code.  Then, it converts the user input from a string to an integer.  Finally, it ensures that my program will return the information for the exact shop my user wants information on.  And now my program can carry on, helping people fuel themselves with some of the best coffee in the area.  
