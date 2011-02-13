Provides connection support for Facebook and Twitter to Symfony2 projects.

Installation
============

** Requires Facebook PHP SDK, and Twitter-OAuth:**

    # Facebook
    git clone git://github.com/facebook/php-sdk.git vendor/facebook

    # Twitter OAuth
    git clone git://github.com/ruudk/twitteroauth.git vendor/twitteroauth


**Add OAuthBundle to your src/Bundle dir**

You can download it from here http://excelwebzone.github.com/OAuthBundle

**Add OAuthBundle to your application kernel:**

    // app/AppKernel.php
    public function registerBundles()
    {
        return array(
            // ...
            new Bundle\OAuthBundle\OAuthBundle(),
            // ...
        );
    }

**Add TwitterOAuth to the autoload file.**

    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        // ...
        'TwitterOAuth' => $vendorDir.'/twitteroauth',
        // ...
    ));


**Add your service(s) in your configuration file.**

    // app/config/config.yml
    oauth.config:
        facebook:
            app_id:    __APPID__
            secret:   __SECRET__

        twitter:
            key:      __KEY__
            secret:   __SECRET__
    ...
    

How to use
----------

In the controller we have some action. In this action we retrieve the login url base on the provider
and redirect to it. 

    public function someAction(){
        ...

        // load service
        $service = $this->get('oauth.facebook');

        $loginUrl = $service->getLoginUrl(
            $this->generateUrl('ALLOW_URL', array('provider' => 'facebook'), true),
            $this->generateUrl('DENIED_URL', array('provider' => 'facebook', 'denied' => 't'), true),
            array(
                'display'   => 'popup',
                'req_perms' => 'email,offline_access',
            )
        );

        return $this->redirect($loginUrl);
    }

Once return to the ALLOW_URL, we can then get all the profile information by using:

    public function allowAction()
    {
        ...

        // load service
        $service = $this->get('oauth.facebook');

        if (!$profile = $service->getProfile()) {
            return $this->createResponse('We couldn&#039;t connect you to Facebook at this time, please try again.');
        }

        // DO SOMETHING WITH $profile
        ...
    }

In addition there is a way to retrieve the profile Friends list:

        // load service
        $service = $this->get('oauth.facebook');

        $friends = $service->getFriends($profile['id'], $profile['access_token']);
