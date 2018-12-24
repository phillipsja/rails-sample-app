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