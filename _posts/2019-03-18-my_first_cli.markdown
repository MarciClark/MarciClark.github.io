---
layout: post
title:      "My First CLI"
date:       2019-03-18 07:05:47 +0000
permalink:  my_first_cli
---


I’m going to be completely honest - this project was rough for me.  I set my sight on eventually building an app that, when it came time to actually write the code, was a bit overwhelming for a newbie like myself.  I will finish that project on my own time one day, but for now, I decided to scale it down a little.  I am very glad I did… because it was still a bumpy road to get to the end. 
	
So what I ended up doing was creating a program that tracked the weekly New York Times Bestsellers.  I scraped Barne's and Nobel's website, as they conveniently track each week's New York Times Bestsellers, and was able to find all my data from their website.  Sidenote: I completely forgot that you have to pay to read the New York Times online after so many free articles.  This, along with the fact that scraping their website was a bit challenging, did not provide a convenient way to scrape straight from their website.
	
What is going to happen with the remainder of this blog is I am going to attempt to explain my code and what I’ve done to create my program.  I’m still having a hard time talking about my coding, so bare with me as I try to explain to the best of my abilities what I did.  Good luck!  :) 

The first thing I did was set up my project in the IDE browser.  I decided to forgo doing this on a local environment and used the Learn’s sandbox instead, using the following steps:
		1.) I first set up the gem, making sure to include both the MIT license and code of conduct.  
		2.) I then connected my project to my Github account by creating a new repository on Github and then added git commands in order to save all of my work.

If you notice my number of commits over on my Github for this project, I am an obsessive saver of my work.  One issue I found with using the sandbox for writing this program was that I had to save my work as soon as I typed even one line of code.  Otherwise, if I waited too long with no activity, I would get kicked out of the sandbox… and all my work gone.  

Once my gem bundler was installed and I connected everything to Github, I began adding the requirements to the gem spec.  The ones I added to my program were:
		1.) Pry
		2.) Nokogiri
		3.) rspec

Finally, onto the CLI! 

I then added my CLI folder, which handles the user input and returns what is requested of the user.  The following is what I did:
		1.) I first defined ‘call’ to welcome the user and set up my ‘list_categories’, ‘list_bestsellers’, and my goodby message.  
		2.) I then defined ‘list_categories’, allowing the user to tell me what category they would like to see: fiction, nonfiction, or children’s bestsellers.  
		3.) Next, I defined the ‘list_bestsellers’ method, which handles user input and returns what they have requested.  I also made sure to include an error message if the user incorrectly enters something my program cannot recognize.  
		4.) Finally, I included my goodbye message for when the user is ready to exit the program, or does so involuntarily, to let them know that they are leaving my program. 

```
class NYT_bestsellers::CLI 
  
  def call 
    puts "Looking for a good book?"
    NytBestsellers::bestsellers.scrape_info
    list_categories 
    list_bestsellers
    goodbye_message
  end 
  
  def list_categories
    puts "Which category would you like to see?  Fiction, nonfiction, or children's?"
    puts "Please select another category by typing 'category' or type 'exit' to leave"
    NytBestsellers::bestsellers.all.each.with_index(1) do |bestseller, i|
      puts "#{i}. #{bestseller.category}"
    end 
  end 
  
  def list_bestsellers
    input = nil 
    while input != "exit"
    input = gets.strip.downcase 
    if input == "category"
      list_categories 
    elsif input.to_i.between?(0, NytBestsellers::bestsellers.all.size)
    selected_category = NytBestsellers::bestsellers.find_bestsellers(input)
    puts "#{selected_category.title}"
    else
      puts "I do not recognize your answer. "
    end 
  end 
  
  def goodbye_message
    puts "Happy reading!"
  end
  
end
```
	

