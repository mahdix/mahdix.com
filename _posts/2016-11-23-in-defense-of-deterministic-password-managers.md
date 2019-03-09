---
id: 307
title: In defense of deterministic password managers
date: 2016-11-23T22:18:15+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=307
permalink: /blog/2016/11/23/in-defense-of-deterministic-password-managers/
categories:
  - Uncategorized
---
**tldr;** Although they have some shortcomings, deterministic password management is a great idea with a lot of benefits!

Today I read [this](https://tonyarcieri.com/4-fatal-flaws-in-deterministic-password-managers) post which was trying to convince people to switch to stateful password managers which store all passwords in some central data storage locally or on the cloud. I have to admit that the article had some valid points, but generally, it did poorly to convince me that deterministic password managers are bad.

First let me explain what a non-deterministic password manager is.

Normally, when you register in a website, you use a password manager to keep track of all of your passwords. So after signing up for a new email account (e.g. `mahdix@gmail.com`), you will store email id and it&#8217;s password (which is chosen by you), in the password manager&#8217;s database.

<figure id="attachment_312" style="width: 877px" class="wp-caption alignnone"><a href="http://www.mahdix.com/wp-content/uploads/2016/11/password-manager.jpg" rel="attachment wp-att-312"><img class="wp-image-312 size-full" src="http://www.mahdix.com/wp-content/uploads/2016/11/password-manager.jpg" alt="password-manager" width="877" height="509" /></a><figcaption class="wp-caption-text">How password manager works? [Image taken from Zoho.com website]</figcaption></figure>Obviously in this scheme, the central database becomes the most important component which needs extra protection. Where will this database be stored? Either in the service provider&#8217;s servers which is out of your control, or locally on your laptop or mobile phone. So if there is a data breach or your mobile phone is infected with a virus, your passwords can be in wrong hands! Of course there are a number of security measures to protect confidentiality of these data but as long as you have no control over them, there is a risk.

What is the solution? There has recently been a new way to manage password which I call deterministic password management (or DPM for short). This means that there is no central password database. Everything is inside your head! There is no need to have a cloud-based server to store your passwords. Generally, you have a basic passphrase which I call the &#8220;master password&#8221;. All other passwords are generated uniquely using a formula based on &#8220;master password&#8221; and some other public information. As a result, you don&#8217;t need to memorize 100 passwords or save them anywhere! Just memorize the master password and the reset can be automated.

For example for website &#8220;gmail.com&#8221; and username &#8220;mahdix&#8221; the password will be calculated using some formula like this:

    password = Hash(master_password, 'gmail.com', 'mahdix')

<a href="http://www.mahdix.com/wp-content/uploads/2016/11/password-managers-no-storage.png" rel="attachment wp-att-313"><img class="alignnone size-full wp-image-313" src="http://www.mahdix.com/wp-content/uploads/2016/11/password-managers-no-storage.png" alt="password-managers-no-storage" width="960" height="690" /></a>

The most important advantage of this scheme is that there is no need to store sensitive data anywhere! I would like to stress the term &#8220;sensitive&#8221; because later I will refer to this feature. The other advantage is that your password will all be strong passwords because they are generated using a hash function which causes the result to be semi-random. There will no longer be passwords like &#8220;letmein&#8221; or &#8220;admin&#8221; in this scheme.

### What are flaws of Deterministic Password Managers?

Now, back to the initial point! The [blog post](https://tonyarcieri.com/4-fatal-flaws-in-deterministic-password-managers) discusses some reasons why this type of password management is not secure and as the author claims: &#8220;has fatal flaws&#8221;.

There are four points discussed in the article:

  1. _DPMs cannot accommodate varying password policies without keeping state:_ I disagree with this one. As long as the hash function is complex enough (which normally is), the generated password can be accoreding to every sane password policy. The examples that author provides: &#8220;password must be at least 8 characters long&#8221; or &#8220;password must be lowercase only&#8221; either are by default satisfied or don&#8217;t make sense! I have never seen a website which insists password must be lowercase.
  2. _DPMs cannot handle recovation of exposed passwords without keeping state_: I agree with this one but see point below.
  3. _DPMs cannot store existing secrets_: Agreed but it can be fixed without loosing security. See the point below.
  4. _Exposed master password alone exposes all of your site passwords_: I disagree with this point because it is not an issue of DPMs per-se. If your email account&#8217;s password is exposed, it will probably have similar effect! That is how email works. This is the natural side-effect of using email.

### State vs Sensitive data

I would like to differentiate between state of a password manager with sensitive data (e.g. passwords). Of course for a traditional password manager like [1Password](https://1password.com/), these are the same: State of the password manager is the collection of stored password. So it is sensitive data by definition.

But for DPMs this is not the case. You can have some basic and minimal state which is not sensitive. What does that mean? It means that this state can be publicly exposed and available without loosing security of any of your accounts. The only important security feature of state in this sense is &#8220;Integrity&#8221;. They should not be altered without your consent. If this feature is satisfied then there is no problem for a DPM to have publicly available state.

For example you can store this state in your Google Drive or Dropbox or even on GitHub! Even if you want to maintain higher level of security you can encrypt those information using the &#8220;master password&#8221;, but that won&#8217;t be required.

Now, how can using state address the two problems stated above: Password revocation and storing existing secrets?

### How to store existing secrets in a DPM?

Suppose that you already have an account with password: **EP**. Now you want to switch to DPM. Can you keep this password? It is possible using State.

Suppose that DLM expects the password of this account to be **CP** where **CP** is result of the Hash function formula of the system. It is enough to store &#8220;`XP=CP^EP`&#8221; in the State storage of the system (^ represents XOR operation). Now, it is impossible to retain any information about &#8220;**EP**&#8221; with having only &#8220;**XP**&#8220;. But user can easily calculate &#8220;**EP**&#8221; by calculating &#8220;**CP**&#8221; and then: &#8220;`EP=CP^XP`&#8221; (You can use any other function with similar property).

Using this method, a user can store his existing password without revealing any sensitive information. Same approach can be used to renew a password. Just keep a public parameter (like counter), in the State data. It won&#8217;t expose anything sensitive and can be used to renew the password. Just increment the counter and the password will be:

<pre>Password = Hash(master_password, website, user_id, counter)</pre>

This can easily be incorporated to any DLM and provide features to re-new password or add pre-existing passwords.

Of course there will always be a trade-off . So for above mentioned advantages, there are some drawbacks too. Most notably, if your master password is exposed or stolen, all of your accounts will be at risk. This is the natural consequence of using &#8220;Master Password&#8221;.