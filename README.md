# bas-ip
Описание работы домофонов BAS-IP



Для осуществления вызовов с панелей уличных домофонов BAS-IP, установленных в ЖК, предлагается использовать фирменный софт на мобильных телефонах этой же компании.

![](https://github.com/achaychenko/bas-ip/blob/main/image.png)


Для активации связи между домофонами и приложением необходимо в УК получить свою учетную запись на квартиру – пользователю выдают логин и пароль. Логин 5 цифр, пароль 4 цифры.
После этого в приложении указываются эти данные в виде логина и домена sip.bas-ip.com.
Например 12345@sip.bas-ip.com. Дальше можно делать исходящие вызовы с панелей домофонов на данное приложение, активное на телефоне, отвечать на вызовы с видеоизображением звонящего, открывать дверь по нажатию символа ключа.
На момент написания статьи имя сервера sip.bas-ip.com преобразовывается в единственный внешний IP адрес – 95.85.31.63, принадлежащей сети digitalocean, которая находится в европейской географической локации – Амстердам, вероятно местная точка обмена трафиком и дата-центр.
Панели домофонов, установленные в ЖК имеют внутренние адреса вида 192.168.хх.хх, адреса на мобильных устройствах могут быть вообще самыми разными, в зависимости от их местонахождения – внутренние WiFi, внешние или внутренние мобильных сетей в разных точках присутствия, роуминга и тд. 
Таким образом, точкой обмена трафика в паре домофон-приложение может быть только внешний сервер BAS-IP, вызовы с разных внутренних сетей звонящих точек не смогут ходить напрямую и никогда не будут успешными. Те предполагается что каждая точка регистрируется на сервере и дальнейшие вызовы идут через него.  Для анализа трафика достаточно получить дамп обмена между устройствами в своей локальной сети используя фильтр вида tcpdump host 95.85.31.63.

В полученном дампе видно, что соединение устанавливается по классическому SIP2.0 протоколу, между сервером Asterick PBX 14.2.1 и мобильным приложением.

![](https://github.com/achaychenko/bas-ip/blob/main/image2.png)

Далее следует обмен опциями и поднимаются аудио и видео потоки
![](https://github.com/achaychenko/bas-ip/blob/main/image3.png)

Для открытия двери на калитке с домофоном используется команда кнопки #.
Получается, что используя устройства с поддержкой SIP2.0 и учетную запись на сервер sip.bas-ip.com можно организовать обмен между ними, получая аудио-видео потоки, посылая управляющие сигналы. 
Для примера было использовано приложение для настольного компьютера Linphone, в котором ввели учетные данные, приложение зарегистрировалось на сервере после чего было сделано исходящее соединение с калитки домофона на домашний компьютер.
https://github.com/achaychenko/bas-ip/blob/main/image4.png
На приложении были получены аудио и видео данные с домофона, по управляющей команде # была открыта калитка.
Таким образом, можно предположить, что любое устройство, с необходимым SIP2.0 протоколом и учетной записью будет работать в паре с внешним домофоном, при этом выбор домашнего устройства продиктован удобством его подключения и использования. Это может быть настольный компьютер, планшет, SIP мобильный софт на телефоне. С питанием от сети, или по POE и подключенным к сети интернет по WiFi, витой паре, с любым внутренним или внешним IP адресом. 
Возможные ограничения.
Фильтрация вызовов на сервере, обрабатывающим SIP звонки. Допускаются вызовы только с внешних панелей домофонов, остальные, судя по всему, фильтруются. 
В будущем возможны ограничения на использование количества вызовов, фильтрация внешних адресов подключения (например, только для определенных провайдеров) или фильтрация по полю User-Agent в обмене опциями (только определенный software/hardware). Сложность такой фильтрации в том, что правила необходимо создавать и поддерживать на центральном сервере bas-ip, который обслуживает множество разнообразных пользователей и разных УК. Скорее всего потенциальные возможности делать индивидуальные дополнения именно для конкретной УК – это отдельные деньги, доработки, согласования и заказы к обслуживанию со стороны самой УК. Как показывает практика в этом поле деятельности УК достаточно неповоротливы и консервативны.
