# WEB CRUNCH TWITTER CLONE
- https://web-crunch.com/posts/lets-build-with-ruby-on-rails-a-twitter-clone

- GEMS

```
group :development do
  # Use console on exceptions pages [https://github.com/rails/web-console]
  gem "web-console"
  # Make errors prettier
  gem 'better_errors'
  gem 'binding_of_caller', '~> 1.0'
  # Add speed badges [https://github.com/MiniProfiler/rack-mini-profiler]
  # gem "rack-mini-profiler"

  # Speed up commands on slow machines / big apps [https://github.com/rails/spring]
  # gem "spring"
end
gem 'bulma-rails'
gem 'simple_form'
gem 'gravatar_image_tag'
gem 'devise'
```
- rails g scaffold tweeet tweeet:text
- rails db:migrate
- rails g simple_form:install
- rails g devise:install
- rails g devise:views
- rails g devise User
- rails db:migrate
- rails g migration AddFieldsToUsers name username
- update migration

```
  def change
    add_column :users, :name, :string
    add_column :users, :username, :string
    add_index :users, :username, unique: true
  end
```

- rails db:migrate
- rails g migration  AddUserIdToTweeets user_id:integer
- update app controller

```
class ApplicationController < ActionController::Base
  before_action :configure_permitted_parameters, if: :devise_controller?

  protected

  def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name, :username])
    devise_parameter_sanitizer.permit(:account_update, keys: [:name, :username])
  end
end

```

- added devise rails 7 from : https://dev.to/efocoder/how-to-use-devise-with-turbo-in-rails-7-9n9
- update routes

```
Rails.application.routes.draw do
  devise_for :users
  resources :tweeets
  root "tweeets#index"
end
```

- update app.scss

```

 @import "bulma";

.navbar-brand .title {
	color: white;
}

// round images
.image {
	border-radius: 50%;
	img {
		border-radius: 50%;
	}
}
.notification:not(:last-child) {
	margin-bottom: 0;
}
```

- updated tweets controller

```
  before_action :set_tweeet, only: [:show, :edit, :update, :destroy]
  before_action :authenticate_user!, except: [:index, :show]

  # GET /tweeets
  # GET /tweeets.json
  def index
    @tweeets = Tweeet.all.order("created_at DESC")
    @tweeet = Tweeet.new
  end

  # GET /tweeets/1
  # GET /tweeets/1.json
  def show
  end

  # GET /tweeets/new
  def new
    @tweeet = current_user.tweeets.build
  end

  # GET /tweeets/1/edit
  def edit
  end

  # POST /tweeets
  # POST /tweeets.json
  def create
    @tweeet = current_user.tweeets.build(tweeet_params)

    respond_to do |format|
      if @tweeet.save
        format.html { redirect_to root_path, notice: 'Tweeet was successfully created.' }
        format.json { render :show, status: :created, location: @tweeet }
      else
        format.html { render :new }
        format.json { render json: @tweeet.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /tweeets/1
  # PATCH/PUT /tweeets/1.json
  def update
    respond_to do |format|
      if @tweeet.update(tweeet_params)
        format.html { redirect_to @tweeet, notice: 'Tweeet was successfully updated.' }
        format.json { render :show, status: :ok, location: @tweeet }
      else
        format.html { render :edit }
        format.json { render json: @tweeet.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /tweeets/1
  # DELETE /tweeets/1.json
  def destroy
    @tweeet.destroy
    respond_to do |format|
      format.html { redirect_to tweeets_url, notice: 'Tweeet was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_tweeet
      @tweeet = Tweeet.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def tweeet_params
      params.require(:tweeet).permit(:tweeet)
    end
end
```

- update tweeet.rb

```
class Tweeet < ApplicationRecord
	belongs_to :user
end

```

- update user rb

```
  has_many :tweeets
```

- update layouts app

