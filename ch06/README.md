# LR5

## Chapter 6

#### "Presenting Models with Forms"

At the terminal, run the following command to create a new application:

	rails new guestbook

Enter the following to create scaffolding for the app with a basic data structure:

	rails generate scaffold Person name:string secret:string country:string email:string description:text can_send_email:boolean graduation_year:integer body_temperature:float price:decimal birthday:date favorite_time:time

Now run `rake db:migrate` to implement the migration created with the scaffold generator. Note that just like the last exercise, the *people_controller* has been generated complete with strong parameters - defined in a private method called *person_params*. This time, however, the person model can accept all of the attributes we specified when running the *rails generate scaffold* command.

Open *localhost:3000/people* in the browser to start the new application. Click the "New Person" link to view the form that Rails has generated to create a new "person" object. The code for this form, in *app/views/people/_form.html.erb*, uses the Rails method `form_for`, which automatically binds the input values to the Person model.

#### "Creating Text Fields and Text Areas"

In a text editor, open *app/views/people/_form.html.erb* and locate the following block of code:

	<div class="field">
    		<%= f.label :secret %><br>
    		<%= f.text_field :secret %>
	</div>

Change the code so that the "secret" attribute uses the *password_field* method:

	<%= f.password_field :secret %>

This will display asterisks (or dots, depending on browser defaults or any style modifications) when the user enters their "secret" criteria.

To change the size of the description's *text_area*, change that line of code to look like this:

	<%= f.text_area :description, :cols => 30, :rows => 10 %>

Demonstrating another option that Rails supports for text fields, locate the "graduation year" attribute and make it a *hidden* field:

	<%= f.hidden_field :graduation_year %>

	*NOTE*: hidden fields are not particularly useful in forms that create new objects,
	 but can come in useful elsewhere in your applications. Also note that the *label* 
	 for this field remains on the form. To completely hide the field in the view, 
	 simply delete the label on the hidden field.

#### "Labels"

In the div that contains the "name" field, change the text that will display above the text field. Change the following line:

	<%= f.label :name %><br />

by adding a comma and then the string (in quotes) to be displayed:

	<%= f.label :name, 'Your name' %>

#### "Creating Checkboxes"

Scaffolding has generated all of our app's checkbox functionality. Refer to the text for explanation of options for **check_box** method.

#### "Creating Radio Buttons"

Open *app/views/people/_form.html.erb* in the text editor and locate the input field for the *country* attribute (starting around line 24). Delete this block of code (or surround it with HTML comment tags <!-- -->). In its place, enter the following code block to create a specific list of linked radio buttons:

	<fieldset>
	<legend>Country</legend>
	<%= f.radio_button :country, 'USA' %> <%= f.label "person_country_usa", "USA" %><br> 
	<%= f.radio_button :country, 'Canada' %> <%= f.label "person_country_canada", "Canada" %><br> 
	<%= f.radio_button :country, 'Mexico' %> <%= f.label "person_country_mexico", "Mexico" %><br>
	</fieldset>

Refresh the "New Person" form in the browser to view the set of radio buttons Rails has created. Next, endeavoring to keep the code DRY, create some whitespace just above the `<fieldset>` tag you just created and enter the following **hash**:

	<% nations = { 'United States of America' => 'USA', 'Canada' => 'Canada', Mexico' => 'Mexico', 'United Kingdom' => 'UK' }%>

This also allows us to use more descriptive naming for the button descriptions in the view while saving the chosen entry as an abbreviation for efficiency.
**NOTE:** A hash of values like this would normally be created in the controller and referenced here in the view.

To use the hash we just created, we must now modify the contents of the `<fieldset>`. Delete (or comment) the <fieldset> code block and replace it with the following:

	<fieldset>
	    <legend>Country</legend>
	    <% list = nations.sort 
	    list.each {|x| %>
	      <%= f.radio_button :country, x[1] %>
	 	<label for="<%= ("person_country_" + x[1].downcase) %>"><%= x[0] %>
		</label><br> 
	    <% } %>
  	</fieldset>

Save the file and refresh the browser to see the new form in action.

#### "Dates and Times"

Scaffolding has generated all of our app's date and time selection. Refer to the text for explanation of time selector methods, helper methods, and options.

#### "Creating Helper Methods"

Open *app/helpers/people_helper.rb* in the text editor. The **buttons** method will help to simplify the radio button code in the form view. Create the method by defining it in the "PeopleHelper" module, like this:

	module PeopleHelper
		def buttons(model_name, target_property, button_source)
			html=''
			list = button_source.sort
			html << '<fieldset><legend>Country</legend>'
			list.each {|x|
				html << radio_button(model_name, target_property, x[1])
				html << (x[0])
				html << '<br>'
			}
			html << '</fieldset>'
			return html.html_safe
		end
	end

Returning to the *_form.html.erb* file, comment (or delete) the entire `<fieldset>` code block, including the `<fieldset>` tags themselves. Don't remove the "nations" hash, it is still needed. In place of this block, insert the following line of code:

	<%= buttons(:person, :country, nations) %>

This basically passes values from the *nations* hash to the *person* model's *country* property, using the new *buttons* method. It uses the exact order of arguments that were defined in the above method. The method uses an `each` loop to list all the values from the "nations" hash.

**Optionally**, a helper method could be created to conditionally represent the hash values as a list instead of radio buttons. This might be useful if there were too many buttons, cluttering the view. This method would look like this:

	
	def button_select(model_name, target_property, button_source)
		html=''
		list = button_source.sort

		if list.length < 4
			html << '<fieldset><legend>Country</legend>'
			list.each {|x|
				html << radio_button(model_name, target_property, x[1])
				html << (x[0])
				html << '<br>'
			}
			html << '</fieldset>'
		else
			html << ' <label for="person_country">Country</label><br>'
			html << select(model_name, target_property, list)
		end
		return html.html_safe
	end

Note that in the view, the helper method call (`<%= buttons(...) %>`) would need to be changed to `<%= button_select(...) %>` to use this method.

***
<sup>Tested and reconciled with changes made to book text</sup>
