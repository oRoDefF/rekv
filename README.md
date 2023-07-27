## Домашнее задание к лекции 8.«Работа с библиотекой requests, http-запросы»

# Задача №1

Кто самый умный супергерой?
Есть API по информации о супергероях с информацией по всем супергероям. Нужно определить кто самый умный(intelligence) из трех супергероев- Hulk, Captain America, Thanos.

import requests

TOKEN = '2619421814940190'  # константа токена
urls = [
    f'https://www.superheroapi.com/api.php/{TOKEN}/search/Hulk',
    f'https://www.superheroapi.com/api.php/{TOKEN}/search/Thanos',
    f'https://www.superheroapi.com/api.php/{TOKEN}/search/Captain%America',
]  # список адресов


```
def requests_get(url_all):
    # принимает список адресов
    r = (requests.get(url) for url in url_all)
    return r


def parser():
    # функция парсинга интелекта
    super_man = []
    for item in requests_get(urls):
        intelligence = item.json()
        try:
            for power_stats in intelligence['results']:
                super_man.append({
                    'name': power_stats['name'],
                    'intelligence': power_stats['powerstats']['intelligence'],
                })
        except KeyError:
            print(f"Проверте ссылки urls: {urls}")

    intelligence_super_hero = 0
    name = ''
    for intelligence_hero in super_man:
        if intelligence_super_hero < int(intelligence_hero['intelligence']):
            intelligence_super_hero = int(intelligence_hero['intelligence'])
            name = intelligence_hero['name']

    print(f"Самый интелектуальный {name}, интелект: {intelligence_super_hero}")


parser()
```


# Задача №2

У Яндекс.Диска есть очень удобное и простое API. Для описания всех его методов существует Полигон. Нужно написать программу, которая принимает на вход путь до файла на компьютере и сохраняет на Яндекс.Диск с таким же именем.

1. Все ответы приходят в формате json;
2. Загрузка файла по ссылке происходит с помощью метода put и передачи туда данных;
3. Токен можно получить кликнув на полигоне на кнопку "Получить OAuth-токен".

HOST: https://cloud-api.yandex.net:443

```
import requests


class YaUploader:
    def __init__(self, _token: str):
        self.token = _token

    def upload(self, file_path):
        """Метод загружает файл file_path на Яндекс.Диск"""
        # Определяем запрос для получения ссылки согласно документации Yandex.API
        upload_url = "https://cloud-api.yandex.net/v1/disk/resources/upload"
        # Выделяем имя загружаемого файла
        filename = file_path.split('/', )[-1]
        # Определяем формат заголовков запроса
        headers = {'Content-Type': 'application/json', 'Authorization': 'OAuth {}'.format(self.token)}
        # Определяем параметры запроса (назначаем путь загрузки, имя файла и разрешаем перезапись)
        params = {"path": f"Загрузки/{filename}", "overwrite": "true"}
        # Выполняем запрос на получение ссылки для загрузки
        _response = requests.get(upload_url, headers=headers, params=params).json()
        # Выделяем ссылку для загрузки в отдельную переменную
        href = _response.get("href", "")
        # Выполняем запрос на загрузку файла на Яндекс.Диск по полученной ссылке
        responce = requests.put(href, data=open(file_path, 'rb'))
        # Получаем статус отправки файла
        responce.raise_for_status()
        # Проверяем успешность отправки по полученному статусу
        if responce.status_code == 201:
            return 'Успешно'
        else:
            return f"Ошибка загрузки! Код ошибки: {responce.status_code}"


if __name__ == '__main__':
    # Получаем путь к загружаемому файлу и токен от пользователя
    path_to_file = '/home/gpgr/Загрузки/tasks-math-4-11-sch-msk-20-21.pdf'
    token = ''
    # Определяем экземпляр класса для токена пользователя
    uploader = YaUploader(token)
    # Загружаем файл на диск
    print(f"Загружаем файл {path_to_file.split('/', )[-1]} на Яндекс.Диск")
    result = uploader.upload(path_to_file)
    print(result)
```