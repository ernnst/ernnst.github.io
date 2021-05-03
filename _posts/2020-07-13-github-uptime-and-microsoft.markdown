---
layout: post
title: "GitHub Uptime and Microsoft"
date: 2020-07-13
tags: github git
comments: true
twitter: true
---

Recently GitHub reliability has been getting worse. It's 2020 July 13, Monday and GitHub wasn't working for around 2 hours. Take a look at their [status page](https://www.githubstatus.com). You'll see something like the screenshot below, taken at 2020-07-13 07:33 UTC.

![GitHub Status on 2020-07-13](/assets/images/2020/github-2020-07-status.png)

Millions of individuals and businesses rely on GitHub daily. Having that in mind, I am surprised how GitHub allows such a generous error budget for themselves. For example in April 2020 it went down to **99.67%**.

After exploring their [historic status page data](https://www.githubstatus.com/uptime) I noticed one interesting pattern. Since 2014, GitHub had their first outage in November 2018, only a month after Microsoft [announced](https://blogs.microsoft.com/blog/2018/10/26/microsoft-completes-github-acquisition) their acquisition was complete. More often and serious incidents started in 2019 December. Whether it's coincidence or consequence, you decide.

Take a look at historic uptime for Git Operations.

### GitHub 3 Years Before Microsoft
##### 2014 November - 2015 January
![2014 November - 2015 January](/assets/images/2020/github-2014-11.jpg)
##### 2015 February - 2015 April
![2015 February - 2015 April](/assets/images/2020/github-2015-02.jpg)
##### 2015 May - 2015 July
![2015 May - 2015 July](/assets/images/2020/github-2015-05.jpg)
##### 2015 August - 2015 October
![2015 August - 2015 October](/assets/images/2020/github-2015-08.jpg)
##### 2015 November - 2016 January
![2015 November - 2016 January](/assets/images/2020/github-2015-11.jpg)
##### 2016 February - 2016 April
![2016 February - 2016 April](/assets/images/2020/github-2016-02.jpg)
##### 2016 May - 2016 July
![2016 May - 2016 July](/assets/images/2020/github-2016-05.jpg)
##### 2016 August - 2016 October
![2016 August - 2016 October](/assets/images/2020/github-2016-08.jpg)
##### 2016 November - 2017 January
![2016 November - 2017 January](/assets/images/2020/github-2016-11.jpg)
##### 2017 February - 2017 April
![2017 February - 2017 April](/assets/images/2020/github-2017-02.jpg)
##### 2017 May - 2017 July
![2017 May - 2017 July](/assets/images/2020/github-2017-05.jpg)
##### 2017 August - 2017 October
![2017 August - 2017 October](/assets/images/2020/github-2017-08.jpg)
##### 2017 November - 2018 January
![2017 November - 2018 January](/assets/images/2020/github-2017-11.jpg)
##### 2018 February - 2018 April
![2018 February - 2018 April](/assets/images/2020/github-2018-02.jpg)
##### 2018 May - 2018 July
![2018 May - 2018 July](/assets/images/2020/github-2018-05.jpg)
##### 2018 August - 2018 October
![2018 August - 2018 October](/assets/images/2020/github-2018-08.jpg)

### GitHub Under Microsoft (Acquired in 2018 October)
##### 2018 November - 2019 January
![2018 November - 2019 January](/assets/images/2020/github-2018-11.jpg)
##### 2019 February - 2019 April
![2019 February - 2019 April](/assets/images/2020/github-2019-02.jpg)
##### 2019 May - 2019 July
![2019 May - 2019 July](/assets/images/2020/github-2019-05.jpg)
##### 2019 August - 2019 October
![2019 August - 2019 October](/assets/images/2020/github-2019-08.jpg)
##### 2019 November - 2020 January
![2019 November - 2020 January](/assets/images/2020/github-2019-11.jpg)
##### 2020 February - 2020 April
![2020 February - 2020 April](/assets/images/2020/github-2020-02.jpg)
##### 2020 May - 2020 July
![2020 May - 2020 July](/assets/images/2020/github-2020-05.jpg)

It's not only Git Operations, but other services too. Explore their [uptime page](https://www.githubstatus.com/uptime) and you'll see similar pattern. API Requests, Web-hooks, Issues, Pull Requests, Projects were having downtimes even more often than Git Operations.

Let's hope it's temporary and GitHub error 500 won't become their own version of Blue Screen of Death. In this case it would be Green Screen of Death (GSoD or GhSoD).

![GitHub - Green Screen of Death](/assets/images/2020/github-2020-07-gsod.png)
