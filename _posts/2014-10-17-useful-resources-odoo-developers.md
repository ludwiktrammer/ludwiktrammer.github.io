---
layout: post
title: Useful resources for Odoo developers
categories: [odoo]
---
Since I started developing in Odoo about 1.5 month ago, I struggle to find good sources of information on Odoo 8 development. Below I present you a list of resources that were the most helpful to me so far. Also, if you have any additional suggestions, I'd love if you'd share them with me!

Please note that this post was published in October, 2014. I think the situation will become better over time.

1. [The official Odoo 8 documentation][doc8]. It's still in development - there are whole huge topics missing and the documentation is not even linked anywhere on the official website. The good news is, it is constantly being updated, so it'll probably become much better over time.

2. Official documentation for older Odoo (OpenERP) versions. [Documentation for version 7 is much more complete][doc7] (although sometimes obsolete). Unfortunately from time to time you need to go back even further [to OpenERP 6 documentation][doc6], since it covers some topics that are missing from both v7 and v8 docs. And of course there are topics that no official documentation covers at all.

3. Videos from Odoo Days 2014 conference. There are three videos, containing 26 presentations: [Day 1 video][day1], [Day 2 video][day2], [Day 3 video][day3]. They often contain information that can't be found anywhere else. Here are some presentations that I think are be most useful for new developers:
    * [Developing frontend (website) modules in Odoo][vid_front]
    * [Developing backend (Odoo proper) modules (using the new ORM API)][vid_back]
    * [Good overview of new programming features in Odoo 8][vid_new]
    * [Creating automated tests / interactive tutorials in the frontend][vid_front_tut]
    * [Creating (pdf) reports][vid_report]

4. ["Odoo new API guideline’s"][orm_guideline] - it's a document providing much more detailed description of the new ORM API, than the one that can be found in the official docs.

5. Reading the code. That is currently my main source of information. The [Sublime Text editor][sublime] has a wonderful search feature, that lets you search the whole project tree using regular expressions. I use this constantly to find examples or find and analyze original classes and methods.

6. Google and StackOverflow, usually programmer's best friends, in this case are not really useful. Still, you can try. Unfortunately more often than not you will be disappointed. That's one of the reasons I started this blog - to provide Google with some actual answers.

[doc8]: https://www.odoo.com/documentation/8.0/
[doc7]: https://doc.odoo.com/trunk/server/
[doc6]: https://doc.odoo.com/6.1/developer/
[day1]: https://www.youtube.com/watch?v=0GUxV85DDm4
[day2]: https://www.youtube.com/watch?v=ij14T3asngo
[day3]: https://www.youtube.com/watch?v=u-VcUpeM6xY
[vid_new]: http://youtu.be/0GUxV85DDm4?t=1h58m20s
[vid_front]: http://youtu.be/0GUxV85DDm4?t=3h17m20s
[siblime]: http://www.sublimetext.com/
[vid_back]: http://youtu.be/0GUxV85DDm4?t=5h47m45s
[vid_front_tut]: http://youtu.be/0GUxV85DDm4?t=6h49m25s
[vid_report]: http://youtu.be/ij14T3asngo?t=8h27m20s
[orm_guideline]: http://odoo-new-api-guide-line.readthedocs.org/
[sublime]: http://www.sublimetext.com/
