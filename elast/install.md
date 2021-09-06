для работы td-agent (Fluentd), затем дополнить конфиг, который расположен "C:/opt/td-agent/etc/td-agent/td-agent.conf".


Запуск ElsticSearch и кибана происходит так же с помощью установки и дальнейшего старта с помощью подкладывания необходимых конфигов.

Для корректной работы необходимо добавить сертификаты, для этого в конфигурационном файле elasticsearch.yaml необходимо найти строки "xpack.security.enabled" и "xpack.security.transport.ssl.enabled", в ключе которых требуется установить "false"
далее необходимо запустить следующую строку "./bin/elasticsearch-setup-passwords auto", результатом которой будет список паролей для различных подсистем. 
следом получаем сертификат с помощью строки "bin/elasticsearch-certutil ca", будет выведена примерно следующая строка "Please enter the desired output file [elastic-stack-ca.p12]: Enter password for elastic-stack-ca.p12 :" Будет создан файл "elastic-stack-ca.p12" после чего выполняем следующую комманду "bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12", результатом будет файл "elastic-certificates.p12". Его так же необходимо будет перенести в папку с конфигурациями.
После получения паролей следует вернуть в вышеуказанные строки параметр "true". 

Для взаимодействия ElasticSearch с Kibana в kibana.yml требуется изменить параметр "<kibana password>" в строке "elasticsearch.password: <kibana password>" на тот ключ, который был получен в предыдущем шаге.









=============================
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.keystore.type: PKCS12
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.type: PKCS12