---
layout: post
title: "JS占位模板"
description: JS占位模板
modified: 2016-06-01
category: JavaScript
tags: [JavaScript]
featured: true
---

用数据替换占位模板

	<body>
		<div id='result'>
			<p><span>邮箱: </span> {{email}}</p>
		</div>
		<button id="update">更新</button>
		<script type="text/javascript" src="./zepto.min.js"></script>
		<script>
		//创建占位的模板
			function createTpl(t) {
				return function (m) {
					return t.replace(/\{\{([^{}]+)\}\}/gm, function (t, e) {
							return m && m[e] || "";
					});
				};
			}
			var params = {"email":"zhh@126.com"}
			$('#update').on('click', function(){
				var t = createTpl($('#result').html());
				$('#result').html(t(params));
			});
		</script>
	</body>