# Guide: Rails Full Stack CRUD practice

1.  Start with a clean slate

    - Close all your text editor windows, quit your text editor.
    - Find and kill any terminal Rails servers using Ctrl C.
    - Open Terminal and navigate to your Actualize folder.

2.  In your terminal, create a new rails project:

    ```
    rails new name_of_your_app --no-api
    ```

3.  In the terminal, go to your new directory

    ```
    cd name_of_your_app
    ```

4.  In the terminal, create the database by entering:

    ```
    rails db:create
    ```

5.  In the terminal, create a new controller by entering:

    ```
    rails generate controller Photos
    ```

6.  In the terminal, create a new model by entering:

    ```
    rails generate model Photo name:string width:integer height:integer
    ```

7.  In the terminal, enter the command to open your text editor:

    ```
    code .
    ```

8.  In your text editor, double check the generated migration code in the `db/migrate` folder

    - If you have any typos, you can fix them now

9.  In the terminal, run the migration code by entering:

    ```
    rails db:migrate
    ```

10. In your text editor, use the `rails console` or the `db/seeds.rb` file to create new items:

    ```
    Photo.create(name: "Winter", width: 200, height: 150)
    Photo.create(name: "Family", width: 1024, height: 768)
    ```

11. In the terminal, run the code in the seeds file by entering:

    ```
    rails db:seed
    ```

12. In the terminal, open the irb rails console by entering:

    ```
    rails console
    ```

13. In the terminal (irb rails console), you can double check your entries by entering:

    ```
    Photo.all
    ```

14. In your terminal (irb rails console), exit the rails console by entering:

    ```
    exit
    ```

15. In the terminal, start your server by entering:

    ```
    rails server
    ```

16. In the terminal, open a new tab by entering the shortcut Command T
    (or use the menu Shell -> New Tab)

17. In `config/routes.rb`, use the [resources shortcut](https://guides.rubyonrails.org/getting_started.html#resourceful-routing) to define all the routes

    ```ruby
    resources :photos

    # Automatically generates the following routes
    # get "/photos" => "photos#index", as: "photos"
    # get "/photos/new" => "photos#new", as: "new_photo"
    # post "/photos" => "photos#create"
    # get "/photos/:id" => "photos#show", as: "photo"
    # get "/photos/:id/edit" => "photos#edit", as: "edit_photo"
    # patch "/photos/:id" => "photos#update"
    # delete "/photos/:id" => "photos#destroy"
    ```

18. Make the [index action](https://gist.github.com/peterxjang/657be7f14974ec79217904f46c746a2e)

19. Make the [show action](https://gist.github.com/peterxjang/a32ef0e6a7231621137b0afff343cda7)

20. Make the [new/create actions](https://gist.github.com/peterxjang/bd31797a9b81c0bf9e6d655804865c2c)

21. Make the [edit/update actions](https://gist.github.com/peterxjang/b478ae23b35bf5ad1246749ee827a62e)

22. Make the [destroy action](https://gist.github.com/peterxjang/1bdc821c5563fb3d4291e40f041ffce6)

## Optional steps
- In `app/controllers/photos_controller.rb`, use strong parameters to DRY up the new and create actions ([example](https://guides.rubyonrails.org/getting_started.html#using-strong-parameters))

- In `app/models/photo.rb`, add validations, then in `app/controllers/photos_controller.rb`, add happy/sad paths, then in `app/views/new.html.erb`, display validation errors ([example](https://guides.rubyonrails.org/getting_started.html#validations-and-displaying-error-messages))

- In `app/views/new.html.erb` and `app/views/edit.html.erb`, use view partials to DRY up the form ([example](https://guides.rubyonrails.org/getting_started.html#using-partials-to-share-view-code))
