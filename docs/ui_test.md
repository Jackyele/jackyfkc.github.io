Physical Layer
--------------

control.py
```python
import functools

class Control(object):
    '''
    To represent an HTML element
    '''
    def __init__(self, htmltag):
        self.tag = htmltag

class Link(Control):
    def __init__(self, htmltag):
        super(Link, self).__init__(htmltag)

    def click(self):
        self.tag.click()

class Edit(Control):
    def __init__(self, htmltag):
        super(Edit, self).__init__(htmltag)
        self.content = None

    @property
    def value(self):
        return self.tag.text

    @value.setter
    def value(self, value):
        self.tag.send_keys(value)

class Button(Control):
    def __init__(self, htmltag):
        super(Button, self).__init__(htmltag)

    def click(self):
        self.tag.click()


# The following decorators make the writting simple
def edit(sig):
    def inner(func):
        @functools.wraps(func)
        def wrapper(self, *args, **kwargs):
            tag = self.get_element(sig)
            return Edit(tag)
        return property(wrapper)
    return inner

def button(sig):
    def inner(func):
        @functools.wraps(func)
        def wrapper(self, *args, **kwargs):
            tag = self.get_element(sig)
            return Button(tag)
        return property(wrapper)
    return inner

def link(sig):
    def inner(func):
        @functools.wraps(func)
        def wrapper(self, *args, **kwargs):
            tag = self.get_element(sig)
            return Link(tag)
        return property(wrapper)
    return inner
```

page.py
```python
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait

class Page(object):
    '''
    To represent an HTML page
    '''
    def __init__(self, browser):
        self.browser = browser

    @property
    def current_url(self):
        return self.browser.current_url

    def execute_script(self, code):
        return self.browser.execute_script(code)

    def wait_until(self, method, message=None, maxtimeout=15):
        '''
        Calls the method provided with the driver as an argument
         until the return value to be true.
        '''
        WebDriverWait(self.browser, maxtimeout).until(method, message)

    def get_element(self, locator, maxtimeout=30):
        '''
        A better approach to retrieve HTML element, to wait the element
        appeared in given time
        '''
        return WebDriverWait(self.browser, maxtimeout).until(EC.presence_of_element_located(locator))
```

Logical Layer
-------------

app.py - To represent the whole applicaton, which will be responsible for starting the browser app
```python
import logging

from selenium import webdriver

log = logging.getLogger(__name__)

class App(object):
    '''
    To represent the entire application
    '''
    browser = None

    def __init__(self, url, options=None):
        if not options:
            options = webdriver.ChromeOptions()
        options.add_argument('-start-maximized')

        log.info('Start the application...')
        self.browser = webdriver.Chrome(chrome_options=options)
        self.browser.set_page_load_timeout(30)
        self.browser.get(url)
```