So then I set up ‘bestsellers.rb’. folder, which contains my initialization method and scraper methods.  Below is another handy list of what I did:  
		1.) Set my class to ‘NytBestsellers’ so that  I can call upon it to create a new object later on in my code.
		2.) I used the attr_accessor to create my title, author, category, and description variables.
		3.) I then initialized title, author, category, description, and the class variable ‘all’.  
		4.) Then I created the ‘self.all’,  ‘save’, and ‘self.find’ methods.. 
		5.) Next, I defined my ‘self.scrape_info’ method and used open brackets for my bestsellers.  This will allow me to push my scrapes into bestsellers.  
		6.) Finally, I defined my scrapes for fiction, nonfiction, and children’s New York Times bestsellers.  I used Nokogiri to parse the HTML, and then scraped the categories (fiction, nonfiction, and children’s), title, author, and description of each bestseller on the list.  

```
require 'pry'

class NytBestsellers::Bestsellers 
  
  attr_accessor :title, :author, :category, :description
  
  @@all = []
  
  def initialize(title, author, category, description)
    @title = title 
    @author = author 
    @category = category 
    @description = description
    @@all << self
  end 
  
  def self.all 
    @@all
  end 
  
  def save
    @@all << self 
  end 
  
  def self.find(id)
    self.all[id-1]
  end 
  
  def self.scrape_info
    bestsellers = []
    
    bestsellers << self.scrape_fiction
    bestsellers << self.scrape_nonfiction
    bestsellers << self.scrape_childrens
  end 
  
  def self.scrape_fiction
    html = open("https://www.barnesandnoble.com/b/the-new-york-times-bestsellers-hardcover-fiction/_/N-1p3r")
    doc = Nokogiri::HTML(html)
    category = doc.css("div.html content-node.html-embed-container").text.gsub(/\t/, "")
    title = doc.css("div.product-shelf-title pr-m").text.gsub(/\t/, "")
    author = doc.css("div.product-shelf-author contributors").text.gsub(/\t/, "")
    description = doc.css("div.text--medium overview-content").text.gsub(/\t/, "")
    
    fiction = self.new(category, title, author, description)
  end 
  
  def self.scrape_nonfiction
    html = open("https://www.barnesandnoble.com/b/the-new-york-times-bestsellers-hardcover-nonfiction/_/N-1p5q")
    doc = Nokogiri::HTML(html)
    category = doc.css("div.html content-node.html-embed-container").text.gsub(/\t/, "")
    title = doc.css("div.product-shelf-title pr-m").text.gsub(/\t/, "")
    author = doc.css("div.product-shelf-author contributors").text.gsub(/\t/, "")
    description = doc.css("div.text--medium overview-content").text.gsub(/\t/, "")
    
    nonfiction = self.new(category, title, author, description)
  end 
  
  def self.scrape_childrens
    html = open("https://www.barnesandnoble.com/b/the-new-york-times-bestsellers-childrens-picture-books/_/N-1p3p")
    doc = Nokogiri::HTML(html)
    category = doc.css("div.html content-node.html-embed-container").text.gsub(/\t/, "")
    title = doc.css("div.product-shelf-title pr-m").text.gsub(/\t/, "")
    author = doc.css("div.product-shelf-author contributors").text.gsub(/\t/, "")
    description = doc.css("div.text--medium overview-content").text.gsub(/\t/, "")
    
    childrens = self.new(category, title, author, description)
  end 
  
end 
```

Next, I added requirements to ‘lib/nyt_bestsellers.rb’ folder.  Those requirements are:
	  1.) Bundler > so that I can install the gems I need
		2.) Pry > for debugging my program 
		3.) Nokogiri > allows me to scrape a webpage
		4.) Open-URI > wraps Net::HTTP , HTTPS, and FTP.

```
require 'bundler'
require 'pry'
require 'nokogiri'
require 'open-uri'

require_relative '../lib/bestsellers'
require_relative '../lib/nyt_cli'
require_relative '../lib/NytBestsellers/version'
```


And finally, I added a few dependancies to gemspec.  Those dependancies are:
```
  spec.add_development_dependency "bundler", "~> 1.17"
  spec.add_development_dependency "rake", "~> 10.0"
  spec.add_development_dependency "pry"
  spec.add_development_dependency "nokogiri"
  spec.add_development_dependency "rspec"
```
	
	
It’s not pretty, but it’s my first ever CLI and I am proud to have finished it.  Hopefully my code gets a little cleaner, I can confidently talk about it, and get working on that app one day.  But for now, I am enjoying this process and look forward to writing more CLI’s in my future.  

Thank you! 

