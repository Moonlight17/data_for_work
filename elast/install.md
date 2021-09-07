для работы td-agent (Fluentd), затем дополнить конфиг, который расположен

> C:/opt/td-agent/etc/td-agent/td-agent.conf

В `td-agent.conf` необходимо поменять следующие строки:

> `<source>`  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;path `путь_к_файлу`&nbsp;&nbsp;&nbsp;&nbsp;#C:/opt/td-agent/var/logs/td-agent.log  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tag  `тэг_для_действия`  &nbsp;&nbsp;&nbsp;&nbsp;#work  
`</source>`  

> `<match тэг_для_действия>`  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;host `адрес_хоста_с_elascticsearch`&nbsp;&nbsp;&nbsp;&nbsp;#172.0.0.1 or abc.project-abc  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;index_name `название `&nbsp;&nbsp;&nbsp;&nbsp;#app1  
`</match>`

Запуск ElsticSearch и кибана происходит так же с помощью установки и дальнейшего старта с помощью подкладывания необходимых конфигов.

Для корректной работы необходимо добавить сертификаты, для этого в конфигурационном файле `elasticsearch.yaml` необходимо найти строки  

> "xpack.security.enabled": true  
> "xpack.security.transport.ssl.enabled": true

в которые требуется привети к виду:

> "xpack.security.enabled": false  
> "xpack.security.transport.ssl.enabled": false

далее необходимо запустить следующую строку

> ./bin/elasticsearch-setup-passwords auto

Результатом выполнения будет список паролей для различных подсистем.   
Следом получаем сертификат с помощью строки 

> ./bin/elasticsearch-certutil ca

Будет выведена примерно следующая строка 
> Please enter the desired output file [elastic-stack-ca.p12]:  
Enter password for elastic-stack-ca.p12 :  

Будет создан файл "elastic-stack-ca.p12" после чего выполняем следующую комманду:
> ./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12

результатом будет файл `elastic-certificates.p12`. Его так же необходимо будет перенести в папку с конфигурациями.

После получения паролей следует вернуть в вышеуказанные строки из файла `elasticsearch.yaml` параметр "true". 

> "xpack.security.enabled": true  
> "xpack.security.transport.ssl.enabled": true 

Для взаимодействия ElasticSearch с Kibana в `kibana.yaml` требуется изменить параметр `<kibana password>` в строке:

> elasticsearch.password: <kibana password>

на ключ, полученный в предыдущем шаге

=============================  

Финальным шагом в настройке взаимодействия следует добавить ниже указанные строки в файл `elasticsearch.yaml`:

> xpack.security.transport.ssl.keystore.type: PKCS12  
> xpack.security.transport.ssl.verification_mode: certificate  
> xpack.security.transport.ssl.keystore.path: elastic-certificates.p12  
> xpack.security.transport.ssl.truststore.path: elastic-certificates.p12  
> xpack.security.transport.ssl.truststore.type: PKCS12