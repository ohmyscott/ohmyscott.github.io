---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ dateFormat "2006-01-02" .Date }}
categories: ["技术"]
tags: []
slug: "{{ .File.ContentBaseName }}"
draft: true
description: ""
ShowToc: true
TocOpen: false
---