```

  <body>
  	<% if flash[:notice] %>
  		<div class="notification is-primary global-notification">
  			<p class="notice"><%= notice %></p>
  		</div>
  	<% end %>
  	<% if flash[:alert] %>
  		<div class="notification is-danger global-notification">
  			<p class="alert"><%= alert %></p>
  		</div>
  	<% end %>
  	<nav class="navbar is-info">
  		<div class="navbar-brand">
  		<%= link_to root_path, class: "navbar-item" do %>
  			<h1 class="title is-5">Twittter</h1>
  		<% end %>
			<div class="navbar-burger burger" data-target="navbarExample">
					<span></span>
					<span></span>
					<span></span>
  		</div>
  	 </div>

			<div id="navbarExample" class="navbar-menu">
				<div class="navbar-end">
          <div class="navbar-item">
					<div class="field is-grouped">
						<p class="control">
							<%= link_to 'New Tweeet', root_path, class: "button is-info is-inverted" %>
						</p>
            <% if user_signed_in? %>
              <p class="control">
                <%= link_to current_user.name, edit_user_registration_path, class: "button is-info" %>
              </p>
              <p>
                <%= link_to "Logout", destroy_user_session_path, method: :delete, class:"button is-info" %>
              </p>
            <% else %>
            <p class="control">
              <%= link_to 'Sign In', new_user_session_path, class: "button is-info" %>
            </p>
						<p class="control">
              <%= link_to 'Sign Up', new_user_registration_path, class: "button is-info" %>
            </p>
            <% end %>
            </div>
					</div>
				</div>
			</div>
  	</nav>

    <%= yield %>
  </body>
</html>
```

- update devise new reg
- update devise edit reg
- update devise new session
- update tweet index

```
<section class="section">
  <div class="container">
    <div class="columns">
    	<% if user_signed_in? %>
    		<%= render 'profile' %>
    	<% else %>
       <%= render 'trends' %>
      <% end %>
       <%= render 'feed' %>
       <%= render 'who-to-follow' %>
    </div>
  </div>
</section>
```

- he didnt update show
- update tweet new

```

<div class="section">
	<div class="container">
		<div class="columns is-centered">
			<div class="column is-half">
				<h1 class="title">Create a new Tweeet</h1>
					<%= render 'form', tweeet: @tweeet %>
			</div>
		</div>
	</div>
</div>

<nav class="navbar is-fixed-bottom">
	<div class="navbar-menu">
		<div class="navbar-item">
			<div class="field is-grouped">
				<p class="control">
					<%= link_to 'Cancel', tweeets_path, class: "button is-dark" %>
				</p>
		</div>
	</div>
</nav>
```

- update tweet edit

```
<div class="section">
	<div class="container">
		<div class="columns is-centered">
			<div class="column is-half">
				<h1 class="title">Editing Tweeet</h1>
				<%= render 'form', tweeet: @tweeet %>
			</div>
		</div>
	</div>
</div>

<nav class="navbar is-fixed-bottom">
	<div class="navbar-menu">
		<div class="navbar-item">
			<div class="field is-grouped">
				<p class="control">
					<%= link_to 'Cancel', tweeets_path, class: "button is-dark" %>
				</p>
		</div>
	</div>
</nav>
```

- create the partial tweeets/who-to-follow

```
<div class="column">
	<nav class="panel">
		<p class="panel-heading">Who to Follow</p>
	
	<div class="panel-block">
		<article class="media">
			<div class="media-left">
				<figure class="image is-32x32">
					<img src="https://bulma.io/images/placeholders/64x64.png">
				</figure>
			</div>
			<div class="media-content">
				<div class="content">
					<p>
						<strong>John Smith</strong>
						<small>@johnsmith</small>
					</p>
				</div>
			</div>
		</article>
	</div>
		<div class="panel-block">
		<article class="media">
			<div class="media-left">
				<figure class="image is-32x32">
					<img src="https://bulma.io/images/placeholders/64x64.png">
				</figure>
			</div>
			<div class="media-content">
				<div class="content">
					<p>
						<strong>John Smith</strong>
						<small>@johnsmith</small>
					</p>
				</div>
			</div>
		</article>
	</div>
		<div class="panel-block">
		<article class="media">
			<div class="media-left">
				<figure class="image is-32x32">
					<img src="https://bulma.io/images/placeholders/64x64.png">
				</figure>
			</div>
			<div class="media-content">
				<div class="content">
					<p>
						<strong>John Smith</strong>
						<small>@johnsmith</small>
					</p>
				</div>
			</div>
		</article>
	</div>
	</nav>
</div>
```

