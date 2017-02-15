# Rails API Relationships Diagnostic

Place your responses inside the fenced code-blocks where indivated by comments.

1.  Describe a reason why a join tables may be valuable.

  ```md
  A join table may be valuable in situations where your app needs to implement a many-to-many relationship, since it is the method by which those relationships are modeled in relational databases.
  ```

1.  Provide a database table structure and explain the Entity Relationship that
  describes a many-to-many relationship for `Profiles`, `Movies` and `Favorites`
  (Think of Netflix). A `Profile` has a `given_name`, `surname` and `email` and a
  `Movies` have `title`, `release_date`, and `length` and `Favorites` would be the
  join table with references to `Movies` and `Profiles`.

  ```md
    profiles(id, given_name, surname, email)
    movies(id, title, release_date, length)
    favorites(id, movie_id, profile_id, [rank]) # rank is a potential other column if the app requires the ability to order favorites

    A Profile has many Movies through Favorites, and thus also has many Favorites. Similarly, a Movie has many Profiles though Favorites, and also has many Favorites.
  ```

1.  For the above example, what needs to be added to the Model files?

  ```rb
  class Profile < ActiveRecord::Base
    has_many :movies, through: :favorites
    has_many :favorites
  end
  ```

  ```rb
  class Movie < ActiveRecord::Base
    has_many :profiles, through :favorites
    has_many :favorites
  end
  ```

  ```rb
  class Favorite < ActiveRecord::Base
    belongs_to :profile
    belongs_to :movie
  end
  ```

1.  What is the purpose of a serializer? What would our `Profile` serializer look
like to show all movies favorited by a profile on
`http://localhost:3000/profiles/1`

  ```md
  The purpose of a serializer is to format the JSON response that the server will send to the client based on the data retrieved by the model.
  ```

  ```rb
  class ProfileSerializer < ActiveModel::Serializer
    attributes :given_name, :surname, :email, :movies

    def movies
      object.movies.pluck(:id)
    end
  end
  ```

1.  What would the command be to _scaffold_ out a **join table** for Favorites from
the above `Movies` and `Profiles`.

  ```sh
    bin/rails g scaffold favorite movie:references profile:references [rank:integer]
  ```

1.  What is `Dependent: Destroy` and where/why would we use it?

  ```md
    Dependent: Destroy is an option we can enable in a rails ApplicationRecord object which will cause rails to automatically delete any entries in the database with foreign keys referencing the object being deleted.
  ```

1.  Think of **ANY** example where you would have a one-to-many relationship as well
as a many-to-many relationship in an application. You only need to list the
description about the resources and how they relate to one another.

  ```md
    The most obvious example would be in some kind of library app, where you would first have a one-to-many relationship between Authors and Books, where one author has many books, but you would then further have a many-to-many realationship between books and borrowers, where books and borrowers both have many of each other through a loan model.
  ```
