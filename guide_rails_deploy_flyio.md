# Guide: Rails deployment with Fly.io

## Install fly.io

- Install the command line tools with homebrew
  ```bash
  brew install flyctl
  ```
- Create a fly.io account
  ```bash
  fly auth signup
  ```

## Deploy a rails app

- Create a new fly.io app within the rails app

  ```bash
  fly launch
  ```

  - The terminal will prompt you with some questions:
    - **App name:** (press Return to use the default name)
    - **Select region:** (type in city near you and press Return)
    - **Would you like to set up a Postgresql database now?** (type y and press Return)
    - **Select configuration:** (select Development and press Return)

- Edit `lib/tasks/fly.rake` in your text editor

  - Remove the code `=> assets:precompile` in the build step if you're using Rails API mode
  - Change the release task to do both migrate and seed

  ```diff
    # commands used to deploy a Rails application
    namespace :fly do
      # BUILD step:
      #  - changes to the filesystem made here DO get deployed
      #  - NO access to secrets, volumes, databases
      #  - Failures here prevent deployment
  -   task :build => 'assets:precompile'
  +   task :build

      # RELEASE step:
      #  - changes to the filesystem made here are DISCARDED
      #  - full access to secrets, databases
      #  - failures here prevent deployment
  -   task :release => "db:migrate"
  +   task :release => ["db:migrate", "db:seed"]

      # SERVER step:
      #  - changes to the filesystem made here are deployed
      #  - full access to secrets, databases
      #  - failures here result in VM being stated, shutdown, and rolled back
      #    to last successful deploy (if any).
      task :server do
        sh "bin/rails server"
      end
    end
  ```

- Deploy the code

  ```bash
  fly deploy
  ```

- Change the release task in `lib/tasks/fly.rake` back to only migrate instead of migrate and seed

  ```diff
      # RELEASE step:
      #  - changes to the filesystem made here are DISCARDED
      #  - full access to secrets, databases
      #  - failures here prevent deployment
  -   task :release => ["db:migrate", "db:seed"]
  +   task :release => ["db:migrate"]
  ```

  - _Note: Normally you can seed the database using the command `fly ssh console -C "app/bin/rails db:seed"`. However, the free plan of fly.io doesn't have enough memory to run this command consistently._

- Open the app in the browser
  ```bash
  fly open
  ```
  Visit an existing url (like `/products.json`)

## Other

- To list all fly.io apps

  ```bash
  fly list apps
  ```

- To access the postgres database using SQL commands

  - Connect to the fly postgres server

    ```bash
    fly postgres connect -a <app-name>-db
    ```

  - List the databases

    ```bash
    \l
    ```

  - Connect to a specific database (replace `<database-name>` with one listed from the previous command)

    ```bash
    \c <database-name>
    ```

  - Run a SQL query (replace posts with one of your table names)

    ```sql
    SELECT * FROM posts;
    ```

- To reset a fly.io postgres database

  - In `db/seeds.rb`, add the following code at the top (to reset each table without dropping the entire database)

    ```ruby
    ActiveRecord::Base.establish_connection
    ActiveRecord::Base.connection.tables.each do |table|
      next if table == "schema_migrations"
      ActiveRecord::Base.connection.execute("TRUNCATE #{table}")
      ActiveRecord::Base.connection.reset_pk_sequence! table
    end
    ```

  - In `lib/tasks/fly.rake`, change the release task to do both migrate and seed

    ```diff
        # RELEASE step:
        #  - changes to the filesystem made here are DISCARDED
        #  - full access to secrets, databases
        #  - failures here prevent deployment
    -   task :release => ["db:migrate"]
    +   task :release => ["db:migrate", "db:seed"]
    ```

  - In the terminal, run the following command to deploy the code

    ```bash
    fly deploy
    ```

  - In `lib/tasks/fly.rake`, change the release task back to only migrate instead of migrate and seed

    ```diff
        # RELEASE step:
        #  - changes to the filesystem made here are DISCARDED
        #  - full access to secrets, databases
        #  - failures here prevent deployment
    -   task :release => ["db:migrate", "db:seed"]
    +   task :release => ["db:migrate"]
    ```

- To destroy a fly.io app

  ```bash
  fly apps destroy <app-name>
  ```
