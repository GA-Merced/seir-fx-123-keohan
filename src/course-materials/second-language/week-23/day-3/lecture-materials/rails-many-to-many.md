---
track: "Second Language"
title: "Rails Many to Many Relationships"
week: 2
day: 3
type: "lecture"
---



# Rails Many to Many Relationships


<br>
<br>
<br>

## Roadmap

<!-- _after this lesson, students will be able to:_ -->

* Establish a many-to-many relationship between models
* Make a join table
* Make a model associated with a join table

<br>
<br>
<br>


### Resource Trading App

In `student_examples`

We're going to make a trading app where many traders can buy and sell commodities.


<br>
<br>
<br>


### Create app

- `mkdir trading_app`
- `cd trading_app`

Create a new Rails app called `trading_app_api`, and don't forget to set up the `postgresql` db, or to add `--skip-git`, `--skip-active-storage`, and `--api`.

<details><summary>Full Command</summary>

```bash
rails new trading_app_api --api -d postgresql --skip-git --skip-active-storage
```

</details>

Go into the project directory

```bash
rails db:create
```


<br>
<br>
<br>


### Make two basic models with full CRUD

Let's make two full and independent models first. Later, we will relate them together.

**Traders** and **Commodities**.

Each has a name (string) and a numerical field.

We are going to set some restrictions on the decimal field to sanitize it for currency. `{8.2}` will generate some constraints that we will see in the code.

```bash
rails g scaffold trader name money:decimal{8.2}
```

```bash
rails g scaffold commodity name price:decimal{8.2}
```

The decimal field will precision of 8 which means 8 digits max, and a scale of 2 which means 2 digits after the decimal point.

```ruby
class CreateTraders < ActiveRecord::Migration[6.0]
  def change
    create_table :traders do |t|
      t.string :name
      t.decimal :money, precision: 8, scale: 2

      t.timestamps
    end
  end
end
```

```ruby
class CreateCommodities < ActiveRecord::Migration[6.0]
  def change
    create_table :commodities do |t|
      t.string :name
      t.decimal :price, precision: 8, scale: 2

      t.timestamps
    end
  end
end
```

```bash
rails db:migrate
```

Schema should look like this:

```ruby
ActiveRecord::Schema.define(version: 2018_01_10_205013) do

  # These are extensions that must be enabled in order to support this database
  enable_extension "plpgsql"

  create_table "commodities", force: :cascade do |t|
    t.string "name"
    t.decimal "price", precision: 8, scale: 2
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "traders", force: :cascade do |t|
    t.string "name"
    t.decimal "money", precision: 8, scale: 2
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end
```

Oops! If you messed up a scaffold, enter the commands below and create the scaffold again.

```bash
rails db:rollback
rails destroy scaffold traders
```
<br>
<br>
<br>


### Seed data

>**Note:** we are seeding Carl sagan with one more digit than scale allows. What happens when we seed it?

>**Note:** Krang's number is too long. What error do we get when we run `rails db:seed`? How might we fix it?

**seeds.rb**

```ruby
Trader.create([
  { name: 'Slimer', money: 1000 },
  { name: 'Skeletor', money: 1000 },
  { name: 'Dame Edna', money: 1000 },
  { name: 'Cousin It', money: 1000 },
  { name: 'Boomhauer', money: 1000 },
  { name: 'Molly Ringwald', money: 1000 },
  { name: 'Vander Von Odd', money: 1000 },
  { name: 'James A. Garfield', money: 1000 },
  { name: 'Krang', money: 1234567.89 },
  { name: 'Carl Sagan', money: 1000.539 }
])

Commodity.create([
  { name: "Oil", price: 53.28 },
  { name: "Gold", price: 1216.70 },
  { name: "Natural Gas", price: 3.175 },
  { name: "Copper", price: 2.654 },
  { name: "Gasoline", price: 1.6 },
  { name: "Silver", price: 17.95 },
  { name: "Wheat", price: 428.2 },
  { name: "Coffee", price: 153.20 },
  { name: "Lumber", price: 325.50 },
  { name: "Milk", price: 16.77 },
])

puts "Seeded database"
```

```bash
rails db:seed
```

Start the Rails server and open in browser:

`localhost:3000/traders`

