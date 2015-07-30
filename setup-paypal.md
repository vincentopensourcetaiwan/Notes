## References

[#141 PayPal Basics](http://railscasts.com/episodes/141-paypal-basics "#141 PayPal Basics")

[#142 PayPal Notifications](http://railscasts.com/episodes/142-paypal-notifications "#142 PayPal Notifications")

[#143 PayPal Security](http://railscasts.com/episodes/143-paypal-security "#143 PayPal Security")

[Website Payments](https://www.paypal.com/cgi-bin/webscr?cmd=p/sell/mc/mc_wa-outside "Website Payments")

## Test project

[thefabulabs shopify](https://github.com/TheFABULABS/thefabulabs-shopify "thefabulabs shopify")

##Sign up for a business account

[Sign up for a business account](https://www.paypal.com/webapps/merchantboarding/webflow/unifiedflow?execution=e2s1 "Sign up for a business account")

##create Sandbox test accounts

[create Sandbox test accounts](https://developer.paypal.com/webapps/developer/applications/accounts "create Sandbox test accounts")

##checkout paypal

/app/views/orders/show.html.erb

    <%= link_to "checkout paypal", @order.paypal_url(root_url) %>

/app/models/order.rb

    def paypal_url(return_url, notify_url)
        values = {
            :business => 'XXX@mail.com',
            :cmd => '_cart',
            :upload => 1,
            :return => return_url,
            :invoice => id,
        }
        cart.line_items.each_with_index do |item, index|
            values.merge!({
                "amount_#{index+1}" => item.product.price,
                "item_name_#{index+1}" => item.product.name,
                "item_number_#{index+1}" => item.product.id,
            })
        end
        "https://www.sandbox.paypal.com/cgi-bin/webscr?" + values.to_query
    end

##PayPal Notifications

**scaffold notification for paypal notifaication**

    rails g scaffold payment_notification params:text order_id:integer status:string transaction_id:string

payment_notifications_controller.rb

    class PaymentNotificationsController < ApplicationController
        protect_from_forgery :except => [:create]

        def create
            PaymentNotification.create!(:params => params, :order_id => params[:invoice], :status => params[:payment_status], :transaction_id => params[:txn_id])
            render :nothing => true
        end
    end

**setup paypal notification**

/app/views/orders/show.html.erb

    <%= link_to "checkout paypal", @order.paypal_url(root_url, payment_notifications_url) %>

/app/models/order.rb

    def paypal_url(return_url, notify_url)
        values = {
            :business => 'XXX@mail.com',
            :cmd => '_cart',
            :upload => 1,
            :return => return_url,
            :invoice => id,
            :notify_url => notify_url
        }
        cart.line_items.each_with_index do |item, index|
            values.merge!({
                "amount_#{index+1}" => item.product.price,
                "item_name_#{index+1}" => item.product.name,
                "item_number_#{index+1}" => item.product.id,
            })
        end
        "https://www.sandbox.paypal.com/cgi-bin/webscr?" + values.to_query
    end


##PayPal Security

    mkdir certs
    cd certs
    openssl genrsa -out app_key.pem 1024
    openssl req -new -key app_key.pem -x509 -days 365 -out app_cert.pem

**upload app_cert.pem file and download paypal_cert_pem.txt**

[Website Payment Certificates](https://www.sandbox.paypal.com/us/cgi-bin/webscr?cmd=_profile-website-cert "Website Payment Certificates")

**copy paypal_cert_pem.txt to cert folder and rename to paypal_cert.pem**

    mv ~/Downloads/paypal_cert_pem.txt paypal_cert.pem

**modify codes**

/app/views/orders/show.html.erb

    <%= form_tag ENV['paypal_url'].dup do %>
        <%= hidden_field_tag :cmd, "_s-xclick" %>
        <%= hidden_field_tag :encrypted, @order.paypal_encrypted(root_url, payment_notifications_url(:secret => ENV['paypal_secret'].dup)) %>
        <p><%= submit_tag "Checkout palpal" %></p>
    <% end %>

/app/models/order.rb

    def paypal_encrypted(return_url, notify_url)
        values = {
            :business => ENV['paypal_email'].dup,
            :cmd => '_cart',
            :upload => 1,
            :return => return_url,
            :invoice => id,
            :notify_url => notify_url,
            :cert_id => ENV['paypal_cert_id'].dup,
            :currency_code => 'TWD'
        }
            cart.line_items.each_with_index do |item, index|
                values.merge!({
                    "amount_#{index+1}" => item.product.price,
                    "item_name_#{index+1}" => item.product.name,
                    "item_number_#{index+1}" => item.product.id,
                })
        end
        encrypt_for_paypal(values)
    end

    PAYPAL_CERT_PEM = File.read("#{Rails.root}/certs/paypal_cert.pem")
    APP_CERT_PEM = File.read("#{Rails.root}/certs/app_cert.pem")
    APP_KEY_PEM = File.read("#{Rails.root}/certs/app_key.pem")

    def encrypt_for_paypal(values)
        signed = OpenSSL::PKCS7::sign(OpenSSL::X509::Certificate.new(APP_CERT_PEM), OpenSSL::PKey::RSA.new(APP_KEY_PEM, ''), values.map { |k, v| "#{k}=#{v}" }.join("\n"), [], OpenSSL::PKCS7::BINARY)
        OpenSSL::PKCS7::encrypt([OpenSSL::X509::Certificate.new(PAYPAL_CERT_PEM)], signed.to_der, OpenSSL::Cipher::Cipher::new("DES3"), OpenSSL::PKCS7::BINARY).to_s.gsub("\n", "")
    end

payment_notifications_controller.rb

    class PaymentNotificationsController < ApplicationController
        protect_from_forgery :except => [:create]

        def create
            if params[:secret] == ENV['paypal_secret'].dup &&
                params[:receiver_email] == ENV['paypal_email'].dup
                PaymentNotification.create!(:params => params, :order_id => params[:invoice], :status => params[:payment_status], :transaction_id => params[:txn_id])
                render :nothing => true
            end
        end
    end

##setup application.yml

**get paypal_cert_id**

[Website Payment Certificates](https://www.sandbox.paypal.com/us/cgi-bin/webscr?cmd=_profile-website-cert "Website Payment Certificates")

/config/application.yml

    paypal_email: XXX@mail.com
    paypal_cert_id: XXX465QAP6XXX
    paypal_url: https://www.sandbox.paypal.com/cgi-bin/webscr
    paypal_secret: qwertyuiop



