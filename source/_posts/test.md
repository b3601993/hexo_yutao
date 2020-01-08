---
layout: '[layout]'
title: test
date: 2017-04-19 22:02:51
tags:
---

<% for (var link in site.data.menu) { %>
    <a href="<%= site.data.menu[link] %>"> <%= link %> </a>
<% } %>