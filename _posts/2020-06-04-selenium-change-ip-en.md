---
layout: post
section-type: post
title: Web Crawling with temporary IP in selenium (Ubuntu 16.04)
category: selenium
tags: [ 'selenium' ]
comments: true
---

> Some sites are blocking an specific ip address. To avoid this, I wrote about how to crawl some web site with temporary ip in selenium(using [Tor](https://en.wikipedia.org/wiki/Tor_(anonymity_network))

# environment

For your information, Here is my test environment

- Ubuntu 16.04
- selenium 3.8.0
- python 3.6.0
- firefox 57.0.1
- geckodriver 0.19.1

# Crawling with temporary IP address

## FIRST STEP: Install Tor

First, Let's install a Tor  

``` shell
sudo apt-get update
sudo apt-get install tor
/etc/init.d/tor restart
```

`netstat -ntlp`, with this command, you can see listener is created at port 9050 like this:  

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

The site named `icanhazip.com`, which is very simple site to print my current ip address. Printed current ip with page_source attribute in WebDriver.  

The result is like this. The IP address started with `13.125` was allocated in its case.

``` python
<html><head><link rel="alternate stylesheet" type="text/css" href="resource://content-accessible/plaintext.css" title="Wrap Long Lines"></head><body><pre>
13.125.XX.XXX
</pre></body></html>
```

Now, using 9050 port that we opened before in Tor installing process, Let's forward the ip.

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

The execution result is like this.

``` shell
. . .  
<span class="cf-footer-item"><span data-translate="your_ip">Your IP</span>:
77.247.181.162
</span>
. . .  
```

My IP address was changed to `77.247.181.162`!


## Forward IP address with RemoteWebDriver

With RemoteWebDriver, You can use WebDriver remotely the same way you would use it locally. The primary difference is that a remote WebDriver needs to be configured so that it can run your tests on a separate machine.

``` python
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities

. . .
# Other parts of code are completely same with ip forwarding in WebDriver
driver = webdriver.Remote('http://127.0.0.1:4444/wd/hub', DesiredCapabilities.FIREFOX, browser_profile=profile)

. . .
```

This code is only the difference with WebDriver.