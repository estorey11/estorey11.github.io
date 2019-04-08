---
layout: post
title:      "Scraping from webpages that load content with AJAX or JavaScript"
date:       2019-04-08 04:12:00 +0000
permalink:  scraping_from_webpages_that_load_content_with_ajax_or_javascript
---


I encountered an interesting problem while trying to scrape data for a CLI Gem that I was building for a Flatiron School project. I was trying to scrape the results data from the table on [this](https://www.wrc.com/en/wrc/results/monte-carlo/stage-times/page/318-226---.html) webpage. When I inspected the elements on the table, I could clearly see the 'td' elements containing the strings of driver names, co-driver names, teams, etc. that I was trying to scrape. However, after locating these nodes using Nokogiri, I found that the text that they contained all looked similar to this:

`[% getCarData(car.entryId).driver.abbvName %]`

It turns out that the site was loading some of its html with AJAX or JavaScript, and OpenURI grabs the html from the page before it has fully been loaded. That's why you can find the driver data when you inspect the table, but not in the html doc that OpenURI grabs.

The easiest solution that I found to deal with this is to use the gem Watir, rather than OpenURI. Watir actually opens a browser window and navigates to the desired page, allowing it to load.

```
browser = Watir::Browser.new :chrome, headless: true
browser.goto 'your url here'
doc = Nokogiri::HTML.parse(browser.html)
browser.close
```

Specifying `headless: true` here stops a physical browser window from opening up, which is nice, but you do still have to wait for the browser to open and the page to load. It's usable because I'm only scraping one page that loads fairly quickly, but I'd imagine it would be pretty cumbersome if you were trying to scrape many more sites.
