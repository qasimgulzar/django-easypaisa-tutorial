# How to setup easypaisa with django.

    1. pip install git+https://github.com/qasimgulzar/easypaisa_module
    2. add django_easypaisa in INSTALLED_APPS
        `INSTALLED_APPS = [
            'django.contrib.admin',
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.messages',
            'django.contrib.staticfiles',
            'main',
            'rest_framework',
            'django_easypaisa'
        ]`
    3. add django-easypaisa urls to your application's urls file
        url(r'^payments/', include('django_easypaisa.urls', namespace='django_easypaisa'))
    4. set parameters settings\
        STORE_ID=os.environ.get("STORE_ID",None) ###you will get store id easypaisa's merchant portal.
        PAYMEN_TEMPLATE='index.html' ###set payment confirmation template which will also post data t easypaisa's server for first redirection.
        PAY_TEMPLATE='pay.html' ### set template for second redirection which will post auth_token to easypaisa for second redirection.
        EASYPAISA_POST_BACK_URL=os.environ.get('EASYPAISA_POST_BACK_URL','https://easypaisa.herokuapp.com/payments/postbackhandler/')
        EASYPAISA_SERVER_URL=os.environ.get('EASYPAISA_SERVER_URL','https://easypaystg.easypaisa.com.pk')
        PAYMENT_COMPLETION_REDIRECT_TO_URL='/'
    
    5. Make migrations `python manage.py makemigrations` 
    6. Run migrations `python manage.py migrate`

##4.1 Content for PAYMEN_TEMPLATE
    <form action="{{ EASYPAISA_SERVER_URL }}/easypay/Index.jsf " method="POST" target="_blank">
            <! -- Store Id Provided by Easypay-->
            <input name="storeId" value="2785" hidden = "true"/>
            <! -- Amount of Transaction from merchant’s website -->
            <input name="amount" value="10" hidden = "true"/>
            <! – Post back URL from merchant’s website -- >
            <input name="postBackURL" value="{{ postBackURL }}" hidden = "true"/>
            <! – Order Reference Number from merchant’s website -- >
            <input name="orderRefNum" value="1212"/>
            <! – Expiry Date from merchant’s website -- >
    {#        <input type ="hidden" name="expiryDate" value="20140606 201521">#}
            <! – This is the button of the form which submits the form -- >
            <input type = "image" src="checkout-button-with-logo.png" border="0" name= "pay">
    </form>
    
##4.2 Content for PAY_TEMPLATE
    <form action="{{ EASYPAISA_SERVER_URL }}/easypay/Confirm.jsf " method="POST" target="_blank">
        <input name="auth_token" value="{{ auth_token }}" hidden="true"/>
        <input name="postBackURL" value="{{ postBackURL }}" hidden="true"/>
        <input value="confirm" type="submit" name="pay"/>
    </form>
    