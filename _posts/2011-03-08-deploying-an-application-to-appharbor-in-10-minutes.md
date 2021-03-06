---
layout: post
title: Deploying an Application to AppHarbor in 10 Minutes
date: 2011-03-08 00:00
comments: true
categories: []
---
<p>A few weekends ago, I had an idea for a new project: as a <a href="http://www.netflix.com/" target="_blank">Netflix</a> user, I constantly forget to return my movies or forget to watch them in the first place. So I thought it would be cool to create a small web application that would email me in the event that my Netflix movie has been "at home" for a long time. I started digging through the <a href="http://developer.netflix.com/" target="_blank">Netflix API</a> and found out that there was, in fact, a <a href="http://developer.netflix.com/docs/REST_API_Reference#0_41310" target="_blank">service call</a> that returned a list of movies that I have at home and how long they've been there. With that, I began development on Returnflix.</p>

<p>The next thing I needed was a hosting provider. I told myself that I wanted to get back into the ASP.NET game, which is great, because I forgot how much I love .NET. The only downside is that hosting for ASP.NET applications is a little bit harder to come by. I remember hearing about <a href="http://appharbor.com/" target="_blank">AppHarbor</a>, a cloud computing service for ASP.NET that touts "Azure done right" as it's slogan, and I had been meaning to give it a thorough trial, so this project was perfect.</p>

<p>I wrote this application in C# on ASP.NET. It's an extremely simple workflow: user visits the website, enters their email address where notifications will be sent to (since Netflix does not provide the user's email from their API), and then they authenticate using their Netflix account over <a href="http://oauth.net/" target="_blank">OAuth</a>. When they are returned to my website, I stored their OAuth credentials in my database and that's it!</p>

<p>The fun stuff takes place on the back-end. AppHarbor is still a <a href="http://blog.appharbor.com/2010/11/13/welcome-aboard" target="_blank">young service</a>, and while they have established an initial <a href="http://appharbor.com/page/pricing" target="_blank">pricing model</a>, some of the services they plan to offer are <a href="http://blog.appharbor.com/2011/1/27/preliminary-pricing-page" target="_blank">not yet available</a>. One of those services happens to be <a href="http://blog.appharbor.com/2011/1/20/handling-background-tasks" target="_blank">background workers</a>, something that Returnflix needs. So I needed another way of accomplishing this. I did some research and stumbled onto a <a href="http://blog.stackoverflow.com/2008/07/easy-background-tasks-in-aspnet/" target="_blank">great article</a> by <a href="http://www.codinghorror.com/blog/" target="_blank">Jeff Atwood</a> on the <a href="http://stackoverflow.com/" target="_blank">Stack Overflow</a> blog which talks about why I needed these background workers and details a "hack" of sorts on how to implement this using nothing but ASP.NET. It basically uses an event in the <a href="http://msdn.microsoft.com/en-us/library/ms178473.aspx" target="_blank">ASP.NET application life-cycle</a> for cache expiration, <code>CacheItemRemoved</code>, and instead of handling this event the way it was <a href="http://weblogs.asp.net/kwarren/archive/2004/05/20/136129.aspx" target="_blank">meant to be handled</a>, I execute my notification methods instead. Every hour, I get a list of 5% of my users, check with Netflix to see if they have had a movie for more than 7 days, and if they have, I send them an email notification, then I store a time-stamp of their last execution, for performance reasons.</p>

<p>So after a couple of hours of development and testing, my application was ready to go. I setup my account at AppHarbor and created a new application. Then I added a 20mb SQL Server <a href="http://blog.appharbor.com/2010/12/06/now-with-sql-server" target="_blank">database</a>, which I then synchronized with my local SQL Server instance, and added my <code>returnflix.com</code> hostname. I was then given a URL to a Git repository, which is how AppHarbor allows for continuous integration deployments and is pure genius. Being able to deploy an application simply by pushing to a Git repository is an innovation that I'm sure will be adopted by several companies in the future, but AppHarbor has got this down. Essentially, two lines of code got me up and running*:</p>

<script src="https://gist.github.com/860286.js"> </script>


<p></p>

<p>If you're a Windows user running <a href="http://code.google.com/p/msysgit/" target="_blank">msysGit</a>, like myself, the code above may not have worked for you (hence the * from above). You probably received an error message along the lines of:</p>

<script src="https://gist.github.com/860293.js"> </script>


<p></p>

<p>A quick Google search lead me to this <a href="http://www.lostechies.com/blogs/dahlbyk/archive/2010/09/26/msysgit-error-setting-certificate-verify-locations.aspx" target="_blank">blog post</a>, which discussed why this was happening and gave the following fix:</p>

<script src="https://gist.github.com/860295.js"> </script>


<p></p>

<p>With that, I was able to run my <code>git push</code> and my application deployed to AppHarbor successfully! From the time my application was developed to the time I deployed it on AppHarbor was 10 minutes flat. AppHarbor completely eliminates what used to be a tedious process of finding a host, configuring it, updating your builds, etc. and just works. They certainly live up to their "Azure done right" tagline with a service that delivers, perfectly. You can find out more about AppHarbor <a href="http://www.appharbor.com" target="_blank">here</a> and you can check out this project on <a href="http://github.com/mbmccormick/returnflix" target="_blank">GitHub</a>.</p>
