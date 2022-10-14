### blog_controller.rb:

```ruby
class BlogController < ApplicationController
    def index
        @blogs = Blog.all
    end

    def show
        @blog = Blog.find(params[:id])
    end

    def new
        @blog = Blog.new
    end
    
    def create
        @blog = Blog.create(blog_params)
        if @blog.valid?
            redirect_to blogs_path
        end
    end

    def destroy
        @blog = Blog.find(params[:id])
        if @blog.destroy
            redirect_to blogs_path
        else 
            redirect_to blog_path(@blog)
        end

    end

    def edit
        @blog = Blog.find(params[:id])
    end

    def update
        @blog = Blog.find(params[:id])
        @blog.update(blog_params)
        if @blog.valid?
            redirect_to blog_path(@blog)
        else 
            redirect_to edit_blog_path(@blog)
        end
    end
    
    private
    def blog_params
        params.require(:blog).permit(:title, :content)
    end



end

```

### index.html.erb

```ruby 

<h1> Here are some blogs </h1>
<ul>
 <% @blogs %>
<% @blogs.each do |blog| %>
    <li><%= link_to blog.title, blog_path(blog) %></li>
    <% end %>
    <br />
    </ul> 
    <p> 
    <%= link_to "Add a New Post", new_blog_path %>

    </p>

```

### show.html.erb

```ruby

<h3><%= @blog.title %></h3>
<p> Here is the content of the blog: <%= @blog.content %> </p>
<br />
<%= link_to 'Back to Home', blogs_path %>
<%= link_to 'Edit Post', edit_blog_path %>

<%= link_to "Delete Post", delete_blog_path(@blog), 
data: { 'turbo-method': :delete, 'turbo-confirm': 'Are you sure?' } %>

```

### new.html.erb

```ruby

<%= form_with model: @blog do |form| %>
    <%= form.label :title %>
    <%= form.text_field :title %>
    <%= form.label :content %>
    <%= form.text_field :content %>

<br />
<%= form.submit "Add Post" %>

<% end %>

<%= link_to 'Back to Home', blogs_path %>

```

### edit.html.erb

```ruby

<h1> Edit Post </h1>

<%= form_with model: @blog, method: :patch, local: true do |form| %>
    <%= form.label :title %>
    <%= form.text_field :title %>
    <%= form.label :content %>
    <%= form.text_field :content %>

<br />
<%= form.submit "Edit Post" %>

<% end %>

<%= link_to 'Back to Home', blogs_path %>

```

### routes.rb

```ruby

Rails.application.routes.draw do
  # Define your application routes per the DSL in https://guides.rubyonrails.org/routing.html

  # Defines the root path route ("/")
  # root "articles#index"
  get 'blogs' => 'blog#index'
  # root makes this the landing page
  get 'blogs/new' => 'blog#new', as:'new_blog'
  get 'blogs/:id' => 'blog#show', as:'blog'
  post 'blogs' => 'blog#create'
  delete 'blogs/:id' => 'blog#destroy', as: 'delete_blog'
  get 'blogs/:id/edit' => 'blog#edit', as: 'edit_blog'
  patch 'blogs/:id' => 'blog#update'
  root 'blog#index'


end

```

### schema.rb

```ruby

create_table "blogs", force: :cascade do |t|
    t.string "title"
    t.string "content"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end

```

### blog.rb

```ruby

class Blog < ApplicationRecord
    validates :title, :content, presence: true

    validates :title, uniqueness: true 

    validates :title, length: { minimum: 4}

    validates :content, length: { minimum: 10}
end

```

### application.html.erb

```ruby

<!DOCTYPE html>
<html>
  <head>
    <title>BlogChallenge</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body>
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html>

```

### application.css

```ruby

body {
    background-color:lightgray;
 }

 .container {
    display: flex; 
    flex-direction: column;
    align-items: center;
 }

 ```