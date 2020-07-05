---
layout: post
section-type: post
title: Web Crawling with temporary IP in selenium (Ubuntu 16.04)
category: selenium
tags: [ 'selenium' ]
comments: true
---

> Some sites have a block for specific ip addresses. Here, I am going to detail a process in which we can access these blocked websites through temporary ip from selenium (with the help of Tor).

# environment

Just for your information, here are the programs I used for my test environment.

- Ubuntu 16.04
- selenium 3.8.0
- python 3.6.0
- firefox 57.0.1
- geckodriver 0.19.1

# Crawling with temporary IP address

## FIRST STEP: Install Tor

First,  Let’s install the Tor browser.  

``` shell
sudo apt-get update
sudo apt-get install tor
/etc/init.d/tor restart
```

`netstat -ntlp`, With that command, you can see that the listener created a port at 9050 as you see down below.

``` shell
$ netstat -ntlp
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.1:9050          0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
```

## SECOND STEP: Forward IP address with WebDriver

Let's check a current IP address.

``` python
from selenium import webdriver

driver = webdriver.Firefox()

driver.get('http://icanhazip.com/')

print(driver.page_source)

driver.quit()
```

icanhazip.com is a simple site that I can use to print my current ip address. This code use a page_source attribute from WebDriver.  

We can see the result below. The IP address in this case starts at `13.125`.  

``` python
<html><head><link rel="alternate stylesheet" type="text/css" href="resource://content-accessible/plaintext.css" title="Wrap Long Lines"></head><body><pre>
13.125.XX.XXX
</pre></body></html>
```

Now, we are going to forward the IP using the 9050 port that is from the Tor installation process. 

``` python
from selenium import webdriver


profile = webdriver.FirefoxProfile()
profile.set_preference("network.proxy.type", 1)
profile.set_preference("network.proxy.socks", "127.0.0.1")
profile.set_preference("network.proxy.socks_port", 9050)

profile.update_preferences()

driver = webdriver.Firefox(profile)

driver.get('http://icanhazip.com/')

print(driver.page_source)

driver.quit()
```

The result from this execution is as follows:  

``` shell
. . .  
<span class="cf-footer-item"><span data-translate="your_ip">Your IP</span>:
77.247.181.162
</span>
. . .  
```

My IP address was changed to `77.247.181.162`!


## Forward IP address with RemoteWebDriver

With RemoteWebDriver, You can use WebDriver both remotely and locally in a similar fashion.  
The primary difference is that a remote WebDriver needs to be configured so that it can run your tests on a separate machine.

``` python
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

. . .
# Other parts of code are completely same with ip forwarding in WebDriver
driver = webdriver.Remote('http://127.0.0.1:4444/wd/hub', DesiredCapabilities.FIREFOX, browser_profile=profile)

. . .
```

The difference between the two is the use of a remote WebDriver and its parameters.  