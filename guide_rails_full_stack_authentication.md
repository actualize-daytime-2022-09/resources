# Guide: Rails Full Stack Authentication

## Part 1: Signup (create a new user):

1. In the `Gemfile`, uncomment the `bcrypt` gem (which helps hash passwords):

   ```ruby
   # Use ActiveModel has_secure_password
   gem 'bcrypt', '~> 3.1.7'
   ```

2. In the terminal, run bundle install to install the gem and restart your rails server

   ```
   bundle install
   rails server
   ```

   (Note: If you have issues here, you may need to install xcode command line tools by running the command `xcode-select --install` first, then enter the above commands again..)

3. In the terminal, create a user model:

   ```
   rails generate model User name email password_digest
   rails db:migrate
   ```

4. In `app/models/user.rb`, add the method `has_secure_password` and validations:

   ```ruby
   has_secure_password
   validates :email, presence: true, uniqueness: true
   ```

5. In the terminal, create a users controller:

   ```
   rails generate controller users
   ```

6. In `config/routes.rb`, add the users new and create routes:

   ```ruby
     get "/signup" => "users#new"
     post "/users" => "users#create"
   ```

7. In `app/controllers/users_controller.rb`, add new and create actions:

   ```ruby
      def new
        @user = User.new
        render template: "users/new"
      end

      def create
        @user = User.new(
          name: params[:user][:name],
          email: params[:user][:email],
          password: params[:user][:password],
          password_confirmation: params[:user][:password_confirmation],
        )
        if @user.save
          session[:user_id] = @user.id
          redirect_to "/"
        else
          render :new, status: :unprocessable_entity
        end
      end
   ```

8. Create a new view file `app/views/users/new.html.erb`:

    ```ruby
    <h1>Signup</h1>
    <%= form_with model: @user do |form| %>
      <div>
        <%= form.label :name %>
        <%= form.text_field :name %>
        <% @user.errors.full_messages_for(:name).each do |message| %>
          <div><%= message %></div>
        <% end %>
      </div>
      <div>
        <%= form.label :email %>
        <%= form.email_field :email %>
        <% @user.errors.full_messages_for(:email).each do |message| %>
          <div><%= message %></div>
        <% end %>
      </div>
      <div>
        <%= form.label :password %>
        <%= form.password_field :password %>
        <% @user.errors.full_messages_for(:password).each do |message| %>
          <div><%= message %></div>
        <% end %>
      </div>
      <div>
        <%= form.label :password_confirmation %>
        <%= form.password_field :password_confirmation %>
        <% @user.errors.full_messages_for(:password_confirmation).each do |message| %>
          <div><%= message %></div>
        <% end %>
      </div>
      <div>
        <%= form.submit %>
      </div>
    <% end %>
    ```

## Part 2: Login:

1. In the terminal, create a sessions controller:

   ```
   rails generate controller sessions
   ```

2. In `config/routes.rb`, add the sessions routes:

   ```ruby
     get "/login" => "sessions#new"
     post "/sessions" => "sessions#create"
     get "/logout" => "sessions#destroy"
   ```

3. In `app/controllers/sessions_controller.rb`, add new, create, and destroy actions:

   ```ruby
    def new
      render template: "sessions/new"
    end

    def create
      user = User.find_by(email: params[:email])
      if user && user.authenticate(params[:password])
        session[:user_id] = user.id
        redirect_to "/"
      else
        redirect_to "/login"
      end
    end

    def destroy
      session[:user_id] = nil
      redirect_to "/login"
    end
   ```

4. Create a new view file `app/views/sessions/new.html.erb`:
   
    ```ruby
    <h1>Login</h1>
    <%= form_with method: "post", url: "/sessions" do |form| %>
      <div>
        <%= form.label :email %>
        <%= form.email_field :email %>
      </div>
      <div>
        <%= form.label :password %>
        <%= form.password_field :password %>
      </div>
      <div>
        <%= form.submit "Submit" %>
      </div>
    <% end%>
    ```

## Part 3: Backend user authentication

1. In `app/controllers/application_controller.rb`, add the `current_user` and `authenticate_user` helper methods:

   ```ruby
    def current_user
      @current_user ||= User.find_by(id: session[:user_id]) if session[:user_id]
    end
    helper_method :current_user

    def authenticate_user
      redirect_to '/login', status: :see_other unless current_user
    end
   ```

   This gives all your Rails controllers access to the methods `current_user` and `authenticate_user`.
