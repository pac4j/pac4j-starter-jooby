[![Build Status](https://travis-ci.org/jooby-project/pac4j-starter.svg?branch=master)](https://travis-ci.org/jooby-project/pac4j-starter)
# pac4j starter

<a href="http://www.pac4j.org/2.2.x/docs/index.html">Pac4j</a> Starter project.

## quick preview

This project contains a simple application that:

* Shows you how to login with

    * Facebook
    * Twitter
    * Google
    * Custom login form
 

[App.java](https://github.com/jooby-project/pac4j-starter/blob/master/src/main/java/starter/pac4j/App.java):

```java
public class App extends Jooby {

  {
    assets("/css/**");
    assets("/images/**");

    /** Template engine: */
    use(new Hbs());

    get("/login", () -> Results.html("login"));

    use(new Pac4j()
        /** Login with facebook: */
        .client("/facebook", conf ->
            new FacebookClient(conf.getString("fb.key"), conf.getString("fb.secret"))
        )
        /** Login with twitter: */
        .client("/twitter", conf ->
            new TwitterClient(conf.getString("twitter.key"), conf.getString("twitter.secret"))
        )
        /** Login with google: */
        .client("/google", conf -> {
          OidcConfiguration oidc = new OidcConfiguration();
          oidc.setClientId(conf.getString("oidc.clientId"));
          oidc.setSecret(conf.getString("oidc.secret"));
          oidc.setDiscoveryURI(conf.getString("oidc.discoveryURI"));
          oidc.addCustomParam("prompt", "consent");
          return new OidcClient(oidc);
        })
        /** Fallback to form login: */
        .client(conf ->
            new FormClient("/login", ((credentials, context) -> {
              // Create default profile:
              String username = ((UsernamePasswordCredentials) credentials).getUsername();
              final CommonProfile profile = new CommonProfile();
              profile.setId(username);
              profile.addAttribute(Pac4jConstants.USERNAME, username);
              profile.addAttribute(CommonProfileDefinition.DISPLAY_NAME, username);
              credentials.setUserProfile(profile);
            }))
        ));

    /** Protected pages: */
    get("/", "/profile", () -> {
      UserProfile profile = require(UserProfile.class);
      return Results.html("profile").put("profile", profile);
    });
  }

  public static void main(final String[] args) {
    run(App::new, args);
  }

}
```

## run

    mvn jooby:run

Open a browser and type:

    http://localhost:8080/profile

## screenshot

![Pac4j Starter](https://github.com/jooby-project/pac4j-starter/raw/master/public/images/pac4jstarter.png)

## help

* Read the [pac4j documentation](http://www.pac4j.org/2.2.x/docs/index.html)
* Read the [pac4j module documentation](http://jooby.org/doc/pac4j2)
* Read the [jooby documentation](http://jooby.org/doc)
* Join the [channel](https://gitter.im/jooby-project/jooby)
* Join the [group](https://groups.google.com/forum/#!forum/jooby-project)
