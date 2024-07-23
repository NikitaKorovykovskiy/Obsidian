
При сохранении в админке поля float в виде 0,0 (через запятую) возникает ошибка валидации.

Это можно решить таким образом.

Создаем форму
```python
class EcologyAtmosphericAirIndicatorForm(forms.ModelForm):
```

Переопределяем поля на другой тип, н-р CharField
```python
pdkmr_custom = forms.CharField(required=False, label="ПДК м.р. ручной ввод", widget=forms.TextInput(attrs={'placeholder': '0.0'}))

pdkss_custom = forms.CharField(required=False, label="ПДК с.с. ручной ввод", widget=forms.TextInput(attrs={'placeholder': '0.0'}))
```
*placeholder* - это подсказка как help_text

Пишем метод очистки данных
```python
def clean(self):
        cleaned_data = super().clean()
        pdkmr_custom = cleaned_data.get('pdkmr_custom')
        pdkss_custom = cleaned_data.get('pdkss_custom')
```

В нем преобразовываем методы
```python
if pdkmr_custom:
            pdkmr_custom = pdkmr_custom.replace(',', '.')
            try:
                cleaned_data['pdkmr_custom'] = float(pdkmr_custom)
            except ValueError:
                self.add_error('pdkmr_custom', 'Введите корректное число. Число должно иметь вид 0.0')
        else:
            cleaned_data['pdkmr_custom'] = None

        if pdkss_custom:
            pdkss_custom = pdkss_custom.replace(',', '.')
            try:
                cleaned_data['pdkss_custom'] = float(pdkss_custom)
            except ValueError:
                self.add_error('pdkss_custom', 'Введите корректное число. Число должно иметь вид 0.0')
        else:
            cleaned_data['pdkss_custom'] = None
```


Полный вариант
```python
class EcologyAtmosphericAirIndicatorForm(forms.ModelForm):
    pdkmr_custom = forms.CharField(required=False, label="ПДК м.р. ручной ввод", widget=forms.TextInput(attrs={'placeholder': '0.0'}))
    pdkss_custom = forms.CharField(required=False, label="ПДК с.с. ручной ввод", widget=forms.TextInput(attrs={'placeholder': '0.0'}))

    class Meta:
        model = EcologyAtmosphericAirIndicator
        fields = ["name", "unit", "pdkmr", "pdkss", "pdkmr_custom", "pdkss_custom"]

    def clean(self):
        cleaned_data = super().clean()
        pdkmr_custom = cleaned_data.get('pdkmr_custom')
        pdkss_custom = cleaned_data.get('pdkss_custom')
        if pdkmr_custom:
            pdkmr_custom = pdkmr_custom.replace(',', '.')
            try:
                cleaned_data['pdkmr_custom'] = float(pdkmr_custom)
            except ValueError:
                self.add_error('pdkmr_custom', 'Введите корректное число. Число должно иметь вид 0.0')
        else:
            cleaned_data['pdkmr_custom'] = None

        if pdkss_custom:
            pdkss_custom = pdkss_custom.replace(',', '.')
            try:
                cleaned_data['pdkss_custom'] = float(pdkss_custom)
            except ValueError:
                self.add_error('pdkss_custom', 'Введите корректное число. Число должно иметь вид 0.0')
        else:
            cleaned_data['pdkss_custom'] = None
            
        return cleaned_data
```