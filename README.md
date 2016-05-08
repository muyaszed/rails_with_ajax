# rails_with_ajax

![alt text](https://upload.wikimedia.org/wikipedia/commons/a/a1/AJAX_logo_by_gengns.svg "AJAX logo")

This is my own note which is a step by step on how to implement built-in rails [AJAX](http://guides.rubyonrails.org/working_with_javascript_in_rails.html) in CRUD. For this app, we will create a simple blog to demonstrate AJAX. Anybody is free to use this note for learning and non-commercial purposes.

##Step 1
You must create a new rails application for testing purposes.
```
	$ rails new <your_app_name>
```
and get into your app folder.

##Step 2
You need to scaffold the Post in order to create a basic blog website and dont forget to migrate
```
	$ rails g scaffold Post title:string content:text

```
Now create a hompage and uncomment the root path on your routes file accordingly

##Step 3
In order to make the blog a little bit nicer where we see the AJAX response we need to use [Bootstrap](http://getbootstrap.com/) or other html framework. 

There are many ways to install bootstrap, [this is one of them](https://github.com/seyhunak/twitter-bootstrap-rails)

Open up your gemfile and add

```ruby

	gem 'twitter-bootstrap-rails', :git => 'git://github.com/seyhunak/twitter-bootstrap-rails.git'

```
then on your terminal run

```
	$ bundle install
```
in order to setup bootstrap, you need to run this command
```
	$rails g bootstrap:install static
```
This will include all necessary files from bootstrap to your app.



