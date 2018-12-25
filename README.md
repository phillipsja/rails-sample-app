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




