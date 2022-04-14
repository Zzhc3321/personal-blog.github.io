---
layout: post
title: 2022-04-14-Django-admin页面配置 
tags: [后端,django]
category: Backend
toc: true
math: true
author: zzhc
---


## admin.py 配置

```python 

from django.contrib import admin
from charts.models import *
from django.http import HttpResponse
from openpyxl import Workbook
from urllib.parse import quote

admin.site.site_title = '数据管理后台'
admin.site.site_header = 'DBM'

class ExportExcelMixin(object):
    def export_as_excel(self, request, queryset):
        meta = self.model._meta
        field_names = [field.name for field in meta.fields]
        chinese_name = [field.verbose_name for field in meta.fields]
        filename = meta.verbose_name
        filename = quote(filename)
        response = HttpResponse(content_type='application/msexcel')
        response['Content-Disposition'] = f'attachment; filename={filename}.xlsx'
        wb = Workbook()
        ws = wb.active
        ws.append(chinese_name)
        for obj in queryset:
            for field in field_names:
                data = [f'{getattr(obj, field)}' for field in field_names]
            row = ws.append(data)
        wb.save(response)
        return response
    export_as_excel.short_description = '导出Excel'

```
