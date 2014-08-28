#Contact us form


In gemfile add the following gems,

	gem 'mail_form'
    gem 'simple_form'
    gem 'quiet_assets'
    gem 'bootstrap-sass'
	gem 'autoprefixer-rails'
	gem 'mail_form'


Run,

	bundle


To run simple_form,

	rails g simple_form:install --bootstrap
=======================================================================
##Bootstrap Installation (https://github.com/twbs/bootstrap-sass)

next,

	 mv app/assets/stylesheets/application.css to app/assets/stylesheets/application.css.scss in it
	 
	 @import "bootstrap-sprockets";
	 @import "bootstrap";

next,

	app/assets/javascripts/application.js:

	//= require bootstrap-sprockets
====================================================================
TO generate a controller

	rails g controller contacts

config/routes.rb

	resources "contacts", only: [:new, :create]

app/controllers/contacts_controller.rb

	class ContactsController < ApplicationController
  		def new
    		@contact = Contact.new
  		end
  		def create
    		@contact = Contact.new(params[:contact])
    		@contact.request = request
    		if @contact.deliver
      			flash.now[:error] = nil
      			flash.now[:notice] = 'Thank you for your message!'
    		else
      			flash.now[:error] = 'Cannot send message.'
      			render :new
    		end
  		end
	end

To generate a model

	rails g model contact name email message:text

run,

	rake db:migrate

app/models/contact.rb

	class Contact < MailForm::Base
  		append :remote_ip, :user_agent, :session
  		attribute :name,      :validate => true
  		attribute :email,     :validate => /\A([\w\.%\+\-]+)@([\w\-]+\.)+([\w]{2,})\z/i
  		attribute :message
  		# Declare the e-mail headers. It accepts anything the mail method
  		# in ActionMailer accepts.
  		def headers
    		{
      			:subject => "My Contact Form",
      			:to => "anju.ideology@gmail.com",
      			:from => %("#{name}" <#{email}>)
    		}
  		end
	end

in new.html.erb,

	<h1>Contact Us</h1>
  	<%= simple_form_for @contact do |f| %>
    	<%= f.input :name %> 
    	<%= f.input :email %>    
    	<%= f.input :message, :as => :text%>
    	<div>
      	</br>
      		<%= f.button :submit, 'Send message', :class=> "btn btn-primary" %>
    	</div>
  	<% end %>

in create.html.erb,

	<div>
  		<% flash.each do |key, value| %>
    	<div>
      		<a href="#" data-dismiss="alert">×</a>
        	<%= value %>
    	</div>
  		<% end %>
	</div>

environment/development.rb

	config.action_mailer.raise_delivery_errors = true
    config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }   
	config.action_mailer.delivery_method = :smtp
 	config.action_mailer.smtp_settings = {
    :address => "smtp.gmail.com",
    :port => 587,
    :domain => "localhost:3000",
    :user_name => "anju.ideology@gmail.com",
    :password => "ideology123",
    :authentication => 'plain',
    :enable_starttls_auto => true 
    }

