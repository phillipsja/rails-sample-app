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

Summarized Chpt. 3

-For a third time, we went through the full procedure of creating a new Rails application from scratch, installing the necessary gems, pushing it up to a remote repository, and deploying it to production.
-The rails script generates a new controller with rails generate controller ControllerName <optional action names>.
-New routes are defined in the file config/routes.rb.
-Rails views can contain static HTML or embedded Ruby (ERb).
-Automated testing allows us to write test suites that drive the development of new features, allow for confident refactoring, and catch regressions.
-Test-driven development uses a “Red, Green, Refactor” cycle.
-Rails layouts allow the use of a common template for pages in our application, thereby eliminating duplication.

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

-Here's the summary from the end of the chapter: 
-Ruby has a large number of methods for manipulating strings of characters.
-Everything in Ruby is an object.
-Ruby supports method definition via the def keyword.
-Ruby supports class definition via the class keyword.
-Rails views can contain static HTML or embedded Ruby (ERb).
-Built-in Ruby data structures include arrays, ranges, and hashes.
-Ruby blocks are a flexible construct that (among other things) allow natural iteration over enumerable data structures.
-Symbols are labels, like strings without any additional structure.
-Ruby supports object inheritance.
-It is possible to open up and modify built-in Ruby classes.
-The word “deified” is a palindrome.

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

`#` for single-line comment

#4.2.2 Strings

String interpolation "#{var}"
```
>> first_name = "Michael"    # Variable assignment
=> "Michael"
>> "#{first_name} Hartl"     # String interpolation
=> "Michael Hartl"
```

Print string (in console) using `puts`

Ruby won't interpolate single-quoted strings, but otherwise they are equivalent? 

```
>> '#{foo} bar'     # Single-quoted strings don't allow interpolation
=> "\#{foo} bar"
```

#4.2.3 Objects and Message Passing

Ruby uses `?` as a convention on methods that return a bool, e.g. 
```
s = "foo"
s.empty? 
=> false
```

Use elsif for "else if" 

i.e. 
```
if empty? 
"something"
elsif s.include("foo")? 
"whatever"
end

to string method
```
.to_s
```
check for nil method
```
.nil? 
```
s

odd use of if, follows statement
```
x = "something"
puts "x is not empty" if !x.empty?
```

unless does the same thing
```
>> string = "foobar"
>> puts "The string '#{string}' is nonempty." unless string.empty?
The string 'foobar' is nonempty.
=> nil
```

#4.2.4 Note on Methods

Note that Ruby functions have an implicit return, meaning they return the last statement evaluated

So notice in the method there is no return keyword: 


```
>> def string_message(the_function_argument = '')
>>   if the_function_argument.empty?
>>     "It's an empty string!"
>>   else
>>     "The string is nonempty."
>>   end
>> end
```


Or in the case of our sample app's full_title "custom helper"

```
module ApplicationHelper

  # Returns the full title on a per-page basis.       # Documentation comment
  def full_title(page_title = '')                     # Method def, optional arg
    base_title = "Ruby on Rails Tutorial Sample App"  # Variable assignment
    if page_title.empty?                              # Boolean test
      base_title                                      # Implicit return
    else
      page_title + " | " + base_title                 # String concatenation
    end
  end
end
```	

#4.3.1 Arrays and Ranges


`.split`
splits on white space by default into array
`.split`('x') or you can split on arbitrarily defined delimiter

Some other array methods
.empty?
.include? # should be .includes? if you ask me
.sort
.reverse
.shuffle

! will mutate array
e.g. 
a = [1,2,3]
a.shuffle!
a
=> [1,3,2]

`<<` ~ "shovel operator" is equivalent to push
a.push(4) ~ a<<4


"ranges" are like arrays
```
>> a = %w[foo bar baz quux]         # Use %w to make a string array.
=> ["foo", "bar", "baz", "quux"]
>> a[0..2]
=> ["foo", "bar", "baz"]
```

-1 allows selecting end element in a range
```
>> a = (0..9).to_a
=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>> a[2..(a.length-1)]               # Explicitly use the array's length.
=> [2, 3, 4, 5, 6, 7, 8, 9]
>> a[2..-1]                         # Use the index -1 trick.
=> [2, 3, 4, 5, 6, 7, 8, 9]
>> a[-1]
=> 9
```

Ranges work with characters
```
>> ('a'..'e').to_a
=> ["a", "b", "c", "d", "e"]
```

#4.3.2 Blocks

```
(1..5).each { |i| puts 2 * i }
```

The vertical bars identify the block variable

Use the do-end convention for multi-line blocks: 

```
>> (1..5).each do |i|
?>   puts 2 * i
>> end
2
4
6
8
10
=> 1..5
```

#4.3.3 Hashes & Symbols

Recall my first introduction to hashes was in Perl (i.e. associative array) 
and later "hash set" in "C#"

Hashes are indicated with curly braces instead of brackets

```
my_hash1 = {}
my_hash2 = { "party" => "b-hole", "bigtime" => "oh-yeah"}
```

Symbols: 

You can think of symbols as basically strings without all the extra baggage

i.e.
```
>> "name".split('')
=> ["n", "a", "m", "e"]
>> :name.split('')
NoMethodError: undefined method `split' for :name:Symbol
```

