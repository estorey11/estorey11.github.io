---
layout: post
title:      "Omniauth and bcrypt has_a_secure_password error"
date:       2019-06-19 17:54:30 +0000
permalink:  omniauth_and_bcrypt_has_a_secure_password_error
---


If you allow users to log into your Rails app using either Omniauth or your own built-in login with bcrypt, you may run into a problem. Your user model likely has the has_secure_password method from bcrypt, which requires that a password is present upon creation. However, if a user creates an account through a third-party login using Omniauth, no password will be created for them and the user instance will not be able to be saved. The simplest solution that I have found to this problem is to assign the user a stand in password wherever your user is created from an Omniauth login. For example:
```

 def self.from_omniauth(auth)
     where(username: auth.info.name).first_or_initialize do |user|
        user.username = auth.info.name
        user.password = user.password_confirmation= not_a_secure_password
      end
 end
```

However, this is obviously not ideal as you are setting the same password for every user that logs in through Omniauth. If someone discovered the password for a single Omniauth login in user then the account safety would be comprimised for *every* Omniauth user. A much more secure way to solve this problem is :

`user.password = user.password_confirmation = SecureRandom.urlsafe_base64(n=6)`

This will generate a different, random password for every Omniauth account creation. The password may be gibberish, but that hardly matters because the Omniauth login user should never have to know it or type it in anywhere, as they will be logging in through a third party.
