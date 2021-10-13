---
title: "How to use hybrid properties with regular expressions in SQLAlchemy"
excerpt: "SQLAlchemy has these things called hybrid properties, which let you create class attributes that can be accessed from Python but also from SQL. They can be tricky to combine with other logic like regular expressions."
date: 2021-03-09 00:00:00
published: true
tags: python sqlalchemy programming
---

SQLAlchemy has these things called [hybrid properties](https://docs.sqlalchemy.org/en/13/orm/extensions/hybrid.html), which let you create class attributes that can be accessed from Python but also from SQL. They can be tricky to understand.

If you want to use them with a regular expression, life can get a lot harder; there are a couple of pitfalls you need to avoid.

This is a quick info post to demonstrate that combination. It uses SQLAlchemy 1.3 and MySQL 5.7.

# Here's our problem

The queries we want to run will provide boolean attributes called `is_alert` and `is_update`, which look like this:

{% highlight sql %}
SELECT title,
title REGEXP '- (?:UPDATED? )?ALERT' AS is_alert
FROM nodes;
{% endhighlight %}

{% highlight sql %}
SELECT title,
title REGEXP '[[.full-stop.]][[:digit:]]{4}[[.full-stop.]][[:digit:]]+$' AS is_update
FROM nodes;
{% endhighlight %}

It can sometimes be difficult for beginners (like me!) to translate SQL into SQLAlchemy, so the rest of the post will explain how.


# Just give me the example!

Here's how to create two hybrid attributes, `is_alert` and `is_update`, which use regular expressions, with a MySQL back-end.

```python
from sqlalchemy import Column, Text
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()

class Bulletin(Base):
    __tablename__ = 'nodes'
    title = Column(Text, name='title')

    """ The pattern for is_alert is the same in both Python and MySQL, so could be a class constant """

    @hybrid_property
    def is_alert(self) -> bool:
        return bool(re.search(r'- (?:UPDATED? )?ALERT', self.title))

    @is_alert.expression
    def is_alert(self) -> bool:
        return self.title.op('regexp')(r'- (?:UPDATED? )?ALERT')

    """ The patterns for is_update are different between Python and MySQL as they use different regex dialects """

    @hybrid_property
    def is_update(self) -> bool:
        return bool(re.search(r'\.\d{4}\.\d+$', self.node_reference))

    @is_update.expression
    def is_update(self) -> bool:
        return self.node_reference.op('regexp')('[[.full-stop.]][[:digit:]]{4}[[.full-stop.]][[:digit:]]+$')
        # for more information on this regex dialect, see https://dev.mysql.com/doc/refman/5.7/en/regexp.html
```

# What are the tricky parts?

## The 'REGEXP' or 'RLIKE' operator

[MySQL uses 'REGEXP' or 'RLIKE'](https://dev.mysql.com/doc/refman/5.7/en/regexp.html), as in `title REGEXP 'some pattern'`. If you're using another back-end, you might need a different operator.

Remember, the queries we want to run will look like this:

{% highlight sql %}
SELECT title,
title REGEXP 'some pattern' AS attribute_name
FROM nodes;
{% endhighlight %}

## 'op'

SQLAlchemy 1.3 doesn't have a built-in operator for regular expressions, so we have to use `.op` to access it. You don't need to import anything - it's available straight from the Column. It takes a string, which is the name of the SQL operator you want.

So to get access to that from Python, we use `title.op('regexp')('some pattern')`. We could use `title.op('rlike')('some pattern')`, since MySQL considers it a different name for the same thing.

### 'op' is not 'func'

There's nothing wrong with using SQL functions in general, but I'm highlighting this to make you aware:

**SQL functions and SQL operators are different things**. Write down the SQL you want to run and make sure you know which you want.

It might sound obvious, but I wish someone had highlighted that while I was trying to solve this - mixing them up will do you no favours. 😄

We are **not** doing this right now, because it's a different approach which (in MySQL) requires the `EXECUTE` permission:

```python
from sqlalchemy import func

class Bulletin:
    ...

    @is_alert.expression
    def is_alert(self) -> bool:
        regexp_func = func('regexp')
        return regexp_func(self.title, 'some pattern')

```

This code will give you a SQL _function_, not a SQL _operator_. The difference is this:

| name | are we using it here? | example |
|-|-----------------------|---------|
| operator | yes | `title REGEXP pattern` |
| function | no  | `regexp_match(title, pattern)` |

The `regexp_match` function provided by MySQL will do the job, but it's a function, and this example is using operators. So we're not using that function.


## @hybrid_property and expression

A hybrid property, in short, is a property on the class which you can use in both of these ways:

```python
# As a Python property (example A):
my_bulletin = db_session.query(Bulletin).first()
if my_bulletin.is_update:
    print("Yep, it's an update")

# As an attribute in SQL (example B):
updates = db_session.query(Bulletin).filter(Bulletin.is_update == 1)
from sqlalchemy import not
not_updates = db_session.query(Bulletin).filter(not_(Bulletin.is_update))
```

The first examples in [SQLAlchemy's documentation](https://docs.sqlalchemy.org/en/13/orm/extensions/hybrid.html) use some convenient examples where you can write some Python code which will also be translated implicitly to SQL. That's cool because it's good to know that can work. However, it's often not the case with regexes, and here's where I stumbled. For this task you need to specify two versions - one native Python (example A) and one in SQLAlchemy's own Python-that-means-SQL (example B).

To know _how_ these are declared separately, see the top example again. But I hope you now understand _why_.

There are also hybrid methods, which we aren't going into here.


# Wrapping up

You hopefully understand, or at the very least have working example code for, querying on regular expressions in SQLAlchemy.

Cheers!
