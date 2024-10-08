---
title: "Change Action Mailer Settings From Rails Console"
date: 2024-10-07T21:09:36-07:00
lastmod: 2024-10-07T21:09:36-07:00
draft: false
keywords: ["rails", "mailer", "action-mailer"]
description: "Change action mailer settings from rails console"
tags: ["rails", "mailer", "action-mailer"]
categories: ["rails"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

In Rails applications, ActionMailer is a powerful module used to send emails. It’s essential to configure your ActionMailer settings correctly to ensure that your application can send emails reliably through your chosen SMTP server. Typically, these settings are configured in your environment files (e.g., config/environments/production.rb). However, there may be instances where you need to override these settings temporarily through the Rails console—for example, when testing a custom configuration or troubleshooting email delivery issues.

<!--more-->

To override the ActionMailer settings, start by accessing your Rails console. Open your terminal and run the following command:

```sh
rails console
```

Once in the console, you can override the settings by directly setting the ActionMailer::Base.smtp_settings. Here’s an example configuration:

```rb
ActionMailer::Base.smtp_settings = {
  address:              'smtp.office365.com',
  port:                 587,
  user_name:            'email@example.com'
  password:             'passwords',
  authentication:       :login,
  enable_starttls_auto: true,
  read_timeout: 60
}
```

You can override all the options available [here](https://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-configuration:~:text=and%20Log4r%20loggers.-,smtp_settings,-Allows%20detailed%20configuration) through this.

Once you’ve set the smtp_settings, you can test if everything is configured correctly by sending a test email directly from the console. Here's how you can do it:

```rb
DeveloperMailer.any_mailer('you@youremail.com').deliver_now
```

This example assumes you have a mailer (DeveloperMailer) and an email method (any_mailer) set up in your application. If the email sends successfully, it indicates that the settings are correct.

It’s important to remember that changes made in the Rails console are temporary and will be lost once you exit the console or restart your server. If you need to persist these settings, you should update your environment files.