Not sure what the point of hashes are, but apparently they are heavily used,
As are hashes-of-hashes: 

```
>> params = {}        # Define a hash called 'params' (short for 'parameters').
=> {}
>> params[:user] = { name: "Michael Hartl", email: "mhartl@example.com" }
=> {:name=>"Michael Hartl", :email=>"mhartl@example.com"}
>> params
=> {:user=>{:name=>"Michael Hartl", :email=>"mhartl@example.com"}}
>>  params[:user][:email]
=> "mhartl@example.com"
```

Because it’s so common for hashes to use symbols as keys, as of version 1.9 Ruby 
supports a new syntax just for this special case (i.e. key:"value"); I guess the 
the takeaway is don't be fooled into thinking this construct is the one like 
in javascript? 

Unfortunately, this can be confusing, especially since :name is valid on its own (as a standalone symbol) but 
	name: has no meaning by itself. 
The bottom line is that :name => and name: are effectively the same only inside literal hashes

```
h2 = { name: "Michael Hartl", email: "michael@example.com" }
```

`inspect`  returns a string with a literal representation of the object it’s called on:
```
>> puts (1..5).to_a            # Put an array as a string.
1
2
3
4
5
>> puts (1..5).to_a.inspect    # Put a literal array.
[1, 2, 3, 4, 5]
>> puts :name, :name.inspect
name
:name
>> puts "It worked!", "It worked!".inspect
It worked!
"It worked!"
```

using inspect to print an object is common enough that there’s a shortcut for it, the p function
```
>> p :name             # Same output as 'puts :name.inspect'
:name
```

Exercises: 

1) Define a hash with the keys 'one', 'two', and 'three', and the values 'uno', 'dos', and 'tres'. 
Iterate over the hash, and for each key/value pair print out "'#{key}' in Spanish is '#{value}'".
```
espanol1 = { one: "uno", two: "dos", three: "tres" }
espanol1.each do |key, value|
  puts "'#{key}' in Spanish is '#{value}'"
end
```

2) Create three hashes called person1, person2, and person3, with first and last names 
under the keys :first and :last. Then create a params hash so that params[:father] is person1, 
params[:mother] is person2, and params[:child] is person3. Verify that, for example, 
params[:father][:first] has the right value.

```
person1 = { first: "Bob", last:"Jones"}
person2 = { first: "Mary", last:"Jones"}
person3 = { first: "Joey", last:"Jones"}

params = {}
params[:father] = person1
params[:mother] = person2
params[:child] = person3

params[:father][:first]
``` 

#4.3.4 CSS link

Recall this in the layout: 

```
<%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
```


Q: So if stylesheet_link_tag is a function, where the parantheses? 
A: In Ruby, they are optional, so the following are equal: 

```
# Parentheses on function calls are optional.
stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'
stylesheet_link_tag('application', media: 'all', 'data-turbolinks-track': 'reload')
```

Q: Note the media argument looks like a hash, but where are the curly braces? 
A: When hashes are the last argument in a function call, the curly braces are optional, 
so these two are equivalent:

```
# Curly braces on final hash arguments are optional.
stylesheet_link_tag 'application', { media: 'all', 'data-turbolinks-track': 'reload' }
stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'
```

#4.4 Ruby Classes

Literal Constructor equivalent to named constructor: 

```
>> s = "foobar"       # A literal constructor for strings using double quotes
=> "foobar"
>> s.class
=> String
>> s = String.new("foobar")   # A named constructor for a string
=> "foobar"
>> s.class
=> String
>> s == "foobar"
=> true
```

Same with arrays: 
```
>> a = Array.new([1, 3, 2])
=> [1, 3, 2]
```


But hashes are different: 

