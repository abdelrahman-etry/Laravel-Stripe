# Laravel Stripe Integration
Simple stripe integration with laravel framework
![LaravelStripe](https://abdelrahmanetry.info/assets/github/sl.png)

## Requirements
- Create a development stripe account from this link : https://stripe.com/
- Locate Both `public_key` and `secret_key` in your profile.

## Usage
You can clone this repository for a simple stripe integration sample or you can do the following:-

1. Create new laravel project by running the following command:
    ```bash
    composer create-project laravel/laravel stripe-integration
    ```
2. Go to your project's directory 
    ```bash
    cd stripe-integration
    ```
3. Install Stripe-php integration laravel package to your project 
    ```bash
    composer require stripe/stripe-php
    ```
5. Set the secret credentials provided by stripe in you `.env` file by adding the following
    ```
    STRIPE_KEY=pk_test_xxxxxxxxxxxxxxxxxxx 
    STRIPE_SECRET=sk_test_xxxxxxxxxxxxxx 
    ```
4. Next you need to set up the Stripe API key, open or create **config/services.php** file and add the following :
    ~~~php
    'stripe' => [
        'secret' => env('STRIPE_SECRET'),
    ],
    ~~~

5. Now run the following command to create the controller:
    ```bash
    php artisan make:controller StripeController
    ```
    then place the following code
    ```php
        <?php
        namespace App\Http\Controllers;
        use Illuminate\Http\Request;
        use Session;
        use Stripe;
    
        class StripeController  extends Controller{
            /**
            * success response method.
            *
            * @return \Illuminate\Http\Response
            */
            public function stripe(){
                return view('stripe');
            }
       
            /**
            * success response method.
            *
            * @return \Illuminate\Http\Response
            */
            public function stripePost(Request $request){
                Stripe\Stripe::setApiKey(env('STRIPE_SECRET'));
                Stripe\Charge::create ([
                    "amount" => 100 * 100,
                    "currency" => "usd",
                    "source" => $request->stripeToken,
                    "description" => "This payment is tested purpose phpcodingstuff.com"
                ]);
     
                Session::flash('success', 'Payment successful!');
                return back();
            }
        }
    ```

6. Then you'll need to create a blade view by creating a file named `stripe.blade.php` inside **resources/views folder**
    ```html
    <!DOCTYPE html>
    <html>
        <head>
            <title>Stripe Payment Page - HackTheStuff</title>
            <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
            <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
            <style type="text/css">
                .panel-title {
                    display: inline;
                    font-weight: bold;
                }
                .display-table {
                    display: table;
                }
                .display-tr {
                    display: table-row;
                }
                .display-td {
                    display: table-cell;
                    vertical-align: middle;
                    width: 61%;
                }
            </style>
        </head>
   
        <body>
            <div class="container">
                <h1>Stripe Payment Page - HackTheStuff</h1>
                
                <div class="row">
                    <div class="col-md-6 col-md-offset-3">
                        <div class="panel panel-default credit-card-box">
                            <div class="panel-heading display-table">
                                <div class="row display-tr">
                                    <h3 class="panel-title display-td" >Payment Details</h3>
                                    <div class="display-td">                            
                                        <img class="img-responsive pull-right" src="http://i76.imgup.net/accepted_c22e0.png">
                                    </div>
                                </div>
                            </div>
                  
                            <div class="panel-body">
                                @if (Session::has('success'))
                                <div class="alert alert-success text-center">
                                    <a href="#" class="close" data-dismiss="alert" aria-label="close">×</a>
                                    <p>{{ Session::get('success') }}</p>
                                </div>
                                @endif
                                <form role="form" action="{{ route('stripe.post') }}" method="post" class="require-validation" data-cc-on-file="false" data-stripe-publishable-key="{{env('STRIPE_KEY') }}" id="payment-form">
                                    @csrf
                                    <div class='form-row row'>
                                        <div class='col-xs-12 form-group required'>
                                            <label class='control-label'>Name on Card</label>
                                            <input class='form-control' size='4' type='text'>
                                        </div>
                                    </div>
                        
                                    <div class='form-row row'>
                                        <div class='col-xs-12 form-group card required'>
                                            <label class='control-label'>Card Number</label>
                                            <input autocomplete='off' class='form-control card-number' size='20' type='text'>
                                        </div>
                                    </div>
                        
                                    <div class='form-row row'>
                                        <div class='col-xs-12 col-md-4 form-group cvc required'>
                                            <label class='control-label'>CVC</label>
                                            <input autocomplete='off' class='form-control card-cvc' placeholder='ex. 311' size='4' type='text'>
                                        </div>
                           
                                        <div class='col-xs-12 col-md-4 form-group expiration required'>
                                            <label class='control-label'>Expiration Month</label>
                                            <input class='form-control card-expiry-month' placeholder='MM' size='2' type='text'>
                                        </div>
                           
                                        <div class='col-xs-12 col-md-4 form-group expiration required'>
                                            <label class='control-label'>Expiration Year</label>
                                            <input class='form-control card-expiry-year' placeholder='YYYY' size='4' type='text'>
                                        </div>
                                    </div>
                        
                                    <div class='form-row row'>
                                        <div class='col-md-12 error form-group hide'>
                                            <div class='alert-danger alert'>Please correct the errors and try again. </div>
                                        </div>
                                    </div>
                        
                                    <div class="row">
                                        <div class="col-xs-12">
                                            <button class="btn btn-primary btn-lg btn-block" type="submit">Pay Now ($100)</button>
                                        </div>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </body>
        
        <script type="text/javascript" src="https://js.stripe.com/v2/"></script>
        <script type="text/javascript">
            $(function() {
                var $form = $(".require-validation");
                $('form.require-validation').bind('submit', function(e) {
                    var $form = $(".require-validation"), 
                    inputSelector = ['input[type=email]', 'input[type=password]', 'input[type=text]', 'input[type=file]', 'textarea'].join(', '),
                    $inputs = $form.find('.required').find(inputSelector),
                    $errorMessage = $form.find('div.error'),
                    valid = true;
                    $errorMessage.addClass('hide');
                    $('.has-error').removeClass('has-error');
        
                    $inputs.each(function(i, el) {
                        var $input = $(el);
                        if ($input.val() === '') {
                            $input.parent().addClass('has-error');
                            $errorMessage.removeClass('hide');
                            e.preventDefault();
                        }
                    });
        
                    if(!$form.data('cc-on-file')){
                        e.preventDefault();
                        Stripe.setPublishableKey($form.data('stripe-publishable-key'));
                        Stripe.createToken({
                            number: $('.card-number').val(),
                            cvc: $('.card-cvc').val(),
                            exp_month: $('.card-expiry-month').val(),
                            exp_year: $('.card-expiry-year').val()
                        }, stripeResponseHandler);
                    }
                });
    
                function stripeResponseHandler(status, response) {
                    if(response.error) {
                        $('.error').removeClass('hide').find('.alert').text(response.error.message);
                    }else{
                        /* token contains id, last4, and card type */
                        var token = response['id'];
                        $form.find('input[type=text]').empty();
                        $form.append("<input type='hidden' name='stripeToken' value='" + token + "'/>");
                        $form.get(0).submit();
                    }
                }
            });
        </script>
    </html>
    ```
7. Finally, create the routes by going to **routes/web.php** and paste the following
    ```php 
    Route::get('/'      ,   'StripeController@index');
    Route::post('stripe',   'StripeController@store')->name('stripe.post');
    ```

8. You can test your code by running the **artisan serve command** 
    ```bash 
    php artisan serve 
    ```

## Testing Card Credentials
- Card No: 4242424242424242
- Month: any future month
- Year: any future Year
- CVV: 123



![Abdelrahman Etry](https://abdelrahmanetry.info/assets/github/logo-tp.png)