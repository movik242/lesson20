# lesson20
***Домашнее задание***</br>
Фильтрация трафика</br>
Задачи</br>
реализовать knocking port</br>
- centralRouter может попасть на ssh inetrRouter через knock скрипт пример в материалах</br>
добавить inetRouter2, который виден(маршрутизируется (host-only тип сети для виртуалки)) с хоста или форвардится порт через локалхост</br>
запустить nginx на centralServer</br>
пробросить 80й порт на inetRouter2 8080</br>
дефолт в инет оставить через inetRouter</br>
* реализовать проход на 80й порт без маскарадинга</br>

Выполнение</br>
Произвел настройку согласно примеру в статье https://wiki.archlinux.org/index.php/Port_knocking#Simple_port_knocking. </br>
Сценарий для iptables подгружается командой iptables-restore из файла сценария, который синхронизируется из каталога с Vagrantfile в директорию /vagrant на виртуалках.</br>

Добавляю inetRouter2. Прописываю ему дополнительный интерфейс (eth2 192.168.11.121), чтобы обеспечить доступность с хостовой машины для проверки проброса запросов до nginx на centralServer.

Установлен nginx из репозитория epel-release. Модицифирован дефолтный index.html, сервис запущен на прослушивание tcp:80.

Проброшен порт 8080 с хоста inetRouter2 на 80 порт хоста centralServer без использования MASQUERADE следующими командами:

    iptables -A FORWARD -d 192.168.0.2/32 -p tcp -m tcp --dport 80 -m state --state NEW,RELATED,ESTABLISHED -j ACCEPT
    iptables -t nat -A PREROUTING -i eth2 -p tcp -m tcp --dport 8080 -j DNAT --to-destination 192.168.0.2:80
    
Для того, чтобы centralRouter знал, куда возвращать пакеты при запросах с хостовой машины, необходим дополнительный маршрут:

С хостовой машины проверяю проброс до nginx

![image](https://github.com/movik242/lesson20/assets/143793993/cf073085-f989-4050-b342-728b0a2a6881)

![image](https://github.com/movik242/lesson20/assets/143793993/b40a03a9-0dd2-4741-8f2b-5cf49b953d02)

![image](https://github.com/movik242/lesson20/assets/143793993/dde5e957-b8c1-48dc-a97b-ee2163d752a7)

![image](https://github.com/movik242/lesson20/assets/143793993/9d527f6c-663a-4027-ba11-57cabeecfdb5)


Дефолт наружу оставлен через inetRouter</br>
Проверка</br>
Проверяю port-knocking (юзер и пароль vagrant)
По дефолту:

![image](https://github.com/movik242/lesson20/assets/143793993/c31307b5-e438-40e4-a009-cc98b4c45c64)


    bash /vagrant/knock.sh 192.168.255.1 8881 7777 9991

![image](https://github.com/movik242/lesson20/assets/143793993/b6f85f79-fc10-4584-bbb8-afbba995f95c)
