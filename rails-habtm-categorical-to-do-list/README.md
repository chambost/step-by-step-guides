# Summary

A Ruby-on-Rails 6.0.2.2 step-by-step guide to implement a TODO list with categories using has_and_belongs_to_many (HABTM)

Based on a similar rails 4 guide found here [0].

[0] http://habtmexample.herokuapp.com/instructions

## Generators and Models

Initail set-up with generators.

```
rails new todomvc -d postgresql
cd todomvc
rails generate scaffold Task name:string{42} completed:boolean
rails generate scaffold Category name:string{18}
rails generate migration categories_tasks
```

Configure the migration db/[TIMESTAMP]_categories_tasks.

```
def change
  create_table :categories_tasks, :id => false do |t|
    t.integer :category_id
    t.integer :task_id
  end
end
```

And the models app/model/*.rb

```
class Task < ApplicationRecord
    has_and_belongs_to_many :categories
end
```

```
class Category < ApplicationRecord
    has_and_belongs_to_many :tasks
end
```

Configure config/database.yml. 

```
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see Rails configuration guide
  # https://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: localhost
  port: 5432
  username: [username]
  password: [password]
```

Run migrations

```
rails db:setup
rails db:migrate
```

Populate the database via db/seeds.rb

```
Task.create(name:"Rails ToDoMVC App",completed:false)
Task.create(name:"5 Tasks",completed:false)
Task.create(name:"Push to GitHub",completed:false)
Task.create(name:"Categories",completed:false)
Task.create(name:"Look for next thing ToDo",completed:false)
Category.create(name:"Rails")
Category.create(name:"Documentation")
```

```
rails db:seed
```

## Controllers

Customise /app/controllers/tasks_controller.rb

```
  # GET /tasks/new
  def new
    @task = Task.new
    @categories = Category.all
  end

  # GET /tasks/1/edit
  def edit
    @categories = Category.all
  end
```

```
    # Only allow a list of trusted parameters through.
    def task_params
      params.require(:toask).permit(:name, :completed, :category_ids => [])
    end
```

### Views 

Customise app/views/tasks/index.html.erb

```
<th>Categories</th>
```
```
<td><%= task.categories.map {|c| c.name}.join(', ') %></td>
```

Customise app/views/tasks/show.html.erb

```
<p>
  <strong>Categories:</strong>
  <%= @task.categories.map {|c| c.name}.join(', ') %>
</p>
```

Customise app/views/tasks/_form.html.erb

```
<div class="field">
  <%= collection_check_boxes(:task, :category_ids, @categories, :id, :name) %>
</div>
```

## Results

Test the result

```
rails server
```

Navigate browser to `localhost:3000/tasks`