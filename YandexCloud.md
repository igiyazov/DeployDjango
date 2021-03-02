# Подключение Yandex cloud на Django

## Настройка Yandex cloud
1) Создать Object Storage на [YandexCloud](https://console.cloud.yandex.ru/)
2) Создать системный аккаунт	| [_Как создать аккаунт?_](https://cloud.yandex.ru/docs/iam/operations/sa/create)
3) Создать secret-key и id		| [_Как создать ключи?_](https://cloud.yandex.ru/docs/iam/operations/sa/create-access-key)
4) Создать бакет для теста, например 'bucket-name'

> Сохраните в надёжном месте _secret-key_ и _id_, так как они доступны только при создании. 
> Если потеряли, то придётся создавать заново.

## Настройка Django
### _boto3_

Для конекта с Object Storage необходимо установить библиотеку [boto3](https://github.com/boto/boto3/blob/develop/README.rst#quick-start)
```
pip install boto3
```

Для настройки создайте в домашнем каталоге файлы конфигурации и задайте в них:
- Статический ключ в файле ```~/.aws/credentials```:
```
[default]
            aws_access_key_id = <id>
            aws_secret_access_key = <secretKey>
```
- Регион по умолчанию в файле ```.aws/config```:
```
[default]
            region=ru-central1
```
> Некоторые приложения, предназначенные для работы с Amazon S3, не позволяют указывать регион, поэтому Object Storage принимает также значение us-east-1.


Можно написать всю логику самому на python используя следуюций код:
```python
#!/usr/bin/env python
#-*- coding: utf-8 -*-
import boto3
session = boto3.session.Session()
s3 = session.client(
    service_name='s3',
    endpoint_url='https://storage.yandexcloud.net'
)

# Создать новый бакет
s3.create_bucket(Bucket='bucket-name')

# Загрузить объекты в бакет

## Из строки
s3.put_object(Bucket='bucket-name', Key='object_name', Body='TEST', StorageClass='COLD')

## Из файла
s3.upload_file('this_script.py', 'bucket-name', 'py_script.py')
s3.upload_file('this_script.py', 'bucket-name', 'script/py_script.py')

# Получить список объектов в бакете
for key in s3.list_objects(Bucket='bucket-name')['Contents']:
    print(key['Key'])

# Удалить несколько объектов
forDeletion = [{'Key':'object_name'}, {'Key':'script/py_script.py'}]
response = s3.delete_objects(Bucket='bucket-name', Delete={'Objects': forDeletion})

# Получить объект
get_object_response = s3.get_object(Bucket='bucket-name',Key='py_script.py')
print(get_object_response['Body'].read())
```

### _boto3 && django-storages_

Чтобы упростить себе задачу, можно воспользоваться библиотекой [django-storages](https://github.com/jschneier/django-storages).

* Установим [django-storages](https://github.com/jschneier/django-storages) набором команды:
```
pip install django-storages
```

* Подключаем [django-storages](https://github.com/jschneier/django-storages) к нашему проекту:
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
	
    'storages', #подключили
]
```

* Добавляем в ```settings.py``` следующие строки:
```python
AWS_ACCESS_KEY_ID = ''
AWS_SECRET_ACCESS_KEY = ''
AWS_STORAGE_BUCKET_NAME = 'youcleve'
AWS_S3_ENDPOINT_URL = 'https://storage.yandexcloud.net'
AWS_LOCATION = 'media'
MEDIA_URL = f'https://{AWS_S3_ENDPOINT_URL}/{AWS_LOCATION}/'
DEFAULT_FILE_STORAGE = 'myproject.storage_backends.MediaStorage'
```

- ```AWS_ACCESS_KEY_ID``` - id статического ключа
- ```AWS_SECRET_ACCESS_KEY``` - статический ключ
- ```AWS_STORAGE_BUCKET_NAME``` - название бакета, с которым будем работать
- ```AWS_S3_ENDPOINT_URL``` - путь к облаке
- ```AWS_LOCATION``` - директория, куда будут сохранятся файлы
- ```MEDIA_URL'``` - итоговый путь
- ```DEFAULT_FILE_STORAGE``` - класс для работы с определенным хранилищем 

> ```AWS_ACCESS_KEY_ID = ''``` между кавычками указываем id, который ранее получили от Yandex Cloud  
> ```AWS_SECRET_ACCESS_KEY = ''``` между кавычками указываем secret-key, который также получили от Yandex Cloud  
> ```AWS_LOCATION = 'media'``` можно указывать многоуровневые директории, например ```'media/videos'``` или ```'media/songs/texts'```  
>   
> Обязательно указать абсолютный путь к облаке  
> Неправильно: ```AWS_S3_ENDPOINT_URL = 'storage.yandexcloud.net'```  
> Правильно: ```AWS_S3_ENDPOINT_URL = 'https://storage.yandexcloud.net'```  

* Так как django-storage не работает с Yandex Cloud, нам необходимо написать свой класс для подключения.

Содадим файл ```storage_backends.py``` в корне проекта, в одной папке с ```settings.py```, и добавим следующий код:
```python
from storages.backends.s3boto3 import S3Boto3Storage

class MediaStorage(S3Boto3Storage):
    location = 'media'
    file_overwrite = False
```

Таким образом у нас построится следующий путь к классу ```MediaStorage```:
```myproject.storage_backends.MediaStorage```
Этот путь мы указали в ```DEFAULT_FILE_STORAGE```.



