## <a name="firewall-configuration"></a>Настройка брандмауэра

Чтобы тесты, чтобы отправить в Xamarin Test Cloud компьютер, отправка тесты, должен иметь для взаимодействия с серверами Test Cloud. Брандмауэры должны быть настроены на разрешение сетевой трафик к и из серверов, расположенных в **testcloud.xamarin.com** на портах 80 и 443. Эта конечная точка находится под управлением DNS и IP-адрес может быть изменена. 

В некоторых ситуациях тест (или устройства под управлением теста) должны обмениваться данными с веб-серверами, защищенной брандмауэром. В этом сценарии необходимо настроить брандмауэр для разрешения трафика от следующих IP-адресов:

* **195.249.159.238**
* **195.249.159.239**
