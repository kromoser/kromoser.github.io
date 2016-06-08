---
layout: post
title:  "The Exhibitionist, my first Ruby gem"
date:   2016-06-08 14:12:10 +0000
---

I just published [my first ruby gem on RubyGems.org](https://rubygems.org/gems/the-exhibitionist), a simple CLI data gem to view what shows are currently on view at a handful of NYC museums. The program scrapes the museums' websites, figures out each show's dates, and then lists them based on how soon they're closing. That way, if you feel like seeing an art exhibition, but you're not sure where or what you'd like to see, you can make sure you don't miss any interesting show because it's closing tomorrow.

At first, I thought it would be nice to organize the shows by museum, as that's the traditional way you might go about searching for an exhibition to visit, but as I started working, and collecting the closing dates, I realized that I could sort by closing date, and in doing so, provide a more useful, novel way to display the shows. After all, if you knew you wanted to go to the MoMA, you could just go the MoMA's website. Good scraping gems should provide some functionality or utility that can't be derived from just visiting a website yourself.

So, I started by planning my interface and stubbing it out with placeholder data, in the form of strings. I wanted to maintain a working program as I coded, and just have the information become "smarter". The interface greets the user, displays the 10 shows closing soonest, and then asks the user which show she'd like to see. The requested show's details are then displayed, namely what museum the show is in and how many days remain before it closes. It also displays other shows at the same museum. Calculating the number of days left and building a collaborating Museums class were the two most challenging logical aspects of building the gem.

 The actual scraping of the website for shows and their dates was relatively easy. Developers often complain about scraping, but I actually find it a fun challenge, like a little scavenger hunt. For each website's current exhibitions page, I found the shows' html container, and then I iterated into it, creating a new instance of my `Exhibitionist::Shows` class, assigning various attributes through several `attr_accessors`, and adding the show instance to an array containing all shows. 
 
One of the attributes I needed was the exhibition dates, and while each website was different, I was able to to retrieve the shows' closing dates as strings. But in order to sort by number of days left, a string wouldn't work. Luckily, I discovered that Ruby has some wonderful Date methods built in, like the `parse` and `strftime` methods. Using these, I was able to convert my date strings into Date objects, and then all I had to do was subtract today's date from the show's closing date, convert it to an integer, and assign to the show using my `#days_left=` method. Here's an example of scraping the date string and converting it to the number of days left in a show for the Brooklyn Museum scraping method:
 
```
 new_show.dates = show.parent.css("h4")
 # The next line strips away the opening date, leaving only the closing date:
 closing_date_object = Date.parse(new_show.dates.text.strip.gsub(/^.+\–/, ""))
 new_show.closing_date = closing_date_object.strftime("%B %-d, %Y")
 new_show.days_left = (closing_date_object - Date.today).to_i
```

There were just a few edge cases to take care of, like if the show was listed as "Ongoing," which would throw an `ArgumentError` when trying to convert to a Date object, and shows that had ended but were still on the museums' websites (which returns a negative number of days left). I handled these with some simple conditional statements, and then I created my `.sort_by_days_left` method:

```
 def self.sort_by_closing_date
    self.all.sort_by { |show| show.days_left }
 end
 ```
 
The other programming challenge was assigning a museum to each show. At first, I assigned the `#museum=` attribute to each show by creating a new instance of an `Exhibitionist::Museums` class, but I quickly realized that I was creating many Museums objects, all with the same name. To fix this, and make the program much more expandable, I implemented a simple `.find_or_create` method for my Museums class. This way, I could easily add the option to sort shows by museum (a functionality I did end up adding to my CLI), or display other shows at the same museum as a selected exhibition. It would also let me add other functionality to the program. For example, it would be pretty simple to scrape the museums' websites to show hours or contact info when viewing a show's details. 

 That covered most of the heavy lifiting. I polished up the CLI, and then I became a little nervous about the possibility of changes to websites' designs breaking my program. If just one museum changed its site, it could break the whole thing. So, I added some basic error handling in my `.scrape_all` method, which serves as a wrapper for all of my individual scraping methods. If a `StandardError` is raised while scraping a site, it `puts` a simple error message and then removes all faulty instances of the `Shows` class from the `self.all` array. This way, if there's a problem scraping a website, it will skip that site and move on to the next scraping method. Maybe it's a bit overkill, but I also added error handling to the scraper methods themselves, to handle quirky edge cases or mistakes in formatting that I can't foresee. By placing my `begin` `rescue` `end` block within the Shows iteration code, I'm able to only eliminate problematic show instances, without deleting all of a museum's shows.

It's always hard to know when a project like this is done. There are always more attributes one can add, more websites to scrape, more user commands to add, but I finished up with some CLI bug testing, and then I ran `rake package` and `gem push` to upload my gem to RubyGems. I caught a few more little things to change, updated the version, packages it and pushed again, and then repeated that loop again. I guess it really is hard to know when a project is done! But now, I have a working, useful, and expandable gem. I'm sure I'll keep working on it, but I'm glad it's out there, and hopefully some art lovers will find it useful.


