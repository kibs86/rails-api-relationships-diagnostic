# Rails API Relationships Diagnostic

Place your responses inside the fenced code-blocks where indivated by comments.

1.  Describe a reason why a join tables may be valuable.

```sh
  # Join tables are valuable when creating many to many relationships.  One
  # example we used in class was Recipes and Ingredients.  Each recipe could
  # have many ingredients and each ingredient can belong to many recipes.  The
  # join table recipe_ingredients allowed us to create this type of relationship.
```

1.  Provide a database table structure and explain the Entity Relationship that
describes a many-to-many relationship for `Profiles`, `Movies` and `Favorites`
(Think of Netflix). A `Profile` has a `given_name`, `surname` and `email` and a
`Movies` have `title`, `release_date`, and `length` and `Favorites` would be the
join table with references to `Movies` and `Profiles`.

```sh
  # I'm not sure what format the answer to this question is supposed to be in so
  # I'm providing the create table SQL commands for each and then a description.

  # CREATE TABLE profiles (
  # id SERIAL PRIMARY KEY,
  # given_name TEXT,
  # surname TEXT,
  # email TEXT
  # );

  # CREATE TABLE movies (
  # id SERIAL PRIMARY KEY,
  # title TEXT,
  # release_date DATE,
  # length INTEGER
  # );

  # CREATE TABLE favorites (
  # id SERIAL PRIMARY KEY,
  # profile_id INTEGER REFERENCES profiles(id),
  # movie_id INTEGER REFERENCES movies(id)
  # );

  # The profiles and movies tables would each have the columns described above
  # and the join table (favorites) would contain references to the those tables
  # with their respective IDs as the foreign keys.  There would be a 1 to many
  # relationship between profiles and favorites and movies and favorites.

```

1.  For the above example, what needs to be added to the Model files?

```rb
class Profile < ActiveRecord::Base
  has_many :movies, through: :favorites
  has_many :favorites, dependent: :destroy
end
```

```rb
class Movie < ActiveRecord::Base
  has_many :profiles, through: :favorites
  has_many :favorites, dependent: :destroy
end
```

```rb
class Favorite < ActiveRecord::Base
  belongs_to :movie, inverse_of: :favorites
  belongs_to :profile, inverse_of: :favorites
end
```

1.  What is the purpose of a serializer? What would our `Profile` serializer look
like to show all movies favorited by a profile on
`http://localhost:3000/profiles/1`

```sh
  # The serializer filters the information provided within API responses.
```

```rb
class ProfileSerializer < ActiveModel::Serializer
  attributes :id, :given_name, :surname, :email, :favorites
end
```

1.  What would the command be to _scaffold_ out a **join table** for Favorites from
the above `Movies` and `Profiles`.

```sh
  # rails g scaffold favorites profile:references movie:references
```

1.  What is `Dependent: Destroy` and where/why would we use it?

```sh
  # Dependent: Destroy means if you want to destroy an item in a table, you
  # must first destroy all references to it within joined tables.  In this example,
  # if you want to destroy a given profile or movie, you first need to remove
  # all instances in the favorites table that reference that profile or movie.
```

1.  Think of **ANY** example where you would have a one-to-many relationship as well
as a many-to-many relationship in an application. You only need to list the
description about the resources and how they relate to one another.

```sh
  # One example we used in class was with books.  Books and Authors have a one to
  # many relationship (Authors can write multiple books, but a book only has a
  # single author).  Books and Borrowers however, have a many to many relationship (
  # a book can be borrowed by multiple people and a person can borrow multipe books).
  # In this case a loans table was created to perform the join between the two.
```
