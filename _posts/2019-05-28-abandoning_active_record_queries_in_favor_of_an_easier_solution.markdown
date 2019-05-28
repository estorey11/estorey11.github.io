---
layout: post
title:      "Abandoning Active Record Queries in Favor of an Easier Solution"
date:       2019-05-28 17:29:41 +0000
permalink:  abandoning_active_record_queries_in_favor_of_an_easier_solution
---

Recently I created a Sinatra based CRUD app called Workout Logger that allows users to create accounts and track workouts . One of the last features that I wanted to implement was for the user's account page to display their "best lifts", as the use of this app is catered towards powerlifters and weightlifters. The "best lifts" section would find and display the highest weight that the user has logged for a specific exercise, as well as the number of reps they completed of that weight, the date that the workout occurred on, and their bodyweight at the time of that workout. 

The database schema for this app looks like this:
```
ActiveRecord::Schema.define(version: 20190507210602) do

  create_table "exercises", force: :cascade do |t|
    t.string   "name"
    t.integer  "weight"
    t.integer  "sets"
    t.integer  "reps"
    t.integer  "workout_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "users", force: :cascade do |t|
    t.string   "username"
    t.string   "password_digest"
    t.datetime "created_at",      null: false
    t.datetime "updated_at",      null: false
  end

  create_table "workouts", force: :cascade do |t|
    t.integer  "bodyweight"
    t.date     "date"
    t.integer  "user_id"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end
```

I assumed that I would be able to use Active Record queries to find the exercise ID where the weight is highest for each unique exercise name, but I ran into a few problems. First I tried to group the exercises by name using `@user.exercises.select(:name, :id, :weight).group(:name)`, but that only returned the first exercise with each unique name. Using the #having method didn't look promising either, as it didn't appear that I could pass it a block to find the maximum weight per a specific name. Then, I discovered the #distinct method, and thought I had finally found my answer. Unfortunately, running `@user.exercises.select(:name).distinct` would only return the names of each exercise, but not the IDs themselves.

I tried numerous combinations of these queries to no avail. I decided to simply query for all exercises in order by weight, and then iterate through them, adding them to a @best_lifts array if the array didn't already incluse an exercise with the same name.


```
@ordered_lifts=@user.exercises.order(weight: :desc)
@best_lifts=[]

@ordered_lifts.each do |lift|

    if !@best_lifts.any? {|best_lift| best_lift.name == lift.name}
      @best_lifts << lift
    end
		
end
```

Is there a way to get this same result using purely Active Record queries? Probably, but I spent far too long trying to find it when a much quicker solution was under my nose using just a few lines of Ruby.
