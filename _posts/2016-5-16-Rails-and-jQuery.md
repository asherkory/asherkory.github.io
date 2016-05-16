---
layout: post
title: Rails and jQuery
tags: learn-co, ruby, rails, javascript, jquery
---

I'll admit I didn't like JavaScript at first, coming straight from Ruby. All those curly braces and semicolons. But by the time I reached the final project assessment, it had grown on me. Dynamically changing page content and AJAX calls were actually pretty fun, and integrating JS with a Rails API was eye-opening.

For this project, I added features to my Homebrewer Rails app.

### Rails magic

Probably the most interesting tidbit I picked up while working on this project was Rails' magical `remote: true` option for form and link helpers. If you specify `remote: true` in a `form_for` helper in a view file, essentially adding the attribute `data-remote="true"` to your form tag, e.g.:

```html
  <%= form_for @ingredient, remote: true do |f| %>
    <p><%= f.label :name %><br>
      <%= f.text_field :name %></p>

      <p><%= f.submit %></p>
  <% end %>
```

...then Rails will do a very cool thing: instead of submitting the form like normal when the user clicks "submit", Rails will act like you wrote `event.preventDefault()` in the form event handler. It will send an ajax request to the controller containing the form input data, as JS. In order to handle the response, my ingredients controller's create method looks like this:

```ruby
  def create
    @ingredient = Ingredient.new(ingredient_params)
    if @ingredient.save
      respond_to do |format|
        format.json { render json: @ingredient, status: 201 }
        format.js {}
      end
    else
      render :index, alert: "Ingredient failed to save"
    end
  end
```

That `format.js {}` is the most important part, since `remote: true` is sending JS. Since those curly braces are empty, Rails will follow its usual default behavior and look for a view template file called "create" with a .js extension. It will allow that view file access to the instance variables in the controller method (@ingredient). So, my views/ingredients/create.js.erb looks like this:

```javascript
showNewIngredient("<%= j render @ingredient %>");
$("input#ingredient_name").val("");
```

In the second line I empty out the text input field so it's obvious to the user that the form data was submitted. In the first line I call a function showNewIngredient, which I defined in the file where I'm writing the bulk of my JS, assets/javascripts/ingredients.js. The cool thing here is the parameter I'm passing it! Since this file ends in .erb, I can write embedded Ruby here -- I can render an existing partial for my newly-created @ingredient! That "j" means "make everything safe for JavaScript", basically, so the HTML string I'm passing doesn't break anything.

Finally, when I define showNewIngredient, I tell it to insert the parameter I passed in, the @ingredient partial, in the DOM. Tada, new ingredient created and rendered without refreshing the page. This technique will also work for `link_to` helpers! See [RailsGuides](http://guides.rubyonrails.org/working_with_javascript_in_rails.html#built-in-helpers) for more.