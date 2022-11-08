# Guide: Rails API CRUD practice

## Create a new Rails app
1.  Start with a clean slate

    - Close all your text editor windows, quit your text editor.
    - Find and kill any terminal Rails servers using Ctrl C.
    - Open Terminal and navigate to your Actualize folder.

2.  In your terminal, create a new rails project:

    <pre><code>rails new <ins><strong>name-of-your-app</strong></ins></code></pre>

3.  In the terminal, go to your new directory

    <pre><code>cd <ins><strong>name-of-your-app</strong></ins></code></pre>

4.  In the terminal, create the database by entering:

    ```
    rails db:create
    ```

5. In the terminal, start your server by entering:

    ```
    rails server
    ```

> In your browser, visit http://localhost:3000 to make sure your app is working

## Create the data layer
1. In the terminal, open a new tab by entering the shortcut Command T
    (or use the menu Shell -> New Tab)
    
2.  In the terminal, create a new model by entering:

    <pre><code>rails generate model <ins><strong>Photo</strong></ins> <ins><strong>name</strong></ins>:string <ins><strong>width</strong></ins>:integer <ins><strong>height</strong></ins>:integer</code></pre>

3.  In the terminal, enter the command to open your text editor:

    ```
    code .
    ```

4.  In your text editor, double check the generated migration code in the `db/migrate` folder

    - If you have any typos, you can fix them now

5.  In the terminal, run the migration code by entering:

    ```
    rails db:migrate
    ```

6. In your text editor, use the `rails console` or the `db/seeds.rb` file to create new items:

    <pre><code>
    <ins><strong>Photo</strong></ins>.create(<ins><strong>name: "Winter", width: 200, height: 150</strong></ins>)
    <ins><strong>Photo</strong></ins>.create(<ins><strong>name: "Family", width: 1024, height: 768</strong></ins>)</code></pre>

7. In the terminal, run the code in the seeds file by entering:

    ```
    rails db:seed
    ```

> In the terminal, open the irb rails console by entering:
>
>   ```
>   rails console
>   ```
>
> In the terminal (irb rails console), you can double check your entries by entering:
> 
> <pre><code><ins><strong>Photo</strong></ins>.all</code></pre>
> 
> In your terminal (irb rails console), exit the rails console by entering:
> 
>  ```
>   exit
>   ```

## Create the web layer
      
1.  In the terminal, create a new controller by entering:

    <pre><code>rails generate controller <ins><strong>Photos</strong></ins></code></pre>

2. Make the **INDEX** action
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        get "/photos" => "photos#index"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>photos</strong></ins>_controller.rb</code>, add an index action</summary>
  
      ```ruby
        def index
          photos = Photo.all
          render json: photos.as_json
        end
      ```
  
      </details>

      > Test your code using a `requests.http` file with the following
      > ```
      > ### Photos Index
      > GET http://localhost:3000/photos.json
      > ```
      > 
      > You can also visit <code>localhost:3000/<ins><strong>photos</strong></ins>.json</code> in the browser to see the output

3. Make the **CREATE** action
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        post "/photos" => "photos#create"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>photos</strong></ins>_controller.rb</code>, add a create action</summary>
  
      ```ruby
        def create
          photo = Photo.create(
            name: params[:name],
            width: params[:width],
            height: params[:height],
          )
          render json: photo.as_json
        end
      ```
  
      </details>

      > Test your code using a `requests.http` file with the following
      > ```
      > ### Photos Create
      > POST http://localhost:3000/photos.json
      > Content-Type: application/json
      >
      > {
      >   "name": "Test name",
      >   "width": 640,
      >   "height": 480
      > }
      > ```
      > 
      > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
 
4. Make the **SHOW** action
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        get "/photos/:id" => "photos#show"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>photos</strong></ins>_controller.rb</code>, add a show action</summary>
  
      ```ruby
        def show
          photo = Photo.find_by(id: params[:id])
          render json: photo.as_json
        end
      ```
  
      </details>

      > Test your code using a `requests.http` file with the following
      > ```
      > ### Photos Show
      > GET http://localhost:3000/photos/1.json
      > ```
      > 
      > You can also visit <code>localhost:3000/<ins><strong>photos</strong></ins>/<ins><strong>1</strong></ins>.json</code> in the browser to see the output

5. Make the **UPDATE** action
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        patch "/photos/:id" => "photos#update"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>photos</strong></ins>_controller.rb</code>, add an update action</summary>
  
      ```ruby
        def update
          photo = Photo.find_by(id: params[:id])
          photo.update(
            name: params[:name] || photo.name,
            width: params[:width] || photo.width,
            height: params[:height] || photo.height,
          )
          render json: photo.as_json
        end
      ```
  
      </details>

      > Test your code using a `requests.http` file with the following
      > ```
      > ### Photos Update
      > PATCH http://localhost:3000/photos/1.json
      > Content-Type: application/json
      >
      > {
      >   "name": "New name"
      > }
      > ```
      > 
      > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)

6. Make the **DESTROY** action
    - <details><summary>In <code>config/routes.rb</code>, add a route</summary>
  
      ```ruby
        delete "/photos/:id" => "photos#destroy"
      ```
  
      </details>

    - <details><summary>In <code>app/controllers/<ins><strong>photos</strong></ins>_controller.rb</code>, add a destroy action</summary>
  
      ```ruby
        def destroy
          photo = Photo.find_by(id: params[:id])
          photo.destroy
          render json: { message: "Photo destroyed successfully" }
        end
      ```
  
      </details>

      > Test your code using a `requests.http` file with the following
      > ```
      > ### Photos Destroy
      > DELETE http://localhost:3000/photos/1.json
      > ```
      > 
      > You CANNOT test this in the browser address bar (since the browser defaults to a GET request)
