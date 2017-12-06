# Rail-REST-API-Guide
Guide to make REST API with rails

## References
  * (https://scotch.io/tutorials/build-a-restful-json-api-with-rails-5-part-one)

## Dependencies  
  * [rspec-rails](https://github.com/rspec/rspec-rails)
  * [factory_bot_rails](https://github.com/thoughtbot/factory_bot_rails)
  * [shoulda_matchers](https://github.com/thoughtbot/shoulda-matchers)
  * [database_cleaner](https://github.com/DatabaseCleaner/database_cleaner)
  * [faker](https://github.com/stympy/faker)

## Step
### 1. Create project
```bash
  rails new <project-name> --api -T --database=postgresql
  # -T to skip test file, use rspec instead.
```
### 2. Adding dependencies
```ruby
# Gemfile
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
end
# ...
group :test do
  gem 'shoulda-matchers'
  gem 'faker'
  gem 'databae-cleaner'
end
```
#### Install gem.
```bash
  bundle install
```
#### Initialize the spec directory.
```bash
  rails g rspec:install
```
#### Create a **factories** directory for factory bot.
```bash
  mkdir spec/factories
```
#### Configuration
```ruby
  # require database cleaner at the top level
  require 'database_cleaner'

  # [...]
  # configure shoulda matchers to use rspec as the test framework and full matcher libraries for rails
  Shoulda::Matchers.configure do |config|
    config.integrate do |with|
      with.test_framework :rspec
      with.library :rails
    end
  end

  # [...]
  RSpec.configuration do |config|
    # [...]
    # add `FactoryBot` methods
    config.include FactoryBot::Syntax::Methods

    # start by truncating all the tables but then use the faster transaction strategy the rest of the time.
    config.before(:suite) do
      DatabaseCleaner.clean_with(:truncation)
      DatabaseCleaner.strategy = :transaction
    end

    # start the transaction strategy as examples are run
    config.around(:each) do |example|
      DatabaseCleaner.cleaning do
        example.run
      end
    end
    # [...]
  end
```

## 3. Create Model
### Generate model
```bash
  rails g model <model-name> <...attributes>
```
### Migrate
```bash
  rails db:migrate
```

### Write model spec
```ruby
# spec/models/<model-name>_spec.rb

RSpec.describe <model-name>, type: :model do
  # Association test example
  it { should have_many(:some_models_1).dependent(:destroy) } # 1:m rel with Some model
  it { should belong_to(:some_models_2) } # m:1 rel with Some model
  
  # Validation test example (verify presence of data before saving)
  it { should validate_presence_of(:some_attribute) }
  
```
### Running test
```bash
  bundle exec rspec
```
### Fixing Model file
Fixing model file to pass the test.
