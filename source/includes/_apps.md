# Apps

With the Quaderno API, it’s easy to build a partner integration to add tax management and billing features to your product.

For example, if you are a marketplace, you can integrate sales tax calculation using the Quaderno API so your users can calculate taxes automatically in their checkout forms depending on their customers' locations.

<aside class="info">
<strong>Are you a marketplace platform?</strong> <a href="https://support.quaderno.io/quaderno-connect" target="_blank">Quaderno Connect</a> provides tax management and invoicing capabilities to your business model! Go check this <a href="https://support.quaderno.io/integrating-connect-with-standard-accounts" target="_blank">step-by-step guide</a> for details on using Quaderno Apps with Quaderno Connect Standard accounts.
</aside>

But it’s not just marketplaces that work great with Quaderno - we’ve seen partner integrations built with all kinds of software from tools to manage sport clubs to WordPress blogs.

Integrating with Quaderno has the power to add huge value for your users, streamlining their businesses processes and providing a great experience.

Every partner integration is different, so this document can’t tell you exactly what to do, but we’ll explain the flows you should handle, and give you some ideas of how to provide the best experience for your users.

## Connecting your users' accounts

To calculate taxes or issue receipts for your users, you’ll need access to their Quaderno account so you can make API requests on their behalf.

Our OAuth flow allows you to securely obtain an access token for a Quaderno account without having to have your user’s password or do any manual setup.

There are four key benefits to getting access to your users’ accounts using OAuth:

- **It’s secure**: Your users have a secure way to grant you access to their account without needing to send you their login credentials or an access token. They only need to tell us that you may have access through a simple online process.
- **It’s fast**: You only need to generate and send your user to a link to our OAuth flow. From there, they’ll create a Quaderno account or sign in to their existing one, approve your access to their account, and then we’ll send them right back to you with an access token.
- **It’s future-proof**: As the Quaderno API gets better and better over time and offers new functionality, you’ll be able to stay up-to-date and use all the latest features

The process for your user looks like this:

1. Your user clicks a button in your UI to start the process of connecting with Quaderno.
2. You generate a link to the authorization page, embedding details of your app (which we’ll set up next) as well as a scope to specify the level of access you want your user to give you.
3. You redirect your user to the generated link. Your user lands on our site, where they create a Quaderno account or log in to their existing one, and agree to grant your app access.
4. We redirect your user’s browser back to you with a code query parameter.
5. You exchange the code for a permanent access token, which you store so that you can use it to make API requests on your user’s behalf.

## Creating an app

Before we can get started, you need to create an "app" which represents your partner integration in the Quaderno system. You’ll be issued with a client ID and secret, which you’ll use to identify yourself when sending users to the OAuth flow and swapping codes for access tokens.

First [sign up for a Quaderno account](https://sandbox-quadernoapp.com/signup) in our sandbox testing environment and then [create an app](https://sandbox-quadernoapp.com/oauth/applications/new).

You’ll need to provide a name and the URL of your website (where users can go to read more about your product or your integration with Quaderno). 

Once your user has completed the OAuth flow, they will be redirected to your application using the `redirect_url` parameter you provide. For security reasons, this can’t be just any internet URL, so you will also need to configure one callback URL here (which must exactly match the parameter). 

Once you’ve created an app, you’ll see it in the [list of your apps](https://sandbox-quadernoapp.com/oauth/applications). Click on your newly-created app, and take a note of the Client ID and Client Secret.

## Generating a link to the authorization page

First, you need to install an Oauth client library. For example, in Ruby you can use the oauth2 gem:

```ruby
echo "gem 'oauth2'" >> Gemfile
bundle install
```

Once we’ve installed the library, we can build a link to the authorization page. We’ll have to pass in a number of parameters, including the `client_id` and `client_secret`, as well as a few parameters that will vary by library. These can include:

Option             | Required | Description
-------------------|----------|---------------------------------------------------------
redirect_uri       | Yes      | The URL to send your users to once they've agreed to connect their account to Quaderno (as well if they deny authorisation or something goes wrong, in which case we'll pass you details of the error). This must exactly match one of the callback URLs you specified above.
scope              | No      | The level of access you want to your users' Quaderno accounts - this may either be `read_write` or `read_only`. Default is `read_only`. 
state              | No       | Any value you pass in here will be included as a querystring parameter when we redirect back to your redirect URL. Please note that this value can be tampered with by your user, and so shouldn't be trusted implicitly. We recommend using this parameter for a CSRF token.

```ruby
require 'oauth2'

# You should store your client ID and secret in environment variables rather than
# committing them with your code
oauth = OAuth2::Client.new(ENV['QUADERNO_CLIENT_ID'],
                           ENV['QUADERNO_CLIENT_SECRET'],
                           site: 'https://sandbox-quadernoapp.com',
                           authorize_url: '/oauth/authorize',
                           token_url: '/oauth/token')

authorize_url = oauth.auth_code.authorize_url(redirect_uri: 'https://yourapp.com/redirect', 
                                              scope: 'read_only')

redirect_to authorize_url
```

Run this code to generate a link to the OAuth flow. Head over to the link you’ve just generated in an Incognito window, and you’ll see our OAuth flow.

## Getting an access token 

On the signup form, we recommend creating a second sandbox account using a different email address. This will replicate what your users will experience when connecting to your partner integration, and will give you an example account which you can use from the perspective of one of your users.

Once your user has either signed up or logged in, and then approved your app’s access to their account, they’ll be sent to your app’s redirect URI with a temporary code which you’ll see in the query parameters, as well as any state you provided.

You should use the OAuth client library we set up earlier to fetch an access token using the code. This is a permanent access token which allows you to use the API on behalf of your merchant at any time.

```ruby
require 'oauth2'

oauth = OAuth2::Client.new(ENV['QUADERNO_CLIENT_ID'],
                           ENV['QUADERNO_CLIENT_SECRET'],
                           site: 'https://sandbox-quadernoapp.com',
                           authorize_url: '/oauth/authorize',
                           token_url: '/oauth/token')

response = oauth.auth_code.get_token(
  params[:code], # use exactly the same access token as you did in the last step
  redirect_uri: 'https://yourapp.com/redirect'
)

# You should store the user's access token - you'll need it to make API requests on their
# behalf in future. If you want to handle webhooks for your users, you should also store
# their account ID which will allow you to identify webhooks belonging to them.
current_user.update!(quaderno_access_token: response.token,
                     quaderno_account_id: response['account_id'])
```

Whether you are developing a mobile, web, or desktop application, it is important not to pass the client secret to your user’s device as it could be used to impersonate your app. The process of exchanging a code for an access token should be done on your server so your client secret can be kept private.

You’ll want to store this access token in your database for use in the future to make requests to the Quaderno API on your user’s behalf. Make sure you keep it safe and secure, as it gives full access to your user’s account.

## Revoking access

To disconnect an account from your platform, POST your `client_id`, your `client_token`, and the user's access token to `/oauth/revoke`

```ruby
  HTTParty.post('https:/sandbox-quadernoapp.com/oauth/revoke', body: {
      client_id: ENV['QUADERNO_CLIENT_ID'],
      client_secret: ENV['QUADERNO_CLIENT_SECRET'],
      token: USER_TOKEN
    })
```
