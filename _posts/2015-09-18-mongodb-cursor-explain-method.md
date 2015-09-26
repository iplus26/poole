---
layout: post
title: MongoDB cursor.explain() 方法 - 学习笔记
tags: [MongoDB, database]
---

# cursor.explain()

在 MongoDB 中使用 `find()` 方法返回的是一个 cursor 类型。cursor 有一个方法是 `cursor.explain()`，用途是提供 `find()` 的 query plan 的信息。在 3.0 版本中，这个方法的参数和输出格式已经发生了改变，比如我正在读的这本《七周七数据库》（2013年7月版）里面返回的结果就和最新版本的 MongoDB 返回的很不一样。

## Verbosity Modes

这个方法的作用取决于它的参数 verbosity mode。这是一个可选参数，它决定了 `explain()` 方法的动作 (behavior) 和返回信息的数目，可能的模式有 *queryPlanner* (default), *executionStats*, *allPlansExecution*. 

### queryPlanner Mode

默认情况下，`explain()` 方法在这个模式下执行。MongoDB 使用 *query optimizer* 来选择操作的 *winning plan*，`explain()` 方法返回 *queryPlanner* 的信息，参考下面的一个例子：

    "queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "myproject.phones",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"display" : {
				"$eq" : "+2 021-556000"
			}
		},
		"winningPlan" : {
			"stage" : "COLLSCAN",
			"filter" : {
				"display" : {
					"$eq" : "+2 021-556000"
				}
			},
			"direction" : "forward"
		},
		"rejectedPlans" : [ ]
	},

## executionStats Mode

在这个模式下，`explain()` 方法会返回 *queryPlanner* 和 *executionStats* 信息。为什么我觉得这个模式才是缺省的模式呢？

你可以在 *executionStats* 里得到关于这次查询的数据，比如 *executionStats.executionTimeMillis* 是执行时间。

## allPlansExecution Mode

在这个模式下，MongoDB 返回 winning plan 和其他备选方案的数据。`explain()` 方法会返回 *queryPlanner* 和 *executionStats* 信息。

# db.collection.explain().find()

这个方法和 `db.collection.find().explain()` 十分相似，区别在于：


>  The `db.collection.explain().find()` construct allows for the additional chaining of query modifiers. For list of query modifiers, see `db.collection.explain().find().help()`.

> The `db.collection.explain().find()` returns a cursor, which requires a call to `.next()`, or its alias `.finish()`, to return the `explain()` results.

翻译过来就是，

`.explain().find()` 结构允许另外的查询链；

`.explain().find()` 返回一个 cursor，要求调用 `.next()` 或者 `.finish()` 以返回 `.explain()` 结果。然而我也不知道这句话是什么意思。

更详细的内容可以参阅官网的文档，[cursor.explain() - MongoDB Manual 3.0](http://docs.mongodb.org/manual/reference/method/cursor.explain/)。