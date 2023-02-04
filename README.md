# 10.5---HAProxy-Nginx
Load Balansing with HAProxy and Nginx

## Задание 1
Что такое балансировка нагрузки и зачем она нужна?
Приведите ответ в свободной форме.

## Задание 2
Чем отличаются алгоритмы балансировки Round Robin и Weighted Round Robin? В каких случаях каждый из них лучше применять?
Приведите ответ в свободной форме.

## Задание 4-5
4. Установите и запустите Nginx.
Приведите скриншот systemctl status nginx, где будет видно, что Nginx запущен.
5. Настройте Nginx на виртуальной машине таким образом, чтобы при запросе:
curl http://localhost:8088/ping
он возвращал в ответе строчку:
"nginx is configured correctly".
Приведите конфигурации настроенного Nginx сервиса и скриншот результата выполнения команды curl http://localhost:8088/ping.

```
server {
listen 8088;

location /{
return 200 'nginx is configured correctly';
 }
}
```

![nginx_result](https://github.com/RSafin12/10.5-HAProxy-Nginx/blob/main/nginx_result.png)

## Задание 3-6
3. Установите и запустите Haproxy.
Приведите скриншот systemctl status haproxy, где будет видно, что Haproxy запущен.
![haproxy-installed](https://github.com/RSafin12/10.5-HAProxy-Nginx/blob/main/haproxy-installed.png)

6. Настройте Haproxy таким образом, чтобы при ответе на запрос:
curl http://localhost:8080/
он проксировал его в Nginx на порту 8088, который был настроен в задании 5 и возвращал от него ответ:
"nginx is configured correctly".
Приведите конфигурации настроенного Haproxy и скриншоты результата выполнения команды curl http://localhost:8080/.

Я настроил 2 VDS, повесив на nginx разные сообщения. 
Результат выполнения, как видно при curl-запросе поочередно перкидвает на разные серверы. 
![haproxy_balansing](https://github.com/RSafin12/10.5-HAProxy-Nginx/blob/main/haproxy_balansing.png)

Для настройки балансировки в дефолтный файл /etc/haproxy/haproxy.cfg добавил секции frontend и backend. Весь конфиг не привожу, он доступен [тут](https://github.com/RSafin12/10.5-HAProxy-Nginx/blob/main/haproxy.cfg). 
```
### Fronted section
frontend task-6_front
bind *:8080
# status uri /haproxy?stats
default_backend task-6_back

### Backend section
backend task-6_back
balance roundrobin
# mode http
server brave 172.16.16.16:8088 check
server strong 172.16.16.15:8088 check
```