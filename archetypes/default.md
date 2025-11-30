---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date.Format "2006-01-02" }}
categories: ["技术"]
tags: []
slug: "{{ .File.ContentBaseName }}"
draft: true
description: ""
ShowToc: true
TocOpen: true
---
