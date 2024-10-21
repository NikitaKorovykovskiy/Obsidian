```python
def export_as_xlsx(self, request, queryset):
        """Экспорт в формате XLSX"""
        output = io.BytesIO()
        workbook = Workbook()
        worksheet = workbook.active
        workbook.title = "Отчет"

        headers = ["Пользователь", "Организация выдавшая поощрение", "Поощрение", "Дата-время формирования запроса", "Статус получения поощрения", "Дата подтверждения поощрения"]
        worksheet.append(headers)

        local_tz = timezone('Asia/Krasnoyarsk')

        for obj in queryset:
            timestamp = local_tz.normalize(obj.timestamp.astimezone(local_tz)).replace(tzinfo=None) if obj.timestamp else None
            worksheet.append(
                [
                    obj.user.username,
                    obj.goods_n_services_item.organization.name,
                    obj.goods_n_services_item.name,
                    timestamp,
                    obj.status,
                    obj.confirmation_promotion_date
                ]
        )
        workbook.save(output)
        output.seek(0)
        response = HttpResponse(
            output.getvalue(),  # Получаем данные из BytesIO
            content_type='application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
        )

        # Задаем имя файла
        file_name = "Отчет по поощрениям.xlsx"
        file_expr = "filename*=utf-8''{}".format(quote(file_name))
        response["Content-Disposition"] = f'attachment; {file_expr}'

        return response
    
    export_as_xlsx.short_description = "Экспорт в формате XLSX"
```
Рабочая функция со скачиванием файла в виде таблицы и с русским названием