---
refcn: chapter_00/faq
refen: welcome/faq
---
# Часто задаваемые вопросы (ЧаВо)

## Использование V2Ray

### Как обновить V2Ray

* Загрузите последний пакет, либо
* Запустите ещё раз скрипт установки (для Linux)

### Сбои V2Ray

* Если вы используете Linux с systemd, вы можете посмотреть в журнал с ошибками, используя команду `journalctl -u v2ray`.
* В общем случае вы можете запустить команду `v2ray -config =<config-file> -test` чтобы увидеть информацию об ошибке.

### Обратная совместимость

* For configuration file (e.g., JSON), backward compatibility applies to at least one major releases. For example, V2Ray 4.x supports config files from V2Ray 3.x.
* For Protobuf based communication protocols, such as [Api](../api.md), backward compatibility applies to at least one major releases.
* For binary based commnunication protocols, such as Shadowsocks and VMess, it is always backward compatible when server version is not older than clients. When client version is newer, backward compatibility applies to at least 12 minor releases.

## Ошибки V2Ray

### Socks: Unknown Socks version: 67

Возможная причина

* You are using socks inbound in V2Ray, but your browser is configured to use HTTP proxy.

Решение

* Add a HTTP inbound in V2Ray, and then update your browser proxy settings to use this proxy.

## Лицензия этого проекта

Project V использует следующую лицензию.

### V2Ray

Исходный код и официальные релизы распространяются под лицензией MIT, включая данные в следующих репозиториях.

* [v2ray/v2ray-core](https://www.github.com/v2ray/v2ray-core/)
* [v2ray/ext](https://www.github.com/v2ray/ext)

### V2Ray.Com

Официальный веб-сайт, [v2ray.com](https://www.v2ray.com/), лицензируется на условиях [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

* Включая все видимые тексты и изображения на веб-сайте.
* В том числе <a href="https://www.v2ray.com/resources/v2ray_1024.png" target="_blank">логотип Project V</a>.
* Включая весь исходный код, который используется для создания веб-сайта, а именно, [v2ray/manual](https://www.github.com/v2ray/manual).

### Скриншоты и другие файлы {#screenshots}

Все файлы третьих лиц, перечисленные ниже, принадлежат их создателям. Файлы принадлежат тем, кто их создал.

* Включая все скриншоты Project V.
* Включая все файлы конфигурации, которые используются для запуска Project V.
* Включая все логи, созданные Project V во время выполнения.

### Другой контент {#other}

Все не упомянутые выше материалы лицензируются индивидуально.