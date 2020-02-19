---
layout: post
title:      "CLI Data Gem Project - Bassmaster Tournament CLI List"
date:       2020-02-16 20:13:14 -0500
permalink:  cli_data_gem_project_-_bassmaster_tournament_cli_list
---


During this project week, I worked on making a CLI application that lists Bassmaster fishing tournaments for the year, and lets its user to gain more information about the tournaments listed in the application. Throughout the whole week, creating this application gave me a few insights on how to scrape data from websites and use them in to a project, which at first was rather challenging to get a hang around. However, I made it through getting used to the scraping and succesfully made the project work, and to get started, I'll show you the flow of how I made this project.

For first part of the project, the main thing was to get a website to scrape from and make the folders and files to contain the code. For the website I've scraped from, I decided to use [Bassmaster's full tournament schedule](https://www.bassmaster.com/bassmaster-tournament-schedule) to scrape from. 

The second part was getting the files needed, such as a readme, gemfile, and a ruby file to get started. To get all these files in one step, I typed the following in to the learn terminal to get all of that in one command:

```
bundle gem bass_tournaments
```

With this one command, getting everything setup was made, and the step after it, was getting the required gem to help scrape the data in place, and that gem was [nokogiri](https://nokogiri.org/). With nokogiri, getting the data parsed seemed easy at first since the table with the data I needed for this project was in seperate colums and rows, and at first it seemed easy to scrape as typing in the following:

```
doc = Nokogiri::HTML(open("https://www.bassmaster.com/bassmaster-tournament-schedule"))
puts doc.css("td.views-field.field-oa-parent-space").text
```

However, this would only give one collection of the text of everything in the colum for the series, and this wouldn't efficently get the information from the other colums. In addition to this, one colum, the location colum didn't have a class/id, so scraping data individually from each colum wasn't the best way to scrape the data.

I've tried many different things to solve this issue, such as using xpath to scrape the data, but that didn't work. Ultimately, my scraper took a different route to scrape the data, so instead of grabbing each colum indiviudally, I stored the entire table and scraped the data from the table by rows like this: 

```
def create_array_of_table_text
    doc = Nokogiri::HTML(open("https://www.bassmaster.com/bassmaster-tournament-schedule"))
    
    # Grab the table from the website
    tournament_table = doc.css("table.views-table.cols-5.table.table-striped.table-bordered")
    
    # Store all the rows in to a variable 
    table_rows = tournament_table.css("tr")
    
    # Store the text from the rows in a nested array 
    # [[first row data], [second row data]]
    
    all_text_from_table = table_rows.collect do |row|
      
      # Go through each of the rows and collect the data as text and store it as a array
      row_text = row.css("td").collect { |data| data.text.strip }
      
      # Place the new array in to the array 
      [*row_text]
    end 
    
    # Return the nested array 
    all_text_from_table
  end 
```

To sum up the code, what it does is store the table, get a second variable to store the rows in to another variable as a collection, then iterate through each row of the table creating an array that holds all of the data from each row as text in to the array, and then return the array at the end of the method. With this method, using the array returned from it allowed me to iterate through the array and create objects for each tournament based on the row's data with the following method:

```
def scrape_tournaments 
    tournament_table_data = create_array_of_table_text
    
    tournament_table_data.each do |table_row_data|
      Tournament.new.create_from_array(table_row_data)
    end 
  end 
```

After successfully scraping the data from the table on the website from that, all that was left was to make a menu to allow the user to see a list of the tournaments and get more details about the tournaments. This ended up being the easiest part of the project for me as it was easy to go through and iterate through the objects created for each tournament to display the data. 

Overall, this was a fun project that made me think about how to scrape data differently instead of calling a .css that would find a element by its class. If I were to add anything else to this project, I'd add another menu that would allow the user to see certain tournaments, whether it be through a month of the tournament or the series type. Another thing I could see to improve upon is going back to change the array storage to instead having everything stored in a hash to spare the program less time to iterate.

If you want to see my project's full source code [click here](https://github.com/OccassionalDev/bass_tournament_cli).