Hash.new takes a default value for the hash, which is the value of the hash for a nonexistent key: 
```
>> h = Hash.new
=> {}
>> h[:foo]            # Try to access the value for the nonexistent key :foo.
=> nil
>> h = Hash.new(0)    # Arrange for nonexistent keys to return 0 instead of nil.
=> {}
>> h[:foo]
=> 0
```

#4.4.2 Class Inheritance

.superclass method: 
```
>> s = String.new("foobar")
=> "foobar"
>> s.class                        # Find the class of s.
=> String
>> s.class.superclass             # Find the superclass of String.
=> Object
>> s.class.superclass.superclass  # Ruby has a BasicObject base class as of 1.9
=> BasicObject
>> s.class.superclass.superclass.superclass
=> nil
```

Some class syntax examples

Create a class: 

```
class Word
  def palindrome?(string)
    string == string.reverse
  end
end
=> :palindrome?
```

Instantiate class: 
```
w = Word.new              # Make a new Word object.
=> #<Word:0x22d0b20>
w.palindrome?("foobar")
=> false
w.palindrome?("level")
=> true
```

Add a method onto the String class (Word inherits String) just like that, 
note that `this` in Ruby is `self`? : 
```
class Word < String             # Word inherits from String.
  # Returns true if the string is its own reverse.
  def palindrome?
    self == self.reverse        # self is the string itself.
  end
end
=> nil
```

#4.4.3 Modifying built-in classes

Ruby classes can be opened and modified, allowing ordinary 
mortals such as ourselves to add methods to them:


```
class String
  # Returns true if the string is its own reverse.
  def palindrome?
    self == self.reverse
  end
end
=> nil
"deified".palindrome?
=> true
```

How in the heck does this work: 

```
>> class String
>>   def shuffle
>>     self.?('').?.?
>>   end
>> end
>> "foobar".shuffle
=> "borafo"
```

#4.4.5 A User Class (that will be used in the Sample App)

```
class User
  attr_accessor :name, :email

  def initialize(attributes = {})
    @name  = attributes[:name]
    @email = attributes[:email]
  end

  def formatted_email
    "#{@name} <#{@email}>"
  end
end
```

`attr_accessor :name, :email` creates "getter" and "setter" methods for in
stance vars name/email. 
Instance variables always begin with an @ sign, and are nil when undefined.

initialize method is kind of like a constructor afaict

Note that the instance variables are available in the formatted_email method

Usage (using rails console): 
```
require './example_user'     # This is how you load the example_user code.
=> true
example = User.new
=> #<User:0x224ceec @email=nil, @name=nil>
example.name                 # nil since attributes[:name] is nil
=> nil
example.name = "Example User"           # Assign a non-nil name
=> "Example User"
example.email = "user@example.com"      # and a non-nil email address
=> "user@example.com"
example.formatted_email
=> "Example User <user@example.com>"
```

Also recall that, 
we can omit the curly braces for final hash arguments

So we can omit the curly braces when initializing a new user: 
```
user = User.new(name: "Michael Hartl", email: "mhartl@example.com")
=> #<User:0x225167c @email="mhartl@example.com", @name="Michael Hartl">
```


#5 Filling in the Layout

Summary: 

-Using HTML5, we can define a site layout with logo, header, footer, and main body content.
-Rails partials are used to place markup in a separate file for convenience.
-CSS allows us to style the site layout based on CSS classes and ids.
-The Bootstrap framework makes it easy to make a nicely designed site quickly.
-Sass and the asset pipeline allow us to eliminate duplication in our CSS while packaging up the results efficiently for production.
-Rails allows us to define custom routing rules, thereby providing named routes.
-Integration tests effectively simulate a browser clicking from page to page.

#5.1.2 Add bootstrap sass and some custom css

Note that the added file "custom.scss" is a sass file (added by me) and will be included in the 
"asset pipeline" by default (by nature of the location ~/app/assets/stylesheets) 

#5.1.3 Partials

convention is to name partials with leading underscore (e.g. _mypartial.erb)
include and render them in a view like this: 
`<%= render 'layouts/shim' %>`

#5.2 Sass and the asset pipeline

The Asset Pipeline: 
- asset directories 
- manifest files
- preprocessor engines

Asset Directories
- app/assets - specific to app? 
- lib/assets - written by team
- vendor/assets - 3pl

each of these in turn contain: 
- images
- javascripts
- stylesheets

Use manifest files to describe how to combine .css into single file. 
I don't really understand the example: 
"The manifest file for app-specific CSS, located @
app/assets/stylesheets/application.css
```
/*
 .
 .
 .
 *= require_tree .
 *= require_self
*/
```

