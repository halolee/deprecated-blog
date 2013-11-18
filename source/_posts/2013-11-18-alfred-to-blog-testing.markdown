---
layout: post
title: "Alfred to Blog Testing"
date: 2013-11-18 21:11
comments: true
categories: [Alfred,blog,productivity]
---

To create a blog manually seems like a have a bit of tasks to accomplish.

1. First need to navigate to the Git repository

2. Second need to use rake new_post to create a new post

3. Then need to navigate to the source/_posts/ folder and locate the newly generated post

4. Then need to open it with either mou or other software to edit it.

If only to do it once is OK. But the above steps will become a daily routain for user who want to really to blog everyday.

Alfred comes to the rescure. 

With a powerpack activated Alfred, just goes to the Workflows and then create a new 

	Template -> Essential -> Keyword To Terminal Command

Then put the following to 

	cd Sites/repository/
	bundle exec rake new_post["{query}"]
	cd source/_posts/
	
And assign a short cut to it. Please note the **Sites/repository/** need to be changed to the location of the repository.

After that just click on the lasted created blog and start blogging.

Thought about to automatically open the blog through pip. Unfortunately I am quite new to the termianl programming, so if anywant to contribute, feel free to put a comment below.

After the editing, just put another Alfred shot cut to fast deploy

	cd Sites/repository/
	bundle exec rake generate
	bundle exec rake deploy
	
Thats pretty much it. 

So after today, the daily routin would be come

**Call Alfred -> Shot cut to create post -> open post -> Short cut to deploy**

Hope this post makes your life easier :D