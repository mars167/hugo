---
title: "Rails Active Record总结"
date: 2019-09-22T16:14:57+08:00
lastmod: 2019-09-22T16:14:57+08:00
draft: false
keywords: []
description: ""
tags: [“ruby”,”ruby on rails”,”active record"]
categories: [”ruby on rails”]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
---

> 总结自[Ruby on Rails 指南](https://ruby-china.github.io/rails-guides/index.html)

<!--more-->

### Rails 6种关联类型

- `belongs_to`
    - 声明所在的模型实例属于另一个模型的实例
    - 外键在 `belongs_to` 关联所在模型对应的表中，即XXX_id所在的模型
    - 在 `has_one` 和 `belongs_to` 关联中，必须使用 `build_*` 方法构建关联对象。`association.build` 方法是在 `has_many` 和 `has_and_belongs_to_many` 关联中使用的。创建关联对象要使用 `create_*` 方法。
    - 声明 `belongs_to` 关联后，所在的类自动获得了五个和关联相关的方法：
        - `association`
        - `association=(associate)`
        - `build_association(attributes = {})`返回对象不存入数据库
        - `create_association(attributes = {})`返回对象并存入数据库
        - `create_association!(attributes = {})`不通过valid时抛出异常
    - `belongs_to` 关联支持下列选项：
        - `:autosave`
        - `:class_name`
        - `:counter_cache`
        - `:dependent`
        - `:foreign_key`
        - `:primary_key`
        - `:inverse_of`
        - `:polymorphic`
        - `:touch`
        - `:validate`
        - `:optional`
    - 在作用域代码块中可以使用任何一个标准的[查询方法](https://ruby-china.github.io/rails-guides/active_record_querying.html)。下面分别介绍这几个：
        - `where`
        - `includes`
        - `readonly`
        - `select`
- `has_one`
    - 这种关联表示模型的实例包含或拥有另一个模型的实例
    - 声明 `has_one` 关联后，声明所在的类自动获得了五个关联相关的方法：
        - `association`
        - `association=(associate)`
        - `build_association(attributes = {})`
        - `create_association(attributes = {})`
        - `create_association!(attributes = {})`
    - `has_one` 关联支持下列选项：
        - `:as`
        - `:autosave`
        - `:class_name`
        - `:dependent`
        - `:foreign_key`
        - `:inverse_of`
        - `:primary_key`
        - `:source`
        - `:source_type`
        - `:through`
        - `:validate`
    - 在作用域代码块中可以使用任何一个标准的[查询方法](https://ruby-china.github.io/rails-guides/active_record_querying.html)。下面介绍其中几个：
        - `where`
        - `includes`
        - `readonly`
        - `select`
- `has_many`
    - `has_many` 关联建立两个模型之间的一对多关系。在 `belongs_to` 关联的另一端经常会使用这个关联
    - 声明 `has_many` 关联后，声明所在的类自动获得了 16 个关联相关的方法：
        - `collection`
        - `collection<<(object, &#8230;&#8203;)`
        - `collection.delete(object, &#8230;&#8203;)`
        - `collection.destroy(object, &#8230;&#8203;)`
        - `collection=(objects)`
        - `collection_singular_ids`
        - `collection_singular_ids=(ids)`
        - `collection.clear`
        - `collection.empty?`
        - `collection.size`
        - `collection.find(&#8230;&#8203;)`
        - `collection.where(&#8230;&#8203;)`
        - `collection.exists?(&#8230;&#8203;)`
        - `collection.build(attributes = {}, &#8230;&#8203;)`
        - `collection.create(attributes = {})`
        - `collection.create!(attributes = {})`
    - `has_many` 关联支持以下选项：
        - `:as`
        - `:autosave`
        - `:class_name`
        - `:counter_cache`
        - `:dependent`
        - `:foreign_key`
        - `:inverse_of`
        - `:primary_key`
        - `:source`
        - `:source_type`
        - `:through`
        - `:validate`
    - 
- `has_many :through`
    - 建立两个模型之间的多对多关联。这种关联表示一个模型的实例可以借由第三个模型（关系表），拥有零个和多个另一模型的实例。
    - 有一个关系表和相对应的model
    - 如果想把关联模型当做独立实体使用，要用 `has_many :through` 关联
- `has_one :through`
    - 通过第三个模型，建立其他两个模型的一对一关系
- `has_and_belongs_to_many`
    - 不借由第三个模型建立的多对多
    - 仍然有第三个关系表但这个表没有model
    - 如果不需要使用关联模型，建立 `has_and_belongs_to_many` 关联更简单（不过要记得在数据库中创建联结表）

#### 自联结如何创建

```ruby
`class` `Employee < ApplicationRecord``  ``has_many ``:subordinates``, class_name: ``"Employee"``,``                          ``foreign_key: ``"manager_id"` `  ``belongs_to ``:manager``, class_name: ``"Employee"``end`
```

#### 创建联结表

使用`create_join_table`创建联结表

```ruby
`class` `CreateAssembliesPartsJoinTable < ActiveRecord::Migration[``5``.``0``]``  ``def` `change``    ``create_join_table ``:assemblies``, ``:parts` `do` `|t|``      ``t.index ``:assembly_id``      ``t.index ``:part_id``    ``end``  ``end``end`
```

#### 双向关联的问题

Active Record 能自动识别多数具有标准名称的双向关联。然而，具有下述选项的关联无法识别：

- `:conditions`
- `:through`
- `:polymorphic`
- `:class_name`
- `:foreign_key`

Active Record 提供了 `:inverse_of` 选项，可以通过它明确声明双向关联

```ruby
`class` `Author < ApplicationRecord``  ``has_many ``:books``, inverse_of: ``'writer'``end` `class` `Book < ApplicationRecord``  ``belongs_to ``:writer``, class_name: ``'Author'``, foreign_key: ``'author_id'``end`
```

`inverse_of` 有些限制：

- 不支持 `:through` 关联；
- 不支持 `:polymorphic` 关联；
- 不支持 `:as` 选项；

