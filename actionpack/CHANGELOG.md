*   Use the reference for the mime type to get the format

    Fixes: CVE-2014-0082

*   Escape format, negative_format and units options of number helpers

    Fixes: CVE-2014-0081

## Rails 3.2.16 (Dec 12, 2013) ##

*   Deep Munge the parameters for GET and POST Fixes CVE-2013-6417

*   Stop using i18n's built in HTML error handling.  Fixes: CVE-2013-4491

*   Escape the unit value provided to number_to_currency Fixes CVE-2013-6415

*   Only use valid mime type symbols as cache keys CVE-2013-6414

## Rails 3.2.15 (Oct 16, 2013) ##

*   Fix `ActionDispatch::RemoteIp::GetIp#calculate_ip` to only check for spoofing
    attacks if both `HTTP_CLIENT_IP` and `HTTP_X_FORWARDED_FOR` are set.

    Fixes #12410
    Backports #10844

    *Tamir Duberstein*

*   Fix the assert_recognizes test method so that it works when there are
    constraints on the querystring.

    Issue/Pull Request #9368
    Backport #5219

    *Brian Hahn*

*   Fix to render partial by context(#11605).

    *Kassio Borges*

*   Fix `ActionDispatch::Assertions::ResponseAssertions#assert_redirected_to`
    does not show user-supplied message.

    Issue: when `assert_redirected_to` fails due to the response redirect not
    matching the expected redirect the user-supplied message (second parameter)
    is not shown. This message is only shown if the response is not a redirect.

    *Alexey Chernenkov*


## Rails 3.2.14 (Jul 22, 2013) ##

*   Merge `:action` from routing scope and assign endpoint if both `:controller`
    and `:action` are present. The endpoint assignment only occurs if there is
    no `:to` present in the options hash so should only affect routes using the
    shorthand syntax (i.e. endpoint is inferred from the the path).

    Fixes #9856

    *Yves Senn*, *Andrew White*

*   Always escape the result of `link_to_unless` method.

    Before:

        link_to_unless(true, '<b>Showing</b>', 'github.com')
        # => "<b>Showing</b>"

    After:

        link_to_unless(true, '<b>Showing</b>', 'github.com')
        # => "&lt;b&gt;Showing&lt;/b&gt;"

    *dtaniwaki*

*   Use a case insensitive URI Regexp for #asset_path.

    This fix a problem where the same asset path using different case are generating
    different URIs.

    Before:

        image_tag("HTTP://google.com")
        # => "<img alt=\"Google\" src=\"/assets/HTTP://google.com\" />"
        image_tag("http://google.com")
        # => "<img alt=\"Google\" src=\"http://google.com\" />"

    After:

        image_tag("HTTP://google.com")
        # => "<img alt=\"Google\" src=\"HTTP://google.com\" />"
        image_tag("http://google.com")
        # => "<img alt=\"Google\" src=\"http://google.com\" />"

    *David Celis + Rafael Mendon??a Fran??a*

*   Fix explicit names on multiple file fields. If a file field tag has
    the multiple option, it is turned into an array field (appending `[]`),
    but if an explicit name is passed to `file_field` the `[]` is not
    appended.
    Fixes #9830.

    *Ryan McGeary*

*   Fix assets loading performance in 3.2.13.

    Issue #8756 uses Sprockets for resolving files that already exist on disk,
    for those files their extensions don't need to be rewritten.

    Fixes #9803.

    *Fred Wu*

*   Fix `ActionController#action_missing` not being called.
    Fixes #9799.

    *Janko Luin*

*   `ActionView::Helpers::NumberHelper#number_to_human` returns the number unaltered when
    the units hash does not contain the needed key, e.g. when the number provided is less
    than the largest key provided.

    Examples:

        number_to_human(123, units: {})                # => 123
        number_to_human(123, units: { thousand: 'k' }) # => 123

    Fixes #9269.
    Backport #9347.

    *Michael Hoffman*

*   Include I18n locale fallbacks in view lookup.
    Fixes GH#3512.

    *Juan Barreneche*

*   Fix `ActionDispatch::Request#formats` when the Accept request-header is an
    empty string. Fix #7774 [Backport #8977, #9541]

    *Soylent + Maxime R??ty*


## Rails 3.2.13 (Mar 18, 2013) ##

*   Fix incorrectly appended square brackets to a multiple select box
    if an explicit name has been given and it already ends with "[]".

    Before:

        select(:category, [], {}, multiple: true, name: "post[category][]")
        # => <select name="post[category][][]" ...>

    After:

        select(:category, [], {}, multiple: true, name: "post[category][]")
        # => <select name="post[category][]" ...>

    Backport #9616.

    *Olek Janiszewski*

*   Determine the controller#action from only the matched path when using the
    shorthand syntax. Previously the complete path was used, which led
    to problems with nesting (scopes and namespaces).
    Fixes #7554.
    Backport #9361.

    Example:

        # this will route to questions#new
        scope ':locale' do
          get 'questions/new'
        end

    *Yves Senn*

*   Fix `assert_template` with `render :stream => true`.
    Fix #1743.
    Backport #5288.

    *Sergey Nartimov*

*   Eagerly populate the http method lookup cache so local project inflections do
    not interfere with use of underscore method ( and we don't need locks )

    *Aditya Sanghi*

*   `BestStandardsSupport` no longer duplicates `X-UA-Compatible` values on
    each request to prevent header size from blowing up.

    *Edward Anderson*

*   Fixed JSON params parsing regression for non-object JSON content.

    *Dylan Smith*

*   Prevent unnecessary asset compilation when using `javascript_include_tag` on
    files with non-standard extensions.

    *Noah Silas*

*   Fixes issue where duplicate assets can be required with sprockets.

    *Jeremy Jackson*

*   Bump `rack` dependency to 1.4.3, eliminate `Rack::File` headers deprecation warning.

    *Sam Ruby + Carlos Antonio da Silva*

*   Do not append second slash to `root_url` when using `trailing_slash: true`

    Fix #8700.
    Backport #8701.

    Example:
        # before
        root_url # => http://test.host//

        # after
        root_url # => http://test.host/

    *Yves Senn*

*   Fix a bug in `content_tag_for` that prevents it for work without a block.

    *Jasl*

*   Clear url helper methods when routes are reloaded by removing the methods
    explicitly rather than just clearing the module because it didn't work
    properly and could be the source of a memory leak.

    *Andrew White*

*   Fix a bug in `ActionDispatch::Request#raw_post` that caused `env['rack.input']`
    to be read but not rewound.

    *Matt Venables*

*   More descriptive error messages when calling `render :partial` with
    an invalid `:layout` argument.

    Fixes #8376.

        render :partial => 'partial', :layout => true
        # results in ActionView::MissingTemplate: Missing partial /true

    *Yves Senn*

*   Accept symbols as `#send_data` :disposition value. [Backport #8329] *Elia Schito*

*   Add i18n scope to `distance_of_time_in_words`. [Backport #7997] *Steve Klabnik*

*   Fix side effect of `url_for` changing the `:controller` string option. [Backport #6003]
    Before:

        controller = '/projects'
        url_for :controller => controller, :action => 'status'

        puts controller #=> 'projects'

    After

        puts controller #=> '/projects'

    *Nikita Beloglazov + Andrew White*

*   Introduce `ActionView::Template::Handlers::ERB.escape_whitelist`. This is a list
    of mime types where template text is not html escaped by default. It prevents `Jack & Joe`
    from rendering as `Jack &amp; Joe` for the whitelisted mime types. The default whitelist
    contains text/plain. Fix #7976 [Backport #8235]

    *Joost Baaij*

*   `BestStandardsSupport` middleware now appends it's `X-UA-Compatible` value to app's
    returned value if any. Fix #8086 [Backport #8093]

    *Nikita Afanasenko*

*   prevent double slashes in engine urls when `Rails.application.default_url_options[:trailing_slash] = true` is set
    Fix #7842

    *Yves Senn*

*   Fix input name when `:multiple => true` and `:index` are set.

    Before:

        check_box("post", "comment_ids", { :multiple => true, :index => "foo" }, 1)
        #=> <input name=\"post[foo][comment_ids]\" type=\"hidden\" value=\"0\" /><input id=\"post_foo_comment_ids_1\" name=\"post[foo][comment_ids]\" type=\"checkbox\" value=\"1\" />

    After:

        check_box("post", "comment_ids", { :multiple => true, :index => "foo" }, 1)
        #=> <input name=\"post[foo][comment_ids][]\" type=\"hidden\" value=\"0\" /><input id=\"post_foo_comment_ids_1\" name=\"post[foo][comment_ids][]\" type=\"checkbox\" value=\"1\" />

    Fix #8108

    *Daniel Fox, Grant Hutchins & Trace Wax*


## Rails 3.2.12 (Feb 11, 2013) ##

*   No changes.


## Rails 3.2.11 (Jan 8, 2013) ##

*   Strip nils from collections on JSON and XML posts. [CVE-2013-0155]


## Rails 3.2.10 (Jan 2, 2013) ##

*   No changes.


## Rails 3.2.9 (Nov 12, 2012) ##

*   Clear url helpers when reloading routes.

    *Santiago Pastorino*

*   Revert the shorthand routes scoped with `:module` option fix
    This added a regression since it is changing the URL mapping.
    This makes the stable release backward compatible.

    *Rafael Mendon??a Fran??a*

*   Revert the `assert_template` fix to not pass with ever string that matches the template name.
    This added a regression since people were relying on this buggy behavior.
    This will introduce back #3849 but this stable release will be backward compatible.
    Fixes #8068.

    *Rafael Mendon??a Fran??a*

*   Revert the rename of internal variable on ActionController::TemplateAssertions to prevent
    naming collisions. This added a regression related with shoulda-matchers, since it is
    expecting the [instance variable @layouts](https://github.com/thoughtbot/shoulda-matchers/blob/9e1188eea68c47d9a56ce6280e45027da6187ab1/lib/shoulda/matchers/action_controller/render_with_layout_matcher.rb#L74).
    This will introduce back #7459 but this stable release will be backward compatible.
    Fixes #8068.

    *Rafael Mendon??a Fran??a*

*   Accept :remote as symbolic option for `link_to` helper. *Riley Lynch*

*   Warn when the `:locals` option is passed to `assert_template` outside of a view test case
    Fix #3415

    *Yves Senn*

*   Rename internal variables on ActionController::TemplateAssertions to prevent
    naming collisions. @partials, @templates and @layouts are now prefixed with an underscore.
    Fix #7459

    *Yves Senn*

*   `resource` and `resources` don't modify the passed options hash
    Fix #7777

    *Yves Senn*

*   Precompiled assets include aliases from foo.js to foo/index.js and vice versa.

        # Precompiles phone-<digest>.css and aliases phone/index.css to phone.css.
        config.assets.precompile = [ 'phone.css' ]

        # Precompiles phone/index-<digest>.css and aliases phone.css to phone/index.css.
        config.assets.precompile = [ 'phone/index.css' ]

        # Both of these work with either precompile thanks to their aliases.
        <%= stylesheet_link_tag 'phone', media: 'all' %>
        <%= stylesheet_link_tag 'phone/index', media: 'all' %>

    *Jeremy Kemper*

*   `assert_template` is no more passing with what ever string that matches
    with the template name.

    Before when we have a template `/layout/hello.html.erb`, `assert_template`
    was passing with any string that matches. This behavior allowed false
    positive like:

        assert_template "layout"
        assert_template "out/hello"

    Now it only passes with:

        assert_template "layout/hello"
        assert_template "hello"

    Fixes #3849.

    *Hugolnx*

*   Handle `ActionDispatch::Http::UploadedFile` like `Rack::Test::UploadedFile`, don't call to_param on it. Since
    `Rack::Test::UploadedFile` isn't API compatible this is needed to test file uploads that rely on `tempfile`
    being available.

    *Tim Vandecasteele*

*   Respect `config.digest = false` for `asset_path`

    Previously, the `asset_path` internals only respected the `:digest`
    option, but ignored the global config setting. This meant that
    `config.digest = false` could not be used in conjunction with
    `config.compile = false` this corrects the behavior.

    *Peter Wagenet*

*   Fix #7646, the log now displays the correct status code when an exception is raised.

    *Yves Senn*

*   Fix handling of date selects when using both disabled and discard options.
    Fixes #7431.

    *Vasiliy Ermolovich*

*   Fix select_tag when option_tags is nil.
    Fixes #7404.

    *Sandeep Ravichandran*

*   `javascript_include_tag :all` will now not include `application.js` if the file does not exists. *Prem Sichanugrist*

*   Support cookie jar options (e.g., domain :all) for all session stores.
    Fixes GH#3047, GH#2483.

    *Ravil Bayramgalin*

*   Performance Improvement to send_file: Avoid having to pass an open file handle as the response body. Rack::Sendfile
    will usually intercept the response and just uses the path directly, so no reason to open the file. This performance
    improvement also resolves an issue with jRuby encodings, and is the reason for the backport, see issue #6844.

    *Jeremy Kemper & Erich Menge*


## Rails 3.2.8 (Aug 9, 2012) ##

*   There is an XSS vulnerability in the strip_tags helper in Ruby on Rails, the
    helper doesn't correctly handle malformed html.  As a result an attacker can
    execute arbitrary javascript through the use of specially crafted malformed
    html.

    *Marek from Nethemba (www.nethemba.com) & Santiago Pastorino*

*   When a "prompt" value is supplied to the `select_tag` helper, the "prompt" value is not escaped.
    If untrusted data is not escaped, and is supplied as the prompt value, there is a potential for XSS attacks.
    Vulnerable code will look something like this:
    select_tag("name", options, :prompt => UNTRUSTED_INPUT)

    *Santiago Pastorino*

*   Reverted the deprecation of `:confirm`. *Rafael Mendon??a Fran??a*

*   Reverted the deprecation of `:disable_with`. *Rafael Mendon??a Fran??a*

*   Reverted the deprecation of `:mouseover` option to `image_tag`. *Rafael Mendon??a Fran??a*

*   Reverted the deprecation of `button_to_function` and `link_to_function` helpers.

    *Rafael Mendon??a Fran??a*


## Rails 3.2.7 (Jul 26, 2012) ##

*   Do not convert digest auth strings to symbols. CVE-2012-3424

*   Bump Journey requirements to 1.0.4

*   Add support for optional root segments containing slashes

*   Fixed bug creating invalid HTML in select options

*   Show in log correct wrapped keys

*   Fix NumberHelper options wrapping to prevent verbatim blocks being rendered instead of line continuations.

*   ActionController::Metal doesn't have logger method, check it and then delegate

*   ActionController::Caching depends on RackDelegation and AbstractController::Callbacks


## Rails 3.2.6 (Jun 12, 2012) ##

*   nil is removed from array parameter values

    CVE-2012-2694

*   Deprecate `:confirm` in favor of `':data => { :confirm => "Text" }'` option for `button_to`, `button_tag`, `image_submit_tag`, `link_to` and `submit_tag` helpers.

    *Carlos Galdino*

*   Allow to use mounted_helpers (helpers for accessing mounted engines) in ActionView::TestCase. *Piotr Sarnacki*

*   Include mounted_helpers (helpers for accessing mounted engines) in ActionDispatch::IntegrationTest by default. *Piotr Sarnacki*


## Rails 3.2.5 (Jun 1, 2012) ##

*   No changes.


## Rails 3.2.4 (May 31, 2012) ##

*   Deprecate old APIs for highlight, excerpt and word_wrap *Jeremy Walker*

*   Deprecate `:disable_with` in favor of `'data-disable-with'` option for `button_to`, `button_tag` and `submit_tag` helpers.

    *Carlos Galdino + Rafael Mendon??a Fran??a*

*   Deprecate `:mouseover` option for `image_tag` helper. *Rafael Mendon??a Fran??a*

*   Deprecate `button_to_function` and `link_to_function` helpers. *Rafael Mendon??a Fran??a*

*   Don't break Haml with textarea newline fix.  GH #393, #4000, #5190, #5191

*   Fix options handling on labels. GH #2492, #5614

*   Added config.action_view.embed_authenticity_token_in_remote_forms to deal
    with regression from 16ee611fa

*   Set rendered_format when doing render :inline. GH #5632

*   Fix the redirect when it receive blocks with arity of 1. Closes #5677

*   Strip [nil] from parameters hash. Thanks to Ben Murphy for
    reporting this! CVE-2012-2660


## Rails 3.2.3 (March 30, 2012) ##

*   Allow to lazy load `default_form_builder` by passing a `String` instead of a constant. *Piotr Sarnacki*

*   Fix #5632, render :inline set the proper rendered format. *Santiago Pastorino*

*   Fix textarea rendering when using plugins like HAML. Such plugins encode the first newline character in the content. This issue was introduced in https://github.com/rails/rails/pull/5191 *James Coleman*

*   Remove the leading \n added by textarea on assert_select. *Santiago Pastorino*

*   Add `config.action_view.embed_authenticity_token_in_remote_forms` (defaults to true) which allows to set if authenticity token will be included by default in remote forms. If you change it to false, you can still force authenticity token by passing `:authenticity_token => true` in form options *Piotr Sarnacki*

*   Do not include the authenticity token in forms where remote: true as ajax forms use the meta-tag value *DHH*

*   Turn off verbose mode of rack-cache, we still have X-Rack-Cache to
    check that info. Closes #5245. *Santiago Pastorino*

*   Fix #5238, rendered_format is not set when template is not rendered. *Piotr Sarnacki*

*   Upgrade rack-cache to 1.2. *Jos?? Valim*

*   ActionController::SessionManagement is deprecated. *Santiago Pastorino*

*   Since the router holds references to many parts of the system like engines, controllers and the application itself, inspecting the route set can actually be really slow, therefore we default alias inspect to to_s. *Jos?? Valim*

*   Add a new line after the textarea opening tag. Closes #393 *Rafael Mendon??a Fran??a*

*   Always pass a respond block from to responder. We should let the responder to decide what to do with the given overridden response block, and not short circuit it. *sikachu*

*   Fixes layout rendering regression from 3.2.2. *Jos?? Valim*


## Rails 3.2.2 (March 1, 2012) ##

*   Format lookup for partials is derived from the format in which the template is being rendered. Closes #5025 part 2 *Santiago Pastorino*

*   Use the right format when a partial is missing. Closes #5025. *Santiago Pastorino*

*   Default responder will now always use your overridden block in `respond_with` to render your response. *Prem Sichanugrist*

*   check_box helper with :disabled => true will generate a disabled hidden field to conform with the HTML convention where disabled fields are not submitted with the form.
    This is a behavior change, previously the hidden tag had a value of the disabled checkbox.
    *Tadas Tamosauskas*


## Rails 3.2.1 (January 26, 2012) ##

*   Documentation improvements.

*   Allow `form.select` to accept ranges (regression). *Jeremy Walker*

*   `datetime_select` works with -/+ infinity dates. *Joe Van Dyk*


## Rails 3.2.0 (January 20, 2012) ##

*   Setting config.assets.logger to false turn off Sprockets logger *Guillermo Iguaran*

*   Add `config.action_dispatch.default_charset` to configure default charset for ActionDispatch::Response. *Carlos Antonio da Silva*

*   Deprecate setting default charset at controller level, use the new `config.action_dispatch.default_charset` instead. *Carlos Antonio da Silva*

*   Deprecate ActionController::UnknownAction in favour of AbstractController::ActionNotFound. *Carlos Antonio da Silva*

*   Deprecate ActionController::DoubleRenderError in favour of AbstractController::DoubleRenderError. *Carlos Antonio da Silva*

*   Deprecate method_missing handling for not found actions, use action_missing instead. *Carlos Antonio da Silva*

*   Deprecate ActionController#rescue_action, ActionController#initialize_template_class, and ActionController#assign_shortcuts.
    These methods were not being used internally anymore and are going to be removed in Rails 4. *Carlos Antonio da Silva*

*   Add config.assets.logger to configure Sprockets logger *Rafael Fran??a*

*   Use a BodyProxy instead of including a Module that responds to
    close. Closes #4441 if Active Record is disabled assets are delivered
    correctly *Santiago Pastorino*

*   Rails initialization with initialize_on_precompile = false should set assets_dir *Santiago Pastorino*

*   Add font_path helper method *Santiago Pastorino*

*   Depends on rack ~> 1.4.0 *Santiago Pastorino*

*   Add :gzip option to `caches_page`. The default option can be configured globally using `page_cache_compression` *Andrey Sitnik*

*   The ShowExceptions middleware now accepts a exceptions application that is responsible to render an exception when the application fails. The application is invoked with a copy of the exception in `env["action_dispatch.exception"]` and with the PATH_INFO rewritten to the status code. *Jos?? Valim*

*   Add `button_tag` support to ActionView::Helpers::FormBuilder.

    This support mimics the default behavior of `submit_tag`.

    Example:

        <%= form_for @post do |f| %>
          <%= f.button %>
        <% end %>

*   Date helpers accept a new option, `:use_two_digit_numbers = true`, that renders select boxes for months and days with a leading zero without changing the respective values.
    For example, this is useful for displaying ISO8601-style dates such as '2011-08-01'. *Lennart Frid??n and Kim Persson*

*   Make ActiveSupport::Benchmarkable a default module for ActionController::Base, so the #benchmark method is once again available in the controller context like it used to be *DHH*

*   Deprecated implied layout lookup in controllers whose parent had a explicit layout set:

        class ApplicationController
          layout "application"
        end

        class PostsController < ApplicationController
        end

    In the example above, Posts controller will no longer automatically look up for a posts layout.

    If you need this functionality you could either remove `layout "application"` from ApplicationController or explicitly set it to nil in PostsController. *Jos?? Valim*

*   Rails will now use your default layout (such as "layouts/application") when you specify a layout with `:only` and `:except` condition, and those conditions fail. *Prem Sichanugrist*

    For example, consider this snippet:

        class CarsController
          layout 'single_car', :only => :show
        end

    Rails will use 'layouts/single_car' when a request comes in `:show` action, and use 'layouts/application' (or 'layouts/cars', if exists) when a request comes in for any other actions.

*   form_for with +:as+ option uses "#{action}_#{as}" as css class and id:

    Before:

        form_for(@user, :as => 'client') # => "<form class="client_new">..."

    Now:

        form_for(@user, :as => 'client') # => "<form class="new_client">..."

    *Vasiliy Ermolovich*

*   Allow rescue responses to be configured through a railtie as in `config.action_dispatch.rescue_responses`. Please look at ActiveRecord::Railtie for an example *Jos?? Valim*

*   Allow fresh_when/stale? to take a record instead of an options hash *DHH*

*   Assets should use the request protocol by default or default to relative if no request is available *Jonathan del Strother*

*   Log "Filter chain halted as CALLBACKNAME rendered or redirected" every time a before callback halts *Jos?? Valim*

*   You can provide a namespace for your form to ensure uniqueness of id attributes on form elements.
    The namespace attribute will be prefixed with underscore on the generate HTML id. *Vasiliy Ermolovich*

    Example:

        <%= form_for(@offer, :namespace => 'namespace') do |f| %>
          <%= f.label :version, 'Version' %>:
          <%= f.text_field :version %>
        <% end %>

*   Refactor ActionDispatch::ShowExceptions. The controller is responsible for choosing to show exceptions when `consider_all_requests_local` is false.

    It's possible to override `show_detailed_exceptions?` in controllers to specify which requests should provide debugging information on errors. The default value is now false, meaning local requests in production will no longer show the detailed exceptions page unless `show_detailed_exceptions?` is overridden and set to `request.local?`.

*   Responders now return 204 No Content for API requests without a response body (as in the new scaffold) *Jos?? Valim*

*   Added ActionDispatch::RequestId middleware that'll make a unique X-Request-Id header available to the response and enables the ActionDispatch::Request#uuid method. This makes it easy to trace requests from end-to-end in the stack and to identify individual requests in mixed logs like Syslog *DHH*

*   Limit the number of options for select_year to 1000.

    Pass the :max_years_allowed option to set your own limit.

    *Libo Cannici*

*   Passing formats or handlers to render :template and friends is deprecated. For example: *Nick Sutterer & Jos?? Valim*

        render :template => "foo.html.erb"

    Instead, you can provide :handlers and :formats directly as option:
             render :template => "foo", :formats => [:html, :js], :handlers => :erb

*   Changed log level of warning for missing CSRF token from :debug to :warn. *Mike Dillon*

*   content_tag_for and div_for can now take the collection of records. It will also yield the record as the first argument if you set a receiving argument in your block *Prem Sichanugrist*

    So instead of having to do this:

        @items.each do |item|
          content_tag_for(:li, item) do
             Title: <%= item.title %>
          end
        end

    You can now do this:

        content_tag_for(:li, @items) do |item|
          Title: <%= item.title %>
        end

*   send_file now guess the mime type *Esad Hajdarevic*

*   Mime type entries for PDF, ZIP and other formats were added *Esad Hajdarevic*

*   Generate hidden input before select with :multiple option set to true.
    This is useful when you rely on the fact that when no options is set,
    the state of select will be sent to rails application. Without hidden field
    nothing is sent according to HTML spec *Bogdan Gusiev*

*   Refactor ActionController::TestCase cookies *Andrew White*

    Assigning cookies for test cases should now use cookies[], e.g:

        cookies[:email] = 'user@example.com'
        get :index
        assert_equal 'user@example.com', cookies[:email]

    To clear the cookies, use clear, e.g:

        cookies.clear
        get :index
        assert_nil cookies[:email]

    We now no longer write out HTTP_COOKIE and the cookie jar is
    persistent between requests so if you need to manipulate the environment
    for your test you need to do it before the cookie jar is created.

*   ActionController::ParamsWrapper on ActiveRecord models now only wrap
    attr_accessible attributes if they were set, if not, only the attributes
    returned by the class method attribute_names will be wrapped. This fixes
    the wrapping of nested attributes by adding them to attr_accessible.

Please check [3-1-stable](https://github.com/rails/rails/blob/3-1-stable/actionpack/CHANGELOG.md) for previous changes.
