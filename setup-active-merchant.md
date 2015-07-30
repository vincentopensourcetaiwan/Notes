## References

[#145 Integrating Active Merchant](http://railscasts.com/episodes/145-integrating-active-merchant "#145 Integrating Active Merchant")

## Test Project

[thefabulabs shopify](https://github.com/TheFABULABS/thefabulabs-shopify "thefabulabs shopify")

## Installation

Gemfile

    gem 'activemerchant'


## setup environment

/config/environments/test.rb

/config/environments/development.rb

/config/environments/production.rb

      # Active Merchant
      config.after_initialize do
        ActiveMerchant::Billing::Base.mode = :test
        ::GATEWAY = ActiveMerchant::Billing::PaypalGateway.new(
            :login => "xxxxxxx_api1.mail.com",
            :password => "XXXXXXXXXX",
            :signature => "XXXXXXXXXXXXXXxxxxXXXXXXXXXXXxxxxxxxxxxxxxxxxxxxxxxxxxxx"
        )
      end


## Generate credit card

    rails g scaffold Credit_card order_id:integer ip_address:string first_name:string last_name:string card_type:string card_expires_on:date

## setup relationship between order and credit_card

/app/models/order.rb

    has_many :credit_cards

/app/models/credit_card.rb

    has_many :order_transactions


## setup routes

/config/routes.rb

    resources :orders do
        resources :credit_cards
    end

## link to checkout by credit cart

/app/views/orders/show.html.erb

    <%= link_to 'Checkout Credit Card', new_order_credit_card_path(@order) %>

## credit card operation

/app/controllers/credit_cards_controller.rb

    def new
        @order = Order.find(params[:order_id])
        @credit_card = @order.credit_cards.new


        respond_to do |format|
            format.html # new.html.erb
            format.json { render json: @credit_card }
        end
    end

    def create
        @order = Order.find(params[:order_id])
        @credit_card = @order.credit_cards.new(params[:credit_card])
        @credit_card.ip_address = request.remote_ip

        respond_to do |format|
          if @credit_card.save
            if @credit_card.purchase
              format.html { redirect_to order_credit_card_path(@order, @credit_card), notice: 'good' }
              format.json { render json: @credit_card, status: :created, location: @credit_card }
            else
              format.html { redirect_to order_credit_card_path(@order, @credit_card), notice: 'bad' }
              format.json { render json: @credit_card, status: :created, location: @credit_card }
            end
          else
            format.html { render action: "new" }
            format.json { render json: @credit_card.errors, status: :unprocessable_entity }
          end
        end
    end

## create credit card form

/app/views/credit_cards _form.html.erb

    <%= simple_form_for([@order, @credit_card]) do |f| %>
        <%= f.error_notification %>

        <div class="form-inputs">
            <%= f.input :first_name %>
            <%= f.input :last_name %>
            <%= f.select :card_type, [["Visa", "visa"], ["MasterCard", "master"], ["Discover", "discover"], ["American Express", "american_express"]] %>
            <%= f.input :card_number %>
            <%= f.input :card_verification %>
            <%= f.date_select :card_expires_on, :discard_day => true, :start_year => Date.today.year, :end_year => (Date.today.year+10), :add_month_numbers => true %>
        </div>

        <div class="form-actions">
            <%= f.button :submit %>
        </div>
    <% end %>

## setup credit_card.rb

/ app / models / credit_card.rb

    class CreditCard < ActiveRecord::Base
        attr_accessible :card_expires_on, :card_type, :first_name, :ip_address, :last_name, :order_id, :card_number, :card_verification
        attr_accessor :card_number, :card_verification
        belongs_to :order
        has_many :order_transactions

        validate :validate_card, :on => :create

        def purchase
            response = GATEWAY.purchase(price_in_cents, credit_card, :ip => ip_address)
            order_transactions.create!(:action => "purchase", :amount => price_in_cents, :response => response)
            response.success?
        end

        def price_in_cents
            total_price = 0

            self.order.cart.line_items.each do |line_item|
                total_price = total_price + line_item.product.price
            end

            (total_price*100).round
        end

        def validate_card
            unless credit_card.valid?
                credit_card.errors.full_messages.each do |message|
                    errors.add :base, message
                end
            end
        end

        def credit_card
            @credit_card ||= ActiveMerchant::Billing::CreditCard.new(
                :type => card_type,
                :number => card_number,
                :verification_value => card_verification,
                :month => card_expires_on.month,
                :year => card_expires_on.year,
                :first_name => first_name,
                :last_name => last_name
            )
        end
    end



## do not log credit card number and card verification

config/application.rb

    config.filter_parameters += [:password, :password_confirmation, :card_number, :card_verification]


## active merchant log

    rails g scaffold order_transaction credit_card_id:integer action:string amount:integer success:boolean authorization:string message:string params:text


/ app / models / order_transaction.rb

    class OrderTransaction < ActiveRecord::Base
        attr_accessible :action, :amount, :authorization, :credit_card_id, :message, :params, :success, :response
        belongs_to :credit_card

        serialize :params

        def response=(response)
            self.success = response.success?
            self.authorization = response.authorization
            self.message = response.message
            self.params = response.params
            rescue ActiveMerchant::ActiveMerchantError => e
                self.success = false
                self.authorization = nil
                self.message = e.message
                self.params = {}
        end
    end

## check transaction

[Paypal History](https://www.sandbox.paypal.com/us/cgi-bin/webscr?cmd=_history&nav=0%2e3 "Paypal History")


