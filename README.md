# README

This is a cryptocurrency watchlist application built with Ruby on Rails.
CoinRocket as it is - perfect for anyone looking to get a 360 overview on the crypto markets.

## Top Features

- Live prices, list top 200+ cryptocurrencies by market cap.
- Search and filter cryptocurrencies by name, market cap, and rank.
- Users can create an account and log in to the application.
- Users can view the details of a cryptocurrency.
- Users can add cryptocurrencies to their watchlist.
- Users can CRUD crytocurrencies from their watchlist.

## How to build the application

### 1. Gathering and Storing Data

#### 1.1 Datasets

- [CoinRanking API](https://api.coinranking.com/v2/coins).It is a free API that provides cryptocurrency data.
- [Faker Gem](https://github.com/faker-ruby/faker). It is used to generate fake users.

#### 1.2 Database ERD Diagram

![image.png](https://s2.loli.net/2023/07/07/DCW964w2BqjHRgV.png)

_Auto-generated by [RailsERD](https://voormedia.github.io/rails-erd/)_

**User Table**

| Column Name | Data Type |
| ----------- | --------- |
| email       | string    |
| password    | string    |

**Cryptocurrency Table**

| Column Name     | Data Type |
| --------------- | --------- |
| uuid            | string    |
| symbol          | string    |
| name            | string    |
| color           | string    |
| icon_url        | string    |
| market_cap      | string    |
| price           | string    |
| listed_at       | integer   |
| tier            | integer   |
| change          | string    |
| rank            | integer   |
| sparkline       | text      |
| low_volume      | boolean   |
| coinranking_url | string    |
| volume_24h      | string    |

**Watchlist Table**

| Column Name | Data Type  |
| ----------- | ---------- |
| user_id     | references |
| name        | string     |

**CryptocurrenciesWatchlists** (Join Table)

| Column Name       | Data Type  |
| ----------------- | ---------- |
| cryptocurrency_id | references |
| watchlist_id      | references |

#### 1.3 AR Models

Create the models and migrations for the tables above.

```bash

rails generate model Cryptocurrency uuid:string symbol:string name:string color:string icon_url:string market_cap:string price:string listed_at:integer tier:integer change:string rank:integer sparkline:text low_volume:boolean coinranking_url:string 24h_volume:string

rails generate model Watchlist user:references name:string

rails generate model CryptocurrenciesWatchlists cryptocurrency:references watchlist:references
```

Use Devise to generate the User model.

```bash
rails generate devise:install
rails generate devise User
rails generate devise:views
```

Run the migrations.

```bash
rails db:migrate
```

#### 1.4 Model Associations and Validations

```ruby
class User < ApplicationRecord

 has_many :watchlists
 has_many :cryptocurrencies, through: :watchlists

devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
end


class Cryptocurrency < ApplicationRecord
  has_many :cryptocurrencies_watchlists
  has_many :watchlists, through: :cryptocurrencies_watchlists

  validates :name, presence: true
  end

class Watchlist < ApplicationRecord
  belongs_to :user
  has_many :cryptocurrencies_watchlists, dependent: :destroy
  has_many :cryptocurrencies, through: :cryptocurrencies_watchlists

  validates :user_id, presence: true
  validates :name, presence: true
end

class CryptocurrenciesWatchlist < ApplicationRecord
  belongs_to :cryptocurrency
  belongs_to :watchlist

  validates :cryptocurrency_id, presence: true
  validates :watchlist_id, presence: true
end
```

#### 1.5 Seed Script

```bash
rails db:seed
```

![Alt text](image-1.png)

### 2. Web Site Navigation -- Done

app/controllers/home_controller.rb
app/views/page/about.html.erb
app/views/home/\_footer.html.erb
app/views/home/\_header.html.erb

config/routes.rb

```ruby
  get 'home/about', to: 'home#about'
  root 'home#index'
```

### 3 - Data Navigation

#### 3.1 List all the cryptocurrencies in a table

`app/views/cryptocurrencies/index`

path:
`cryptocurrencies_path `

#### 3.2 Display cryptocurrecy infomation by :id

`app/views/cryptocurrencies/_cryptocurrency.html.erb`
`app/views/cryptocurrencies/show.html.erb`

#### 3.3 Multi-model Data on Member Pages

On one user's watchlist page, show all followed coins.

#### 3.4 Hierarchical Collection Navigation

Coins page -> Coin details page -> Add to watchlist -> Watchlist page
-> Show all coins in the watchlist -> Show all watchlists

#### Pagination

Use the [kaminari gem](https://github.com/kaminari/kaminari) to add pagination to the index page.

### 4 Searching and Filtering

Easy to search and filter cryptocurrencies by name and rank.

Search a cryptocurrency by name, symbol in Top100 or All or Potential coins.

### 5 Markup and Design

Bootstrap 5
Erb Conditional (if user is logged in, show the watchlist)
HTML Validation

#### 6 Source Control

Use Git and GitHub to track changes and manage version control.