![screenshot](https://i.imgur.com/WiJ04c5.png)

`localhost:3000/commodities`

![screenshot](https://i.imgur.com/dvXe062.png)

<br>
<br>
<br>


### Many-to-many

**Traders** and **Commodities** are two independently existing models.

These models will remain independent -- to relate **traders** and **commodities** we will not tie them together with foreign key columns.

We have **traders** and we have **commodities**. What would be a suitable relationship if a trader wanted to be associated with commodities that the trader owns?

We could do a regular one-to-many, a *has many* and *belongs to*.

With a one to many, a commodity with a foreign key could only be associated with one trader. One number in the column, one association.

What if we want it so that traders could take from a pool of commodities? A commodity could belong to many traders, and a trader could have many different commodities?

A commodity would need a way to keep track of all its traders, OR, a trader would need a way to keep track of all its commodities . . . The foreign keys for each would have to be an array. An array of trader ids or an array of commodity ids.

Since we are not storing array data, we need a better solution.

<details><summary><b>Click for Answer</b></summary>

A Join Table.

![screenshot](https://i.imgur.com/vxqyUqp.png)

We can make a **third table** to map the relationships between traders and commodities.

According to this table, Trader 5 _and_ Trader 9 each have Commodity 5, and Commodities 2 _and_ 1 are owned by Trader 2.

</details>

**Many commodities can be associated with many traders and vice versa.**

* How many commodities does trader 5 own? (look in the traders column)

* How many traders own commodity 1? (look in the commodities column)

<br>
<br>
<br>



We can make a join table a couple of ways.

* `has_many_and_belongs_to` is the simpler relation. It makes an anonymous table and looks exactly like the image above. The table itself can't be expanded. For that it would need to have its own model. The docs recommend using a more complicated relationship in case you want to expand the table in the future.

* `has_many :through` makes a new model for the join table. This way, you can name the join table and add columns and validation to the table if you need to. We will use this way. This is also known as a join model.

**Construct a new resource called Ledger.**

Our ledger will be its own model, and it will map the relationship between Traders and Commodities just like the table above.

Since it will be its own model, we can add extra columns to the table. Let's add a **qty** column to our ledger to document _how many_ of a given commodity a trader owns.

**qty** is the information _about_ the relationship.

>Table for a **Ledger model** with an extra column for qty.

![screenshot](https://i.imgur.com/6VMhd1p.png)

We can **scaffold** the Ledger and provide foreign-key columns to the table that are associated with our traders and commodities.

## Make join model

```bash
rails g scaffold ledger trader:references commodity:references qty:integer
```

Migration file:

```ruby
class CreateLedgers < ActiveRecord::Migration[6.0]
  def change
    create_table :ledgers do |t|
      t.references :trader, foreign_key: true
      t.references :commodity, foreign_key: true
      t.integer :qty

      t.timestamps
    end
  end
end
```

**references** means that the column will be given an `_id` suffix column which holds an integer as a foreign key. It will add relational integrity with sql `REFERENCES`, and indexing.

* `rails db:migrate`

resulting ledgers schema:

```ruby
  create_table "ledgers", force: :cascade do |t|
    t.bigint "trader_id"
    t.bigint "commodities_id"
    t.integer "qty"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.index ["commodities_id"], name: "index_ledgers_on_commodities_id"
    t.index ["trader_id"], name: "index_ledgers_on_trader_id"
  end
```

Don't worry that it looks a little funny. Just imagine it's doing the same thing as if the foreign key columns were just integers.

**Side note:** You can see the SQL commands that were executed during migrations in

> `log/development.log`

* Open the log file and watch it as you migrate the file

![screenshot](https://i.imgur.com/bmncS2R.png)

<br>
<br>
<br>



### Rails Console Queries

* We can also install a gem to make Rails console look nicer.

Gemfile

```ruby
gem 'awesome_print'
gem 'rb-readline'
```

* Run `bundle`

* Run `rails c`

[Alternative to official docs console guide](https://pragmaticstudio.com/tutorials/rails-console-shortcuts-tips-tricks)

Now you can use `ap <query>` to get nicer output. Example `ap Trader.all` or `ap Commodity.all`

![screenshot](https://i.imgur.com/NV2fJwu.png)

* Query for all Ledgers (there should be none) `ap Ledger.all`
* Query for all column names `ap Ledger.column_names`

![screenshot](https://i.imgur.com/QigilLt.png)

We can see that the 'references' type created columns called `trader_id` and `commodities_id`.

With three models, **Trader**, **Commodity**, **Ledger** interacting with each other, there are a few variations of ActiveRecord commands we can perform now.

We have a few traders and commodities created already. Let's create an **association** between a trader and a commodity by creating a ledger.

* Create a Ledger (omit `ap` if not using awesome_print):

```ruby
ap Ledger.create(trader_id: 1, commodity_id: 1, qty: 1)
```

![screenshot](https://i.imgur.com/cOyUaaa.png)

This creates a relationship between trader 1 and commodity 1 (it's the only relationship we created so far).

Let's also create a relationship between trader 2 and the same commodity (1):

```ruby
ap Ledger.create(trader_id: 2, commodity_id: 1, qty: 1)
```

* See all ledgers:

```ruby
ap Ledger.all
```

![screenshot](https://i.imgur.com/L5DF1wB.png)

Looking at this, we can deduce that we ought to be able to query for:

* Trader 1 and see Trader 1's commodity, Commodity 1.
* Trader 2 and see Trader 2's commodity, Commodity 1. (Both Trader 1 and Trader 2 have commodity 1)
* Commodity 1, and see Commodity 1's traders, Trader 1 and Trader 2.

Try to see Trader 1's commodities

```ruby
ap Trader.find(1).commodities
```

Unless Active Record relations have been set, we will get

> NoMethodError: undefined method `commodities' for #<Trader:0x007f95e8ca4420>


<br>
<br>
<br>

### Add Active Record relations

Add many-to-many through relations to the two main models:

**commodity.rb**

```ruby
class Commodity < ApplicationRecord
  has_many :ledgers
  has_many :traders, through: :ledgers
end
```

**trader.rb**

```ruby
class Trader < ApplicationRecord
  has_many :ledgers
  has_many :commodities, through: :ledgers
end
```

A Trader or a Commodity will have many ledgers associated with them.

A Trader or a Commodity also have many of each other, and they do so through the ledgers.

Leave the Ledger model exactly how the scaffold made it:

```ruby
class Ledger < ApplicationRecord
  belongs_to :trader
  belongs_to :commodities
end
```

A single ledger does not have multiple traders or multiple commodities. A ledger will have **one** trader and **one** commodity. Therefore it **belongs to** a trader and **belongs to** a commodity.

[More on many to many relationships, through, and through source](https://www.sitepoint.com/master-many-to-many-associations-with-activerecord/)


<br>
<br>
<br>



### More Rails console queries

Restart Rails console, then see trader 1's commodities:

```ruby
ap Trader.find(1).commodities
```

![screenshot](https://i.imgur.com/z7yQwQ4.png)

> This trader has 'Oil'

See trader 2's commodities:

```ruby
ap Trader.find(2).commodities
```

![screenshot](https://i.imgur.com/Sl4wTPT.png)

> This trader also has oil.

See Commodity 1's traders

```ruby
ap Commodity.find(1).traders
```

> 'Oil' is owned by both "Skeletor" and "Slimer"

![screenshot](https://i.imgur.com/AQiqIeS.png)

<br>
<br>
<br>


### Proof of many-to-many

So far we have shown one many side of the relationship (a commodity has many traders), but a many-to-many is **symmetrical**.

Let's give more than one commodity to each of our Traders.

```ruby
ap Ledger.create( trader_id: 1, commodity_id: 2, qty: 1 )
```

```ruby
ap Ledger.create( trader_id: 2, commodity_id: 2, qty: 1 )
```

```ruby
ap Trader.find(1).commodities
```

![screenshot](https://i.imgur.com/C7h6Wth.png)

```ruby
ap Trader.find(2).commodities
```

![screenshot](https://i.imgur.com/zC6kocn.png)

Traders 1 and 2 have both commodities, just as those same commodities have both traders.


<br>
<br>
<br>



#### See ledgers

We have seen the commodities and traders associated with each other. They are also associated with ledgers.

See Trader 1's ledgers:

```ruby
ap Trader.find(1).ledgers
```

![screenshot](https://i.imgur.com/sL9hXnR.png)

> This way we can see the qty of each commodity the trader owns

See Commodity 1's ledgers

```ruby
ap Commodity.find(1).ledgers
```

![screenshot](https://i.imgur.com/Z9GJAth.png)

> For this commodity, we can see that trader 1 has  qty 1, and trader 2 has qty 1

<br>
<br>
<br>



#### Create a ledger entry through the Trader

```ruby
ap Trader.find(1).ledgers.create( commodity_id: 8, qty: 8 )
```

```ruby
ap Trader.find(1).commodities
```

![screenshot](https://i.imgur.com/sVUrdVN.png)


<br>
<br>
<br>


#### Unless you create a ledger, the relationship will not have a qty

Create a new commodity that belongs to Trader 1:

```ruby
ap Trader.find(3).commodities.create( name: 'iPhones', price: 300 )
```

```ruby
ap Trader.find(3).ledgers
```

The commodity will be added to the Ledger with qty nil:

![screenshot](https://i.imgur.com/3NvxlH7.png)

Create a new trader that owns commodity 6. It will be added to the Ledger with qty nil

```ruby
ap Commodity.find(4).traders.create( name: "Dumbo", money: 1 )
```

```ruby
ap Commodity.find(4).ledgers
```

![screenshot](https://i.imgur.com/avYNywD.png)

<br>
<br>
<br>


### Sending JSON Relations

Send Commodities with related Traders using `to_json`:

**commodities_controller.rb**

```ruby
  # GET /commodities
  def index
    @commodities = Commodity.all

    render json: @commodities.to_json(include: :traders)
  end

  # GET /commodities/1
  def show
    render json: @commodity.to_json(include: :traders)
  end
```

![screenshot](https://i.imgur.com/PRkV4hJ.png)

Send Traders with related Commodities using `to_json`:


<br>
<br>
<br>



**traders_controller.rb**

```ruby
  # GET /traders
  def index
    @traders = Trader.all

    render json: @traders.to_json(include: :commodities)
  end

  # GET /traders/1
  def show
    render json: @trader.to_json(include: :commodities)
  end
```

![screenshot](https://i.imgur.com/yOoZXyR.png)

Send Ledgers with related Traders and Commodities using array syntax:


<br>
<br>
<br>



**ledgers_controller.rb**

```ruby
  # GET /ledgers
  def index
    @ledgers = Ledger.all

    render json: @ledgers.to_json(include: [:trader, :commodity])
  end

  # GET /ledgers/1
  def show
    render json: @ledger.to_json(include: [:trader, :commodity])
  end
```

![screenshot](https://i.imgur.com/xhvCtZ1.png)

<br>
<br>
<br>

## Bonus

<br>
<br>



### ANOTHER EXAMPLE - Playlists and Songs

<br>
<br>


### Many to Many

A **song** can belong to many **playlists**

Many **playlists** might share the same **song**

Playlists and songs will be related with a two-column join table:

* `playlist_id`
* `song_id`

![screenshot](https://i.imgur.com/42vVJ1F.png)

Song 2 is associated with Playlist 4 _and_ Playlist 6.

Playlist 6 is associated with Song 2 _and_ Song 18.

The relationship is **symmetrical.**

<br>
<br>
<br>


### Many to Many Through

Each **playlist** can, in addition, also track how many **plays** a song has had.

For that the join table will need an additional column, `plays`. Any columns in the join table tell you _about_ the relationship between the related models. In this case, `plays` is extra information _about_ the relation of a song to a playlist.

![screenshot](https://i.imgur.com/GhAZKec.png)

What can we say _about_ Song 2 in Playlist 4? We can say it has 3 plays.

What we can we say _about_ Playlist 6's relationship to that same song? We can say that Playlist 6 has 99 plays of that song.

<br>
<br>
<br>

### Do a lab on Playlists and Songs

- Using what you've learned, create an api for playlists and songs.

<br>
<br>
<br>


## Extra research


<br>
<br>
<br>


### VALIDATIONS - Duplicates

If you create a Ledger that is a duplicate of a relationship between a trader and commodity, it will make another entry exactly the same.

Run this twice to create duplicates:

![screenshot](https://i.imgur.com/lCa08UF.png)

Run a query to see all ledgers formatted on their own lines:

![screenshot](https://i.imgur.com/pbp2V0Z.png)

See the duplicate entries:

![screenshot](https://i.imgur.com/YnG5IBN.png)

We would want to think that pairings of trader-commodity would be unique, but they are not. Does Trader 1 have 8 of commodity 5, or 16 of commodity 5?

![screenshot](https://i.imgur.com/EQ4FQWz.png)

Use a validation, either `validates_uniqueness_of :commodity_id, scope: :trader_id`

OR

`validates_uniqueness_of :trader_id, scope: :commodity_id`

![screenshot](https://i.imgur.com/l4z1fRB.png)

Now, Trader 1 cannot have two entries of Commodity 8 or vice versa.

If we try add a duplicate, we'll get an error

![screenshot](https://i.imgur.com/dumrTP7.png)

We can destroy ledgers we don't want:

![screenshot](https://i.imgur.com/tkEGLMx.png)

PLAYLISTS APP activity

* Make it so no song can be added to a playlist more than once.
