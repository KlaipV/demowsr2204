# demowsr2204

HKEY-LOCAL-MACHINE\SYSTEM\CurretControlSet\Services\CertSvc\Configuration\demo.wsr\ValidatyPeriodUnits

Ставим значение от 2 лет и все
=============================================
ОБЯЗАТЕЛЬНО ПЕРЕЗАГРУЗИТСЯ ПЕРЕД СОЗДАНИЕМ СЕРТА
=============================================
и после этого создаем сертификат через MMC

=============================================
чтобы скопировать конфиг nginx, НУЖНО:
1. Маунтим диск из папки образа docker_inst.iso
2. После этого устанавливаем Докер и запускаем его
3. Заходим в папку /mnt/app
4. Копируем файл "readme.md5" ОБЯЗАТЕЛЬНО ЧЕРЕЗ MC, ТАК КАК СР НЕ РАБОТАЕТ в папку /opt/share
5. Копируем файл из папки /opt/share по пути /etc/nginx/sites-available/default
6. Заходим в него и меняем ip с 192.168.200.100 на 192.168.100.100
7. PROFIT конфиг nginx готов 

upstream backend {
  server 192.168.100.100:8080 fail_timeout=25;
  server 172.16.100.100:8080 fail_timeout=25;
 }
 
 server {
  listen 443 ssl default_server;
  iclude snippets/snakeoil.conf
  server_name www.demo.wsr;
  ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.3;
  location / {
    proxy_pass http://backend;
  }
 }
 
 server {
  listen 80 default_server;
  server_name _;
  return 301 https://www.demo.wsr;
}




RTR-L (ACL)
access-list 1 permit 192.168.200.0 0.0.0.255
ip nat inside source list 1 interface Gi1 overload
ip access-list extended Lnew
permit tcp any any established
permit udp host 4.4.4.100 eq 53 any
permit udp host 5.5.5.1 eq 123 any
permit tcp any host 4.4.4.100 eq 80
permit tcp any host 4.4.4.100 eq 443
permit tcp any host 4.4.4.100 eq 2222
permit udp host 5.5.5.100 host 4.4.4.100 eq 500
permit esp any any
permit icmp any any
int gi 1
ip access-group Lnew in
RTR-R (ACL)
access-list 1 permit 172.16.100.0 0.0.0.255
ip nat inside source list 1 interface Gi1 overload
ip access-list extended Rnew
permit tcp any any established
permit tcp any host 5.5.5.100 eq 80
permit tcp any host 5.5.5.100 eq 443
permit tcp any host 5.5.5.100 eq 2244
permit udp host 4.4.4.100 host 5.5.5.100 eq 500
permit esp any any
permit icmp any any
int gi 1
ip access-group Rnew in

