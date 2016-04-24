---
layout: post
title: LeetCode Solution - First Bad Version
description: "有一段时间没有更新了，希望能够趁春节假期的最后两天，努力多刷几道 LeetCode，并且能够将 blog 的 UI 和主页统一起来。"
image: "leetcode"
tag: [LeetCode, Binary Search (二分查找)]
---
有一段时间没有更新了，希望能够趁春节假期的最后两天，努力多刷几道 LeetCode, 并且能够将 blog 的 UI 和主页 [ivanjiang.com][1] 统一起来。

果然是将算法荒废了很久，心里很没底地刷了一道 easy 的题目还是花了很长时间。第 278 题，First Bad Version 其实就是一道简单的二分查找的题目。

{% highlight javascript %}
/**  * Definition for isBadVersion()
 * 
 * @param {integer} version number
 * @return {boolean} whether the version is bad
 * isBadVersion = function(version) {
 * ...
 * };
 */
'use strict';
/**  * @param {function} isBadVersion()
 * @return {function}
 */
var solution = function(isBadVersion) {

/**  * @param {integer} n Total versions
	 * @return {integer} The first bad version
	 */
	return function(n) {

	var low = 1, high = n, mid = low;

	while (low \<= high) {
	mid = (low + high) % 2 === 0 ? (low + high) / 2 : (low + high - 1) / 2;

	if (!isBadVersion(mid)) {
	low = mid + 1;
	++mid;
	} else {
	high = mid - 1;
	}
	}

	return mid;

	};
};
{% endhighlight %}

[1]:	http://ivanjiang.com "ivanjiang.com"