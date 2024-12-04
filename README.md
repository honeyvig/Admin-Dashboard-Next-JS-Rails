# Admin-Dashboard-Next-JS-Rails
The scope of this application is primarily the following

Bootstrap UI investor.propstack.ai
- Clone Flowbite codebase
- Implement basic Auth with Auth0
- Get primary navigation and secondary navs & routes
- Vercel build working & linked to investor.propstack.ai domain
- User setting nav and route
- Generic breadcrumb

Bootstrap investor-api.propstack.ai
- Bootstrap a rails api backend on provided AWS account (ask for the account)
- Implement dummy routes for search & reporting data using standard JSON inputs
- Integrate provided Mysql DB and manage migrations and exclude existing tables from migration scripts (ask for the list)

Search Cities
- Search filter UI
- Search query POST to investor-api
- Tabular view of market data, ensure each data field is rendered correctly based on type (here’s the list of the column data)
- Integrate with dummy API

Reporting
- Section UI - 1 column, or 2 column data, with Map, and Table data
- Side nav with all links & routes
- Generic Screeners / Reports UIs
- Integrate with dummy investor-api

Investor Profile
- Link it after signup & through nav
- Construct the Investor profile UI as a form
- Integrate with working investor-api/users/user_id
==============
Here's a high-level implementation plan for your Ruby on Rails and React-based application, including the specified functionalities. The implementation involves the following:
1. Rails API Backend (investor-api.propstack.ai)
Bootstrap the API Backend

    Generate a new Rails API app:

rails new investor-api --api

Install the necessary gems:

    mysql2 for MySQL DB integration.
    auth0 for authentication.
    Any additional libraries for JSON handling.

Configure MySQL:

# config/database.yml
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: 5
  username: <username>
  password: <password>
  host: <hostname>

development:
  <<: *default
  database: investor_api_development

production:
  <<: *default
  database: investor_api_production

Exclude provided tables from migrations:

rails g migration ExcludeTables

Add this to the generated migration:

    class ExcludeTables < ActiveRecord::Migration[6.1]
      def change
        reversible do |dir|
          dir.up do
            execute "SET FOREIGN_KEY_CHECKS = 0;"
            %w[table1 table2].each { |table| execute "DROP TABLE IF EXISTS #{table}" }
            execute "SET FOREIGN_KEY_CHECKS = 1;"
          end
        end
      end
    end

Routes and Dummy Endpoints

Define routes for search and reporting:

# config/routes.rb
Rails.application.routes.draw do
  namespace :api do
    namespace :v1 do
      post 'search', to: 'search#index'
      post 'reports', to: 'reports#index'
    end
  end
end

Create controllers:

rails g controller api/v1/search
rails g controller api/v1/reports

Implement dummy actions:

# app/controllers/api/v1/search_controller.rb
class Api::V1::SearchController < ApplicationController
  def index
    render json: { message: 'Search endpoint placeholder', data: [] }
  end
end

# app/controllers/api/v1/reports_controller.rb
class Api::V1::ReportsController < ApplicationController
  def index
    render json: { message: 'Reports endpoint placeholder', data: [] }
  end
end

2. Frontend (investor.propstack.ai)
Bootstrap with Flowbite

    Clone the Flowbite codebase into a new Next.js app:

npx create-next-app investor --use-npm
cd investor
npm install flowbite flowbite-react

Add Flowbite to the tailwind.config.js:

    module.exports = {
      content: [
        "./pages/**/*.{js,ts,jsx,tsx}",
        "./components/**/*.{js,ts,jsx,tsx}",
        "node_modules/flowbite-react/**/*.{js,jsx,ts,tsx}",
      ],
      plugins: [require("flowbite/plugin")],
    };

Integrate Auth0

    Install Auth0 SDK:

npm install @auth0/auth0-react

Add Auth0 provider in _app.js:

import { Auth0Provider } from '@auth0/auth0-react';

function MyApp({ Component, pageProps }) {
  return (
    <Auth0Provider
      domain="<AUTH0_DOMAIN>"
      clientId="<AUTH0_CLIENT_ID>"
      authorizationParams={{ redirect_uri: window.location.origin }}
    >
      <Component {...pageProps} />
    </Auth0Provider>
  );
}

export default MyApp;

Add authentication in navigation:

    import { useAuth0 } from '@auth0/auth0-react';

    const NavBar = () => {
      const { loginWithRedirect, logout, isAuthenticated } = useAuth0();

      return (
        <nav>
          {isAuthenticated ? (
            <button onClick={() => logout()}>Log out</button>
          ) : (
            <button onClick={() => loginWithRedirect()}>Log in</button>
          )}
        </nav>
      );
    };

Search Cities and Reporting UI

    Build reusable UI components for search filters and reporting:

const SearchFilters = () => {
  return (
    <form>
      <input type="text" placeholder="Search by city" />
      <button type="submit">Search</button>
    </form>
  );
};

Post data to the API:

    const fetchSearchResults = async (query) => {
      const response = await fetch('/api/v1/search', {
        method: 'POST',
        body: JSON.stringify(query),
      });
      const data = await response.json();
      console.log(data);
    };

    Render a map and table: Use libraries like react-leaflet for the map and Flowbite for the table.

3. Vercel and Deployment
Frontend Deployment

    Link the project to Vercel:

    vercel link

    Add the custom domain investor.propstack.ai.

Backend Deployment

    Deploy the Rails API on AWS Elastic Beanstalk or ECS.
    Use nginx to serve requests to the Rails app.

4. Breadcrumb and Navigation

Add breadcrumbs using React Context or state management.

This outline includes the initial implementation and can be extended further based on additional requirements.
