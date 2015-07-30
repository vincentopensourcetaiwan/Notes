### reference:

[#53 Handling Exceptions (revised)](http://railscasts.com/episodes/53-handling-exceptions-revised "#53 Handling Exceptions (revised)")

config/application.rb

    config.exceptions_app = self.routes

config/environments/development.rb

    config.consider_all_requests_local = false

config/routes.rb

    # Handling Exceptions
    match '404', to: 'errors#show_404'
    match '500', to: 'errors#show_500'


app/controllers/errors_controller.rb

    class ErrorsController < ApplicationController
      def show
        @exception = env['action_dispatch.exception']
      end

      def show_404
        @exception = env['action_dispatch.exception']
      end

      def show_500
        @exception = env['action_dispatch.exception']
      end
    end

app/views/errors/show.html.erb

    <br>
    <h4 class="text-center">抱歉，這錯誤我們也無法接受</h4>
    <p style="width: 500px;margin: 0 auto;">若您持續的看到此頁面，請聯絡我們的<a href="mailto:service@brandinlabs.com">客服信箱</a>，我們會儘快的修復此錯誤。
    </p>

app/views/errors/show_404.html.erb

    <br>
    <h4 class="text-center">您迷路了嘛？</h4>
    <p style="width: 500px;margin: 0 auto;"><%= @exception %></p>

app/views/errors/show_500.html.erb

    <br>
    <h4 class="text-center">抱歉，這錯誤我們也無法接受</h4>
    <p style="width: 500px;margin: 0 auto;">若您持續的看到此頁面，請聯絡我們的<a href="mailto:service@brandinlabs.com">客服信箱</a>，我們會儘快的修復此錯誤。</p>



