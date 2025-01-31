## Восстановление серверной части бизнеса по продаже конфет через автоматы

В этом проекте я помог мистеру Роджерсу восстановить его бизнес по продаже конфет через автоматы после того, как его сервер был украден, а предыдущий разработчик исчез. Я заново создал серверную часть, используя OpenAPI, и обеспечил её безопасность для обработки транзакций клиентов.
#### Выполненные задачи:

* **Генерация сервера по спецификации OpenAPI.** <br>
Я использовал OpenAPI для автоматической генерации серверной части. Это позволило быстро создать необходимые эндпоинты для взаимодействия с клиентами и автоматами по продаже конфет, что существенно упростило дальнейшую разработку.

* **Настройка и защита сервера** <br>
Я настроил сервер для безопасного взаимодействия с клиентами. Были реализованы такие меры защиты, как TLS с проверкой клиентских и серверных сертификатов для безопасной передачи данных. Ключи и сертификаты были сгенерированы с помощью инструмента `minica`, а сервер настроен на работу через защищённое соединение.

* **Интеграция и настройка Cowsay** <br>
Для придания проекту немного забавного стиля, я добавил `Cowsay` — утилиту на `C` в код программы на `Go`, которая выводит текст в виде «говорящей» ASCII-коровы в ответе в поле `thanks` при покупке конфет. Это помогло сделать вывод информации более визуально привлекательным и добавить элемент развлечения в проект. (По заданию ее написал племянник мистера Роджерса)

Благодаря выполненным задачам бизнес мистера Роджерса снова работает, а его серверная часть надёжно защищена и полностью функциональна.

#### Зависимости
* Доступ в интернет чтобы скачалась утилита `minica`, `docker`-образы
* `Docker`
* установленный `golang1.22` 

#### Установка
```sh
make -B
```
* Скачается `minica`, прокинутся сертификаты клиенту и серверу
* Соберет в контейнер серверную часть, доступную на порте `3333`
* Соберется в бинарник по пути `./bin/client` клиент

#### Использование
* **Используя клиент**
```
./bin/client -k AA -c 1 -m 50
```
|флаг|значение|
|-|-|
|-k|тип конфет (`CE`, `AA`, `NT`, `DE`, `YR`)|
|-c|количество конфет (целое значение больше нуля)|
|-m|количество передаваемых денег(целое значение больше нуля)|
* **Используя cURL** <br>
Если у вас есть доступ к изменению файла `/etc/hosts/` то, используйте эту команду перед вызовом `cURL`
```sh
echo "127.0.0.1 candy.ltd" | sudo tee -a /etc/hosts
```
тогда вы сможете использовать `cURL` для отправки запроса
```sh
curl -s --key certs/candy.ltd/key.pem --cert certs/candy.ltd/cert.pem --cacert certs/minica.pem \
  -POST -H "Content-Type: application/json" -d '{"candyType": "NT", "candyCount": 2, "money": 34}' \
  "https://candy.tld:3333/buy_candy"
```
**P.S** <br>
Не забудьте потом удалить маппинг `127.0.0.1 candy.ltd` из `/etc/hosts` <br>

Команды для генерации сервера
```sh
docker pull openapitools/openapi-generator-cli
docker run --rm -v ${PWD}:/local openapitools/openapi-generator-cli generate \
    -i /local/scheme.yaml \
    -g go-server \
    -o /local/generated-go-server
``` 
