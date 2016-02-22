# Using development

See [Setup a Loomio development environment](setup_development_environment.md) for a step by step guide to setting up your computer to develop on Loomio. If you are familiar with the process of running rails apps you can just fork and clone the repo, `bundle install` then `rake db:setup`.

## Installing the frontend dependencies

The new javascript frontend is a [angular](https://angularjs.org/) project. This is what you need to know:

You'll need gulp installed:

  `$ npm install -g gulp`  

Fetch the npm and bower dependencies:

  from within loomio/angular/

  `$ npm install`

## Trying the new user interface in development mode
Run the rails development server

  `$ rails s`  

Then start the gulp dev task from the loomio/angular folder  

  `$ gulp dev`
  
What this does is reconcatenates your app.js file and other files when you make changes to the source files.

We have links that can setup some fake data and log you in:

  http://localhost:3000/development/start_discussion

See the app/controllers/development_controller.rb for more.

## Updating to the latest loomio code
update your fork from loomio master.
assuming you're working on a branch: new-feature

```
g co master
g pull loomio master
g co new-feature
g merge master
```

We regularly change and update the node modules.
If you just updated and things are broken then you need to

```
cd angular
npm install
```

## Testing
We have rspec and cucumber tests on the rails app.

### Rspec and Cucumber for the rails app
We unit test the rails app with rspec, and have intregration tests of
the rails based UI in cucumber.

We also unit and e2e test the new javascript frontend.

### Jasmine and Protractor for the frontend

You can run the frontend tests with 

`$ gulp protractor`

## Halp! My environment is broken!

What to do when your developer environment breaks:

- restart the gulp dev task
- restart rails
- bundle install; npm install;
- weep softly
- look at the actual error message in rails console or gulp dev terminal windows.
