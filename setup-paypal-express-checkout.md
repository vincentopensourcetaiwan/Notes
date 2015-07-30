## References

[#146 PayPal Express Checkout](http://railscasts.com/episodes/146-paypal-express-checkout "#146 PayPal Express Checkout")

## test project

[TheFABULABS/thefabulabs-shopify](https://github.com/TheFABULABS/thefabulabs-shopify "TheFABULABS/thefabulabs-shopify")

## scaffold paypal express checkout

    rails g scaffold Paypal_express_checkout ip_address:string order:references express_token:string express_payer_id:string first_name:string last_name:string


## setup environment

config/environments/development.rb

    config.after_initialize do
        ActiveMerchant::Billing::Base.mode = :test
            paypal_options = {
                :login => "xxxx.mail.com",
                :password => "xxxxxxxxxx",
                :signature => "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
            }
        ::STANDARD_GATEWAY = ActiveMerchant::Billing::PaypalGateway.new(paypal_options)
        ::EXPRESS_GATEWAY = ActiveMerchant::Billing::PaypalExpressGateway.new(paypal_options)
    end

config/environments/production.rb

    config.after_initialize do
        ActiveMerchant::Billing::Base.mode = :test
            paypal_options = {
                :login => "xxxx.mail.com",
                :password => "xxxxxxxxxx",
                :signature => "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
            }
        ::STANDARD_GATEWAY = ActiveMerchant::Billing::PaypalGateway.new(paypal_options)
        ::EXPRESS_GATEWAY = ActiveMerchant::Billing::PaypalExpressGateway.new(paypal_options)
    end

config/environments/test.rb

    config.after_initialize do
        ActiveMerchant::Billing::Base.mode = :test
        ::STANDARD_GATEWAY = ActiveMerchant::Billing::BogusGateway.new
        ::EXPRESS_GATEWAY = ActiveMerchant::Billing::BogusGateway.new
    end


## setup routes

/ config / routes.rb

    resources :orders do
        resources :paypal_express_checkouts do
            get :complete_checkout
        end
    end

## setup controller

/ app / controllers / paypal_express_checkouts_controller.rb


    def complete_checkout
      @order = current_user.orders.find(params[:order_id])
      @paypal_express_checkout = @order.paypal_express_checkouts.find(params[:paypal_express_checkout_id])

      if @paypal_express_checkout.purchase
        @order.state_paypal_express_checkout_transaction!('paypal express checkout purchase success')
        flash[:notice] = 'paypal express checkout purchase success'
      else
        @order.state_credit_card_transaction!('paypal express checkout purchase failure')
        flash[:notice] = 'paypal express checkout purchase failure'
      end
    end

    def show
      @order = current_user.orders.find(params[:order_id])
      @paypal_express_checkout = @order.paypal_express_checkouts.find(params[:id])
      @paypal_express_checkout.express_token = params[:token]
      details = EXPRESS_GATEWAY.details_for(params[:token])
      @paypal_express_checkout.express_payer_id = details.payer_id
      @paypal_express_checkout.first_name = details.params["first_name"]
      @paypal_express_checkout.last_name = details.params["last_name"]
      @paypal_express_checkout.save

      respond_to do |format|
        format.html # show.html.erb
        format.json { render json: @paypal_express_checkout }
      end
    end

    def new
      order = current_user.orders.find(params[:order_id])
      paypal_express_checkout = order.paypal_express_checkouts.new(params[:paypal_express_checkout])
      paypal_express_checkout.ip_address = request.remote_ip
      paypal_express_checkout.save
      response = EXPRESS_GATEWAY.setup_purchase(paypal_express_checkout.price_in_cents,
                                                :ip => request.remote_ip,
                                                :return_url => order_paypal_express_checkout_url(order, paypal_express_checkout),
                                                :cancel_return_url => root_url,
                                                :currency => 'TWD'
      )
      redirect_to EXPRESS_GATEWAY.redirect_url_for(response.token)
    end

## setup model

/ app / models / paypal_express_checkout.rb

    class PaypalExpressCheckout < ActiveRecord::Base
      belongs_to :order
      has_many :order_transactions, as: :transactionable
      #attr_accessible :ip_address

      def price_in_cents
        total_price = 0

        self.order.cart.line_items.each do |line_item|
          total_price = total_price + line_item.product.price
        end

        (total_price*100).round
      end

      def purchase
        response = EXPRESS_GATEWAY.purchase(price_in_cents, express_purchase_options)
        order_transactions.create!(:action => 'paypal express checkout purchase', :amount => price_in_cents, :response => response)
        response.success?
      end

      def express_purchase_options
        {
            :ip => ip_address,
            :token => express_token,
            :payer_id => express_payer_id,
            :currency => 'TWD'
        }
      end
    end

## link to paypal express checkout

    <%= link_to 'Paypal Express Checkout', new_order_paypal_express_checkout_path(@order)%>

## show paypal express checkout

/ app / views / paypal_express_checkouts / show.html.erb

    <p id="notice"><%= notice %></p>
    <p>Name: <%=h @paypal_express_checkout.first_name %> <%=h @paypal_express_checkout.last_name %></p>
    <p>TODO Display order confirmation details</p>
    <p>
      <b>Order:</b>
      <%= @paypal_express_checkout.order %>
    </p>


    <%= link_to 'complete checkout', order_paypal_express_checkout_complete_checkout_path(@order, @paypal_express_checkout) %>

## show paypal express checkout result

create following file

/ app / views / paypal_express_checkouts / complete_checkout.html.erb







