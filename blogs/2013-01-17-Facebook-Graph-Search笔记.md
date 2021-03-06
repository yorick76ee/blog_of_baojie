Facebook Graph Search笔记
---
    
> Categories: Web, 自然语言处理, 语义网  
> Time: 2013-01-17  
> Original url: <http://baojie.org/blog/2013/01/17/facebook-graph-search/>
    
### 摘抄了一些新闻。尽可能过滤buzz, hype和口水

2013-02-16: 根据Reddit访谈增补了内容: Unicorn, Query Syntax, Question Understanding (NLP)

### Source

Facebook’s Bold, Compelling and Scary Engine of Discovery: The Inside Story of Graph Search

- 2013-01-15
- <http://www.wired.com/business/2013/01/the-inside-story-of-graph-search-facebooks-weapon-to-challenge-google/all/>

中文翻译：Facebook社交搜索Graph Search的幕后故事

- 2013-01-16
- <http://tech.sina.com.cn/i/csj/2013-01-16/16357984233.shtml>

Under the Hood: Building Graph Search Beta

- by Lars Eilstrup Rasmussen on Tuesday, January 15, 2013
- <https://www.facebook.com/notes/facebook-engineering/under-the-hood-building-graph-search-beta/10151240856103920>

Facebook Graph Search: <https://www.facebook.com/about/graphsearch>

A really tiny explanation of how Facebook’s Graph Search works 

- <http://gigaom.com/2013/01/15/a-really-tiny-explanation-of-how-facebooks-graph-search-works/>
- Om Malik, 2013-01-15

### What

成为一款发现工具

利用庞大的**结构化数据库**开发一个完全不同的搜索引擎，从而带来巨大价值

扎克伯格表示：“我最喜爱的搜索关键词与招聘有关。我们可以尝试查找Facebook工程师在谷歌的工程师朋友。

斯托基随后尝试了一个查询请求——“住在我附近的单身女性。”

随后，他们又展示了推荐功能.如果你来到任何一个城市，都可以向好友或好友的好友征求美食建议。还可以向那些自称美食达人或专业厨师的人寻求建议。你甚至可以随意搜索各种有趣的信息，例如：喜欢米特•罗姆尼(Mit Romney)的人都喜欢看哪些书。

If it fails, it might refer people to Bing

### History

在发布之初，Graph Search仅面向一小部分用户开放。扎克伯格认为，到面向全球上亿用户全面开放时，Graph Search将得到极大的改进

Graph Search的开发从2011年春季开始

Toward the end of summer 2012, Graph Search was starting to take shape.

Released on 2013-01-15

### Who

<http://www.linkedin.com/pub/lars-rasmussen/5/519/16>

Lars Rasmussen。以前在Google

- 成功的项目是谷歌地图
- 失败的项目是Google Wave (2500万美元和60名工程师)

<http://www.linkedin.com/in/tomstocky>

2011年夏季，拉斯姆森的项目有了一名合作领导者汤姆•斯托吉(Tom Stocky)

这一项目共有50名工程师，其中包括两名语言学家，负责让搜索引擎理解人类语言。

照片里一共73个人。印度人很多但是据说核心团队不大，不超过10个人

### How

2011年夏季 扎克伯格当时的反应是：“不可能。你可以输入任何希望的关键词，而标题中含有这一关键词的页面将弹出。无人能使自然语言以这种方式工作，或编目所有这些内容。在Facebook上有超过1万亿个用户关系。编目所有这些关系并使其提供服务将是一个巨大的技术挑战。”

为了形成足够复杂的搜索关键词，Facebook会猜测用户试图搜索的信息

Challenge number one: scale. More than 1 billion people use Facebook each month, they have shared more than 240 billion photos on the site, and formed more that 1 trillion connections of thousands of different types

Secondly, we owned too much code running too many services already.

But we needed the system also to find answers more than a single connection away, such as “restaurants liked by my friends from India.” Here we were in luck: one of our three existing systems, Unicorn, was designed exactly with this in mind.

two-stage approach:

- first build out Unicorn to manage all the existing search experiences on the site
- and then, build out Unicorn further to meet all the requirements of Graph Search

At a very rudimentary level, Rasmussen in a call later explained that when we type a query, say, “Restaurants liked by my friends from India in San Francisco,”

- an aggregator tool using natural language processing takes the query and parses it into keywords
- against which searches are conducted. So “Restaurants,” “San Francisco,” and “Friends from India” become queries
- whose results are fetched and further sorted to give us the final answer.A big infrastructure challenge. Want to keep the latency to below two seconds

### Unicorn     

Unicorn是什么？貌似网上没有公开资料

Quora上有人在问：What exactly is Facebook Unicorn?  目前还没有答案

2013-01-19 Aditya Tayade and Tudor Bosman在Quora上回答

> Aditya: With S-expressions you can traverse the graph to multiple depths in one shot. For example you can query for something like — a list of all the fans of all the pages that your FoFs have liked, and so on.
> 
> Tudor: I wrote a large fraction of the Unicorn code.  Unicorn processes queries that select a section of the social graph.  Unicorn is essentially built like a standard search engine that maps terms to sets of documents; unlike a text search engine, the terms aren’t words, but connections in the social graph (for example, “friend:4″ maps to the ids of all users who are friends with the user with id 4; we have similar terms for “photos taken by user id 4″, “users who like page with id 25″ etc).

2013-02-15 Mike Curtiss from Facebook’s search infrastructure said on Reddit

