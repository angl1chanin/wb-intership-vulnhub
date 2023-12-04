### Инструкция по сборке и запуску приложения

Установка и запуск
```
git clone https://github.com/angl1chanin/wb-intership-vulnhub.git
cd wb-intership-vulnhub
go get ./...
go run ./cmd/app
```
##### Приложение запущено, переходим по <code>localhost:3333</code>


#### Нас встречает главная страница

<img width="1155" alt="Screenshot 2023-12-04 at 13 19 04" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/7b72649f-66a0-4c35-b987-5cddcffcb425">

...

### Proof of Concept

> 1. ### XSS
> - Отправить GET параметр username с содержимым ```<script>alert(’hacked’)</script>```
> - <img width="445" alt="Screenshot 2023-12-04 at 13 23 53" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/99a2e3d3-4a16-460b-8b72-1db65286f246">
> - <img width="1856" alt="Screenshot 2023-12-04 at 13 24 13" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/e38dce94-cf52-4724-8938-63636e6c03db">
> 2. ### SQLi
> - <img width="1100" alt="Screenshot 2023-12-04 at 13 24 50" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/0073f2ec-72f9-4f40-93ca-2f422191aae4">
> - При выполнении следующего запроса, получаем содержимое другой таблицы: ```0 union SELECT * FROM flags WHERE id = 2```
> - <img width="1325" alt="Screenshot 2023-12-04 at 13 25 49" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/06ab767c-4fec-47de-897f-99ef92115c2e">
> 3. ### IDOR
> - На странице находятся три заметки
> - <img width="1460" alt="Screenshot 2023-12-04 at 13 26 36" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/c9f8bc5d-227e-4eaa-b80d-64ba5e573299">
> - При переходе на заметку, отображается конкретная заметка, а в адресной строке можно заметить GET параметр id.
> - <img width="1831" alt="Screenshot 2023-12-04 at 13 26 44" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/95a3c245-67d8-46b4-ba4b-9a0d06665d4b">
> - Путем перебора параметра id (с помощью Burp Suite>intruder), находим id=1337
> - <img width="1818" alt="Screenshot 2023-12-04 at 13 26 56" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/f26eb646-de46-4b6b-ba55-69a961c92faf">
> 4. ### OS Command Injection
> - На странице предлагается пропинговать какой либо хост.
> - <img width="1176" alt="Screenshot 2023-12-04 at 13 28 01" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/8cc1be5d-0fdd-4b65-a25e-58a236ecc6fe">
> - Поле input принимает ip адрес
> - <img width="1121" alt="Screenshot 2023-12-04 at 13 28 12" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/529313bf-d1e4-4d5f-b076-895251fbdcba">
> - Но можем дополнить команду, poc: ```s| uname -a```
> - <img width="1094" alt="Screenshot 2023-12-04 at 13 29 34" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/38721e69-5f60-4991-bba7-6b63937270e0">
> 5. ### Path Traversal
> - На странице предлагается посмотреть некие картинки
> - <img width="1879" alt="Screenshot 2023-12-04 at 13 30 43" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/01d48ffc-72b6-4e9d-8cbd-fc1f5006c82b">
> - при открытии, можно заметить GET параметр file в адресной строке
> - <img width="1476" alt="Screenshot 2023-12-04 at 13 30 51" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/b558805b-bc0b-45cc-88d2-f12fc56496f8">
> - подставив в него ```../../../../../etc/passwd``` получим содержимое файла.
> - <img width="680" alt="Screenshot 2023-12-04 at 13 31 16" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/300f4b6e-291d-4840-b8e1-d3358d60b5a2">
> 6. ### Brute Force
> - На странице находится форма для ввода пароля. 
> - <img width="1404" alt="Screenshot 2023-12-04 at 13 32 57" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/7e1cc60d-11af-4784-9876-c875addc5705">
> - При попытке ввода неправильного пароля, отображается соответствующее сообщение
> - <img width="1432" alt="Screenshot 2023-12-04 at 13 33 19" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/2f17fe78-99d6-4c88-8f54-f75489efb11e">
> - Напишем скрипт, который перебирает пароли из словаря ```rockyou.txt```(Словарь пришлось укоротить до нужного пароля, чтобы загрузилось в репозиторий). Директория содержащая скрипт и словарь: ```poc_scripts```
> - Скрипт находит нужный пароль
> - <img width="1421" alt="Screenshot 2023-12-04 at 13 48 15" src="https://github.com/angl1chanin/wb-intership-vulnhub/assets/68481069/095e8def-fd67-48df-b22f-7b501ec2bec6">
### Дополнительные комментарии
> Опциональный раздел

...