- create the partial tweeets/trends.html.erb

```
<div class="column is-one-quarter">
	<nav class="panel">
		<p class="panel-heading">Trends</p>
		<a class="panel-block">
			Trend 1
		</a>
		<a class="panel-block">
			Trend 2
		</a>
		<a class="panel-block">
			Trend 3
		</a>
		<a class="panel-block">
			Trend 4
		</a>
		<a class="panel-block">
			Trend 5
		</a>
		<a class="panel-block">
			Trend 6
		</a>
	</nav>
</div>
```

- create the partial tweeets/profile

```
<div class="column is-one-quarter">
	<nav class="panel">
		<p class="panel-heading">Profile</p>
		<div class="panel-block">
			<article class="media">
				<div class="media-left">
					<figure class="image is-64x64">
						<%= gravatar_image_tag(current_user.email, size: 64, alt: current_user.name) %>
					</figure>
				</div>
				<div class="media-content">
					<div class="content">
						<p>
							<strong><%= current_user.name %></strong><br />
							<small><%= current_user.username %></small>
						</p>
					</div>
				</div>
			</article>
		</div>
		<div class="panel-block">
			<div class="level is-mobile">
				<div class="level-item has-centered-text">
					<div>
						<p class="heading">Tweeets</p>
						<p class="title is-6"><%= current_user.tweeets.count %></p>
					</div>
				</div>
				<div class="level-item has-centered-text">
					<div>
						<p class="heading">Following</p>
						<p class="title is-6">123</p>
					</div>
				</div>
					<div class="level-item has-centered-text">
					<div>
						<p class="heading">Followers</p>
						<p class="title is-6">465K</p>
					</div>
				</div>
			</div>
		</div>
	</nav>
</div>
```

- update tweets form

```

<%= simple_form_for(@tweeet) do |f| %>
<%= f.error_notification %>
<div class="field">
  <div class="control">
    <%= f.input :tweeet, label: "Tweeet about it", input_html: { class: "textarea "}, wrapper: false, label_html: {class: "label"}, placeholder: "Compose a new tweeet...", autofocus: true %>
  </div>
</div>
<%= f.button :submit, class: "button is-info" %>
<% end %>
```

- create the tweeets/feed partial

```
<div class="column is-half">
  <% if user_signed_in? %>
	<article class="media box">
		<figure class="media-left">
			<p class="image is-64x64">
				<%= gravatar_image_tag(current_user.email, size: 64, alt: current_user.name) %>
			</p>
		</figure>
		<div class="media-content">
			 <%= render 'tweeets/form' %>
		</div>
	</article>
  <% end %>
<% @tweeets.each do | tweeet | %>
  <div class="box">
  	<article class="media">
  		<div class="media-left">
  			<figure class="image is-64x64">
  				<%= gravatar_image_tag(tweeet.user.email, size: 64, alt: tweeet.user.name) %>
  			</figure>
  		</div>
  		<div class="media-content">
  			<div class="content">
  				<strong><%= tweeet.user.name %></strong><br />
  				<small><%= tweeet.user.username %></small><br/>
  				<p><%= tweeet.tweeet %></p>
  			</div>
        <% if user_signed_in? && current_user.id == tweeet.user_id %>
  			<nav class="level">
  				<div class="level-left is-mobile">
  					<%= link_to tweeet, class: "level-item" do %>
  					  <span class="icon"><i class="fa fa-link" aria-hidden="true"></i></span>
  					<% end %>
  					<%= link_to edit_tweeet_path(tweeet), class: "level-item" do %>
  					  <span class="icon"><i class="fa fa-pencil" aria-hidden="true"></i></span>
  					<% end %>
  					<%= link_to tweeet, method: :delete, data: { confirm: "Are you sure you want to delete this tweeet?" } do %>
  							<span class="icon"><i class="fa fa-trash-o" aria-hidden="true"></i></span>
  					<% end %>
  				</div>
  			</nav>
        <% end %>
  		</div>
  	</article>
  </div>
<% end %>
</div>
```

- refresh and test it out
## THE END