> We have an inverted-index system called Unicorn that we use to select nodes in the social graph based on edge-relationships to other nodes. Unicorn is akin to document search systems for other contexts like email, webpages, or files on your computer; but it also has some graph-centric features that make it especially useful for searching over the social graph. For example, we don’t index the lists of friends-of-friends for every user (friends only), but our system makes it possible to retrieve friends-of-friends by executing a single query.
> 
> Just like the rest of Facebook, we try to use (and contribute to) open source technology when possible. So we use Hadoop in our pipeline for building search indices.

Writes Soren Lassen, who heads up he search infrastructure team:

> We use a combination of Hive, Hadoop, and HBase to convert database contents into an inverted “base” index on a regular basis, and then we send messages to the index servers with realtime updates to the base index.

### Query Syntax

The are 3 level of query languages in Graph Search.

- The natural language that users type into the search field,
- a semantic language that describes our interpretation of the natural language,
- and then further down in the stack we use an **s-expression** syntax to retrive potential results from an **inverted index**.

For example, if you look for

My friends who live in Sydney, Australia

the corresponding semantic is

intersect(friends(me), residents(110884905606108)),

where 110884905606108 of course is a unique id for Sydney, Australia. You can see the expression in the result page URLs, encoded in a reverse-polish style:

<https://www.facebook.com/search/110884905606108/residents/me/friends/intersect>

The corresponding s-expression looks roughly like this:

(and friend:767560056 city_to_user:110884905606108)

Where 767560056 is my unique id. In practice, we often re-write the s-expression several times to a sometimes much more complex one that make retrieval more efficient.

### Question Understanding

see  [LarsRasmussen](https://www.reddit.com/user/LarsRasmussen) on [Reddit 2013-02-16](https://www.reddit.com/r/IAmA/comments/18jb6d/i_am_the_pointyhaired_engineering_director_for/c8fdjgq)

- At the core of the system sits a Context-Free Grammar
- a Parser attempts to find the queries from the grammar that most closely matches
- Part of the parsing involves searching for people and entities
- When the user clicks one of the suggested queries, we proceed to resolve the corresponding semantic.here are three steps to this part: 1) we retrive candidate answers from an inverted index (<http://en.wikipedia.org/wiki/Inverted_index>), then we 2) filter out anything the searcher does not have access to, and finally we 3) order them according to a great many criteria in the way we think is most interesting to the searcher.
- Lastly, we display the results.

### Graph Database?

They don’t use, e.g., Neo4j or Titan [permalink](https://www.reddit.com/r/IAmA/comments/18jb6d/i_am_the_pointyhaired_engineering_director_for/c8faa75)

### Market Impact

Yelp股票应声跌了8%

对于在线招聘网站Monster和职业社交网络LinkedIn来说，这都不是一个好消息。

除了屏幕左边展示的结果外，右边还提供了很多选择，帮助我进一步提炼或调整搜索请求。图谱搜索团队称之为“能量棒”(Power Bar)。譬如，如果你要查找尚未在Facebook上建立联系的大学同窗，便可进一步搜索与你同年毕业于同一专业的校友。

- 根据目的不同，你或许还可以将搜索结果限制为单身人士。
- Facebook已经为广告主提供了这种“细致定位”功能

Graph Search是Google Search杀手吗？看来不是

- <http://www.quora.com/Facebook-Graph-Search/Is-Facebook-Graph-Search-a-Google-Search-killer>

### Privacy

他强调说，图谱搜索尊重用户施加的所有限制。“如果用户对信息施加了限制，就不会显示在搜索结果中。

Graph Search can find only content that someone has shared with you and that you could already see on the site.   [permalink](www.reddit.com/r/IAmA/comments/18jb6d/i_am_the_pointyhaired_engineering_director_for/c8fd04l) 2013-02-16

### Future

图谱搜索目前不会索引Facebook中最重要的功能：帖子和状态更新。要整合这类内容，需要复杂的技术，还会耗费巨大的资源。但Facebook已经开始攻克这一难关。

他们的另一项重要计划是融合第三方应用产生的海量数据。例如，使用Spotify了解你的扩展队列中有谁特别喜欢听劳拉•奈罗(Laura Nyro)的歌。或者使用健身应用寻找与你路线相同、速度相当的慢跑搭档。

目前的图谱搜索还缺乏另外一个重要元素：广告。但它可能不会缺席太久，

Facebook还将在移动应用中整合搜索功能

### 质疑

[Graph Search’s Dirty Promise and the Con of the Facebook “Like”](stevecheney.posterous.com/graph-searchs-false-promise-and-the-con-of-th) by [steve cheney](posterous.com/users/3sy8mS0xCulP) 2013-01-16

- data is dated  
- You simply can’t role up recommendations for people, places, and interests into a service that’s one size fits all.
- Graph Search is no more a slam dunk for local / interest intent harvesting than me yelling across an auditorium for which doctor I should see for my cough.


[@张俊林say](www.weibo.com/malefactor)

- 为什么说社交搜索价值被高估？首先，社交搜索肯定是有独特价值的，主要有两点：一个是对信息的可信性通过社交关系进行了过滤；第二个是可以通过社交网络起到发现引擎的作用。但是缺点也是很明显的。
- 社交搜索对应的缺点是：首先搜索是个主动行为，社交搜索能够发挥作用的query占用户信息需求比例应该非常低；另外一点，如果社交网络不够大的话，仍旧无法获取足够多的信息，     
    