Basically, the directives at the bottom are read-in even though it is commented out? 
`require_tree . `
"ensures that all CSS files in the app/assets/stylesheets 
directory (including the tree subdirectories) are included into the application CSS"

`require_self`
specifies where in the loading sequence the CSS in application.css itself gets included

Here's a link to the docs: 

Here's some more info specific to the above: 
"
The require_tree directive in a CSS manifest works the same way as the JavaScript one, 
requiring all stylesheets from the current directory.

In this example, require_self is used. This puts the CSS contained within the 
file (if any) at the precise location of the require_self call."

you can specify individual files and they are compiled in the order specified. For example, you might concatenate three CSS files together this way:

/* ...
*= require reset
*= require layout
*= require chrome
*/

So I guess yeah, the comments stay? 

Preprocess engines: 

1) ERB
2) Coffee Script
3) Sass

You can chain preprocessor directives: 

`foobar.js.erb.coffee`

gets run through both CoffeeScript and ERb (with the code running from right to left, i.e., CoffeeScript first).

#5.2.2 Sass

"Syntactically Awesome Stylesheets" 

Improvement on CSS, top three improvements: 

1) Nesting
2) Variables
3) Mixins

Nesting

Here's a clear example: 

In css: 
```
.center {
  text-align: center;
}

.center h1 {
  margin-bottom: 10px;
}
```

Refactored in Sass: 
```
.center {
  text-align: center;
  h1 {
    margin-bottom: 10px;
  }
}
```


Here's another example: 
```
#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
}

#logo:hover {
  color: #fff;
  text-decoration: none;
}
```
In order to nest the second rule, 
we need to reference the parent 
element #logo; in SCSS, this is accomplished 
with the ampersand character & as follows:

```
#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
  &:hover {
    color: #fff;
    text-decoration: none;
  }
}
```

Variables

You can create your own using `$`, e.g. 

$light-gray: #777

You can also use the fact that bootstrap defines [less variables](https://getbootstrap.com/docs/3.3/customize/#less-variables) for all
kinds of stuff and those variables can be used since bootstrap-sass gem provides the Sass equivalents. 

e.g. our "$light-gray" variable is defined in bootstrap sass as
`@gray-light`
So we could just use
`$gray-light`

Using these techniques, we can refactor the custom.scss file: 
```
@import "bootstrap-sprockets";
@import "bootstrap";

/* universal */

body {
  padding-top: 60px;
}

section {
  overflow: auto;
}

textarea {
  resize: vertical;
}

.center {
  text-align: center;
}

.center h1 {
  margin-bottom: 10px;
}

/* typography */

h1, h2, h3, h4, h5, h6 {
  line-height: 1;
}

h1 {
  font-size: 3em;
  letter-spacing: -2px;
  margin-bottom: 30px;
  text-align: center;
}

h2 {
  font-size: 1.2em;
  letter-spacing: -1px;
  margin-bottom: 30px;
  text-align: center;
  font-weight: normal;
  color: #777;
}

p {
  font-size: 1.1em;
  line-height: 1.7em;
}

/* header */

#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
}

#logo:hover {
  color: #fff;
  text-decoration: none;
}

/* footer */

footer {
  margin-top: 45px;
  padding-top: 5px;
  border-top: 1px solid #eaeaea;
  color: #777;
}

footer a {
  color: #555;
}

footer a:hover {
  color: #222;
}

footer small {
  float: left;
}

footer ul {
  float: right;
  list-style: none;
}

footer ul li {
  float: left;
  margin-left: 15px;
}
```
to
```
@import "bootstrap-sprockets";
@import "bootstrap";

/* mixins, variables, etc. */

$gray-medium-light: #eaeaea;

/* universal */

body {
  padding-top: 60px;
}

section {
  overflow: auto;
}

textarea {
  resize: vertical;
}

.center {
  text-align: center;
  h1 {
    margin-bottom: 10px;
  }
}

/* typography */

h1, h2, h3, h4, h5, h6 {
  line-height: 1;
}

h1 {
  font-size: 3em;
  letter-spacing: -2px;
  margin-bottom: 30px;
  text-align: center;
}

h2 {
  font-size: 1.2em;
  letter-spacing: -1px;
  margin-bottom: 30px;
  text-align: center;
  font-weight: normal;
  color: $gray-light;
}

p {
  font-size: 1.1em;
  line-height: 1.7em;
}


/* header */

#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: white;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
  &:hover {
    color: white;
    text-decoration: none;
  }
}

/* footer */

footer {
  margin-top: 45px;
  padding-top: 5px;
  border-top: 1px solid $gray-medium-light;
  color: $gray-light;
  a {
    color: $gray;
    &:hover {
      color: $gray-darker;
    }
  }
  small {
    float: left;
  }
  ul {
    float: right;
    list-style: none;
    li {
      float: left;
      margin-left: 15px;
    }
  }
}
```


#5.3 Named routes

For a link like
```
<a href="/static_pages/about">About</a>
```

in Rails we use a named route, which is defined somewhere obviously: 
```
<%= link_to "About", about_path %>
```

So to illustrate this, the contact page is being referenced (which was added
as an exercise in an earlier chapter), here are the things to add when 
adding a view? 

1) Create Test in test/controllers/static_pages_controller_test.rb
2) Add a route in config/routes.rb
3) Add action in app/controllers/static_pages_controller.rb
4) Add view @ app/views/static_pages/contact.html.erb

