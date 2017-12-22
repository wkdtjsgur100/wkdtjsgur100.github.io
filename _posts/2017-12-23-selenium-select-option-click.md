---
layout: post
section-type: post
title: How to select option in selenium webdriver using python
category: selenium
tags: [ 'selenium' ]
---

At first, I tried to click the option like this:

``` python
select_elem = driver.find_element_by_xpath("//select[@name='" + select_name + "']")
select_elem.click()

option_elem = select_elem.find_element_by_xpath("./option[contains(text(),'" + option_text + "')]")
option_elem.click()
```

or

``` python
select_elem = driver.find_element_by_xpath("//select[@name='" + select_name + "']")

for option in select_elem.find_elements_by_tag_name('option'):
    if option.text == option_text:
        option.click() # select() in earlier versions of webdriver
        break
```

But, click functionality does not work consistently. so, **I changed the code like this:**

``` python
from selenium.webdriver.support.ui import Select

select = Select(driver.find_element_by_xpath("//select[@name='"+select_name+"']"))
select.select_by_visible_text(option_text)
```

It works well!
