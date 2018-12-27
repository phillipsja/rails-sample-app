# rails-sample-app

[*Ruby on Rails Tutorial* book](http://www.railstutorial.org/book).

#Setting up Repo

#create rails app
>rails _5.1.6_ new sample_app
edit "Gemfile" if needed
>bundle install --without production
>bundle update

Setup Git

[Use this](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)
as a reference. 

But don't forget these steps: 

>git init
>git add . 
>git commit -m "msg"
setup git repo and copy link
>git remote add origin remote repository URL
>git push origin master

Next, migrate the database:

```
$ rails db:migrate
```

Finally, run the test suite to verify that everything is working correctly:

```
$ rails test
```

If the test suite passes, you'll be ready to run the app in a local server:

```
$ rails server
```

Then just to make heroku is setup..

>heroku create
>git push heroku master

...

#3.2.1 Generating Controllers & Actions with Scaffolding

using `generate controller`, create StaticPages controller with home & help "actions"
```
rails generate controller StaticPages home help
```

Note again, that this automatically creates help & home routes in the routes file, 
along with of course the controller, StaticPages and also views for home/help. 

FYI, to undo a command (and all the files and edits it does), use destroy, i.e.
```
rails destroy  controller StaticPages home help
```

Or for a model, these two cancel each other: 

```
rails generate model User name:string email:string
rails destroy model User
```

...And with migrations (Migrations change the state of the database using the command)
```
rails db:migrate
rails db:rollback
```
or you can specify rollback to version: 
```
rails db:migrate VERSION=0
```

#3.3.1 Testing

Generating controllers generates test files, e.g.
test/controllers/static_pages_controller_test.rb
for the StaticPages controller

Check out the default test for the home/help actions: 

``
require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest

  test "should get home" do
    get static_pages_home_url
    assert_response :success
  end

  test "should get help" do
    get static_pages_help_url
    assert_response :success
  end
end
```

To run the test...
```
rails test
```

#3.3.2 "Red, Green, Refactor" cycle

We are going to create a new controller, for an "About" page. 
But first, write a test
Just like the others, add test to test/controllers/static_pages_controller_test.rb


```
  test "should get about" do
    get static_pages_about_url
    assert_response :success
  end
```

Of course the test fails, so first add new route to 
config/routes.rb

```
get  'static_pages/about'
```

followed by a corresponding action in app/controllers/static_pages_controller.rb
```
  def about
  end
```

...and finally, create the view app/views/static_pages/about.html.erb


Note: in 
```
  test "should get about" do
	get static_pages_about_url
	assert_response :success
  end
```

the indentation, and the do-end wrappter around test

#3.4 "Slightly Dynamic Pages" 

The `rails new` command creates a layout page by default: 
app/views/layouts/application.html.erb

We gonna use this to display the title in the child view pages. 
Not sure why we are doing this to the default layout file
 `mv app/views/layouts/application.html.erb layout_file`

But in the TDD approach, write some tests. New command: 

`assert_select` 

this is a selector (like jquery) that we can incorporate into tests: 

e.g. 

`assert_select "title", "Home | Ruby on Rails Tutorial Sample App"`

So this is literally (well, "Home", "Help" and "About" changes), 
what we add to each test in: 

`test/controllers/static_pages_controller_test.rb`

You could add the title tag w/string to each template, but skip that...

...

Note, `setup` method is run first in every test
So, instance variable: 
def setup
    `@base_title = "Ruby on Rails Tutorial Sample App"`
then use it in tests: 

```
  test "should get home" do
    get static_pages_home_url
    assert_response :success
    assert_select "title", "Home | #{@base_title}"
  end
```

#3.4.3 Embedded Ruby wink(.erb) and Layouts

use `provide` function to set a different title

```
<% provide(:title, "Home") %>
<!DOCTYPE html>
<html>
  <head>
    <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
  </head>
```  

Note the templating delimitef `<% ... %>`
and `<%= ... %>`

So again, you could change all the pages to use this, but this section is supposed
to be on layouts so...


So put the layout file back to its default (I guess this interferred with running the 
tests as some kind of illustration: 

`mv layout_file app/views/layouts/application.html.erb`

So replace: 
`    <title>SampleApp</title>`
with 
`	<title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>`
	
Oh, yeah, each view, will need to have the provide funtion with the appropriate 
value for title in it for this to work, e.g.: 

```<% provide(:title, "Home") %>```
	
#3.4.4 Setting the root route

Hasn't this already been covered? 

Question, what's the difference between `/` and `#` in:
```
  get 'static_pages/help'
  root 'static_pages#home'
```

#3.6 Advance Test Setup

install gem for showing color in win32 console: 
`gem install win32console`

Also add to test_helper.rb to make use of minitest reporters gem: 

```
require "minitest/reporters"
Minitest::Reporters.use!
```

...some problem with above, didn't work to show color

#3.6.2 Automated Test Monitoring using "Guard"

Guard Gem was already installed, so just need to initialize:
`bundle exec guard init`


#4 Rails-Flavored-Ruby

Talking about the layout that we looked at earlier, this line in particular: 

`<%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>`

`[stylesheet_link_tag](https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-stylesheet_link_tag)` is a build in ruby function
e.g.

```
stylesheet_link_tag "style"
# => <link href="/assets/style.css" media="screen" rel="stylesheet" />
```

Ruby ideas to be covered: 
Ruby ideas: 
- built-in Rails methods (or custom helpers) 
- method invocation with missing parentheses 
- symbols 
- hashes


e.g. custom helper in the context of the page title we earlier, i.e. 
in the template: 
```<%= yield(:title) %> | Ruby on Rails Tutorial Sample App```
in the view: 
```<% provide(:title, "Home") %>```

What if the view was missing the provide statement? 

Custom Helper: 

Define a custom helper called full_title. The full_title helper returns a base title, 
“Ruby on Rails Tutorial Sample App”, if no page title is defined, and adds a vertical 
bar preceded by the page title if one is defined: 

```
module ApplicationHelper

  # Returns the full title on a per-page basis.
  def full_title(page_title = '')
    base_title = "Ruby on Rails Tutorial Sample App"
    if page_title.empty?
      base_title
    else
      page_title + " | " + base_title
    end
  end
end
```

So in the page replace: 
`<title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>`
with 
`<title><%= full_title(yield(:title)) %></title>`

Now update tests and remove the provide in Home page view

#4.2 strings and methods

`rails console`

starts dev console (as opposed to test or prod)

[Ruby Docs](http://ruby-doc.org/)

									   

