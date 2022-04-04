---
layout: post
title: 2022-03-04-csv导入mongodb 
tags: [mongodb,csv]
category: 数据库
toc: true
math: true
author: zzhc
---

```python
import csv
import json
import os
import pymongo


myclient = pymongo.MongoClient(host, port)
f_d = myclient['xx']


path = "D:\数据\金融"
datanames = os.listdir(path)


nums = 0

for i in datanames:
    nums += 1
    namei = i.replace('.csv', '')
    print(namei)
    mycol = f_d[namei]

    with open(path + '/' + i, 'r', encoding='utf-8') as f:
        next(f)
        lines = f.readlines()

        fieldnames = ('date', 'open', 'close', 'high', 'low', 'volume', 'money')

        reader = csv.DictReader(lines, fieldnames)
        js = json.dumps([row for row in reader], ensure_ascii=False)
        data = []
        data.extend(json.loads(js))
        print(data[:10])
        mycol.insert_many(data)
        print(namei,' ',nums,'ok')
        print(namei,' ',nums,'ok')
        print(namei,' ',nums,'ok')


```