#5.3.2 Routes

Defined in Routes file (config/routes.rb)	

First a review of the three ways shown so far to define a root route (which I don't remember): 
root 'application#hello' (in hello world application)
root 'users#index'       (also in hello world application)
root 'static_pages#home' (in toy app)

Here's the conversion...

```
  #get 'static_pages/help'
  get 'help', to: 'static_pages#help'
  #get 'static_pages/about'
  get 'about', to: 'static_pages#about'
  #get 'static_pages/contact'
  get 'contact', to: 'static_pages#contact'
```  

This new pattern routes a GET request for the URL /help to the help action in the Static Pages controller. 
As with the rule for the root route, this creates two named routes. 

Now, update partials to use named routes. 

#5.3.4 Layout Link Tests

So we just changed a bunch of links. We could test it all manually, or we could
write a test to do it for us. 

we will scaffold a test template called site_layout: 

```
rails generate integration_test site_layout
```

Cool, this generates a folder under "test" called "integration" and a filed
calld "site_layout_tests.rb" with a skeleton ready for writing tests! 

Are test is checking the named routes: 
```
  test "layout links" do
    get root_path
    assert_template 'static_pages/home'
    assert_select "a[href=?]", root_path, count: 2
    assert_select "a[href=?]", help_path
    assert_select "a[href=?]", about_path
    assert_select "a[href=?]", contact_path
  end
  ```
  
Here's some other parameters to the assert_select, this looks like a selector syntax: 
```
assert_select "div"	<div>foobar</div>
assert_select "div", "foobar"	<div>foobar</div>
assert_select "div.nav"	<div class="nav">foobar</div>
assert_select "div#profile"	<div id="profile">foobar</div>
assert_select "div[name=yo]"	<div name="yo">hey</div>
assert_select "a[href=?]", '/', count: 1	<a href="/">foo</a>
assert_select "a[href=?]", '/', text: "foo"	<a href="/">foo</a>

```

Here's how to run the integration tests: 
```
rails test:integration
```


Exercise: 

It’s convenient to use the full_title helper in the tests by including the Application helper 
into the test helper, as shown in Listing 5.35. We can then test for the right title using 
code like Listing 5.36. This is brittle, though, because now any typo in the base title 
(such as “Ruby on Rails Tutoial”) won’t be caught by the test suite. Fix this problem by 
writing a direct test of the full_title helper, which involves creating a file to test 
the application helper and then filling in the code indicated with FILL_IN in Listing 5.37. 
(Listing 5.37 uses assert_equal <expected>, <actual>, which verifies that the expected result
 matches the actual value when compared with the == operator.)
 
 
 I don't get this line: 
 
 "This is brittle, though, because now any typo in the base title 
(such as “Ruby on Rails Tutoial”) won’t be caught by the test suite." 

Why not? 
 

use the title_helper we wrote earlier in the integration test. 

First we need to include the "Application Helper" in the tests. 
So add `include ApplicationHelper` in test/test_helper.rb

Next, in the site_layout_test file we add: 
```
    get contact_path
    assert_select "title", full_title("Contact")
```
  
Now, create a new file in test/helpers/application_helper_test.rb
and replace FILL_IN
```
require 'test_helper'

class ApplicationHelperTest < ActionView::TestCase
  test "full title helper" do
    assert_equal full_title,         FILL_IN
    assert_equal full_title("Help"), FILL_IN
  end
end
```

#5.4 User Signup

Will create a new route for new user signup, new

Use rails generate to build scaffold: 

```rails generate controller Users new```

So recall that arguments arger the controller name, Users, are actions that we can setup
right-away, 0 or many. 









