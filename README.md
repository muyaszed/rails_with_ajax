# rails_with_ajax

![alt text](https://upload.wikimedia.org/wikipedia/commons/a/a1/AJAX_logo_by_gengns.svg "AJAX logo")

This is my own note which is a step by step on how to implement built-in rails [AJAX](http://guides.rubyonrails.org/working_with_javascript_in_rails.html) in CRUD. For this app, we will create a simple blog to demonstrate AJAX. 
AJAX will be applied when creating new, ecit and delete a post.
Anybody is free to use this note for learning and non-commercial purposes.

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

##Step 4
Now you are going to create the blog index page. You can find the file in /app/vievs/posts/index.html.erb.
Assumming that you are already familiar with bootstrap, you are going to use modal for this blog.

First of all, for simplicity, you want to make the posts#index to be your home page. So we can edit the routes.rb like below. Uncomnent this and change
```ruby

	root 'welcome#index'

```
to this

```ruby
	root 'posts#index'
```
Back to the index page, we need to create a "New Post" button, so delete everything else and insert a bootstrap button with a modal like below
```html
	<div class="container">
	  <div class="row">
	   
	    <div class="col-md-12">
	      
	      <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#mymodal">New Post</button>

	    </div>
	  </div>

	  
	</div>

	<!---Model for new post-->
	<div id="mymodal" class="modal fade" tabindex="-1" role="dialog" aria-labelledby="myLargeModalLabel">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        
      </div>
       <div class="modal-body">
       
      <div class="modal-footer">
        
      </div>
      
    </div>
  </div>
</div>
```
Since you want to see the AJAX output instantly from the index page, you are basically will not required to use new.html.erb to create a new post. This where the modal comes in. You will use the modal as replacement for new.html.erb. So that is why we will put all the new post form inside the modal next.

```html

	<!---Model for new post-->
	<%= form_for(@post) do |p| %>

	  <div id="mymodal" class="modal fade" tabindex="-1" role="dialog" aria-labelledby="myLargeModalLabel">
	  <div class="modal-dialog modal-lg">
	    <div class="modal-content">
	      <div class="modal-header">
	        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
	        <h4 class="modal-title" id="myModalLabel">Create new post</h4>
	      </div>
	       <div class="modal-body">
	       <div class="field">
	            <%= p.label :title %><br>
	            <%= p.text_field :title, class: "form-control post_title" %>
	        </div>
	        <div class="field">
	            <%= p.label :content %><br>
	            <%= p.text_area :content, class: "form-control post_content" %>
	        </div>
	      </div>
	      <div class="modal-footer">
	        <%= submit_tag "Create", class: "btn btn-primary" %>
	      </div>
	      
	    </div>
	  </div>
	</div>
	  
	<% end %>

```
One important thing to remember when you want to create a new object utilizing the action index, you need to add some information inside your posts controller like below to avoid error from the form. 

```ruby

	  def index
	    @posts = Post.all
	    @post = Post.new
	  end
	  
```
Then you need to code how is your posts will be listed on the index page. here you will use partial for the code. Add the rendering code just after the 'New Post' button like below
```ruby
	<div class="container">
	  <div class="row">
	    <div class="col-md-12">
	      <div id="message" class="alert alert-warning alert-dismissible hidden" role="alert">
	        <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
	        
	      </div>

	    </div>
	    <div class="col-md-12">
	      
	      <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#mymodal">New Post</button>

	    </div>
	  </div>

	  <div class="row" id="container_posts">
	    <%=render @posts%>
	  </div>
	</div>
```
next create a file called _post.html.erb in the same folder and add the code below
```html
	<div class="col-md-4 blog-main box text-center " id="post_<%=post.id%>">
		<div class="panel panel-info">
			<div class="panel-heading">
				<h3 class="panel-title"><%= post.title %></h3>
				<p><%=post.content %></p>
			</div>
			<div class="panel-body">

				<div style="color: #3BB2D6;"><%= time_ago_in_words(post.created_at) %></div>
	    
	       
				
			</div><!-- panel body-->
		</div><!--panel-info -->



	</div>


```

now your new post form is ready, you can now click the 'new post' button, a pop up window(modal) will appear showing details for a new post. You can fill everything up and click 'Create', and your new post will be created.

##Step 5

Now to apply AJAX to this process, first edit your posts controller. We need modify the create action to support AJAX
```ruby

	 def create
	    @post = Post.new(post_params)

	    respond_to do |format|
	      if @post.save
	        format.html { redirect_to @post, notice: 'Post was successfully created.' }
	        format.json { render :show, status: :created, location: @post }
	        format.js #add here
	      else
	        format.html { render :new }
	        format.json { render json: @post.errors, status: :unprocessable_entity }
	      end
	    end
	  end

```

Then, in order to prevent the page to refresh when the form in the modal is submitted we need to add the built in helper 'remote: true' to the form
```ruby
	<!---Model for new post-->
<%= form_for(@post, remote: true) do |p| %>

  <div id="mymodal" class="modal fade" tabindex="-1" role="dialog" aria-labelledby="myLargeModalLabel">
  <div class="modal-dialog modal-lg">
  .
  .
  .
  .
```
Finally, you need to create the javascript file which will pull the data from backend and render it to the view. So let cretae a new file in app/views/posts and name the file 

	create.js.erb

Put these code inside the javascript file
```javascript
1	$(document).ready(function() {
2		$("#mymodal").modal('hide');
3		$(".post_title").val('');
4		$(".post_content").val('');
5		$("#container_posts").prepend('<%= j render @post %>');
6		$("#post_<%= @post.id %>").hide().fadeIn(1000);
7
8		$("#new_post").on("ajax:success", function(e, xhr, status, data) {
9
10
11			$("#message").removeClass("hidden").append("You successfully created a post");
12			
13
14		});
15
16
17
18	});
```
1. On line 1, you wait the page to load 100% then we execute the line inside it.
2. On line 2, you need to hide the modal after you click the create buuton when submitting the new post
3. On line 3 and 4, you need to clear the form so next time when you want to create another post, the form will be empty.
4. On line 5, you render the created post pull from backend too the post index page.
5. On line 6, you just create some opacity effect before showing the new post.
6. From line 8 to 14, you are checking the AJAX response and give a message if success.

To display the message you need to create a div where you can render message. In this case, you will create a div with a id name 'message' in post#index

```ruby
	<div class="container">
	  <div class="row">
	    <div class="col-md-12">
	      <div id="message" class="alert alert-warning alert-dismissible hidden" role="alert">
	        <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        
      </div>
      .
      .
      .

```
Now you can create a new post and see the new post will show up immediately without refresing the page

##Step 6

	Next you can repeat the process for "edit" and "delete"










