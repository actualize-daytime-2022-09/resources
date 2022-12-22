# Guide: React deployment with Netlify

## Deploying your frontend

- In your terminal, navigate to your React app and run the following command to configure Netlify redirects:

  ```bash
  echo "/*    /index.html   200" >> public/_redirects
  ```

- Commit and push your code to a GitHub repository:

  ```bash
  git add --all
  git commit -m "Configure Netlify redirects"
  git push origin main
  ```

- Log into www.netlify.com using your GitHub credentials.

- Once you’re logged in, click on the button “New site from Git”

- Click the “GitHub” button and authorize Netlify to access your GitHub when prompted.

- Select your GitHub repository.

- Click the “Deploy” button to build your site.

- Once your site is done building, you’ll see a link to your deployed site. Click on the link to test it out!

_Note that since Netlify is connected to your GitHub account, your app will automatically be redeployed each time you commit your code and run `git push origin main`. Sweet!_

## Configuring your backend

If you are making web requests to a Rails backend, you’ll need to configure the backend and frontend with the appropriate domains:

- In your backend, configure `config/initializers/cors.rb` to accept requests from the frontend domain (`your-frontend-app-name.netlify.com`).

  ```ruby
  Rails.application.config.middleware.insert_before 0, Rack::Cors do
    allow do
      origins "localhost:8080", "your-frontend-app-name.netlify.app"

      resource "*",
        headers: :any,
        methods: [:get, :post, :put, :patch, :delete, :options, :head]
    end
  end
  ```

  _(Save your changes, commit your code, and deploy the changes.)_

- In your frontend, configure your web requests in `src/main.jsx` to use your backend domain (`your-backend-app-name.herokuapp.com`) in production.

  ```js
  import axios from "axios";

  axios.defaults.baseURL =
    process.env.NODE_ENV === "development" ? "http://localhost:3000" : "https://your-backend-app-name.herokuapp.com";
  ```

- Change all your axios requests to use relative urls (`axios.get("/posts.json")` ✅) instead of full urls (`axios.get("http://localhost:3000/posts.json")` ❌)

  _(Save your changes, commit your code, and push the changes to GitHub to trigger the Netlify build.)_
