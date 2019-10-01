<h1>Security/password/encryption with ElasticSearch and Kibana with Docker SSL/TLS/HTTPS</h1>
This docker-compose file define a starting point for a simple ElasticSearch  + Kibana cluster with all encrypted communications beetwen es nodes, es and kibana (https), kibana and browser (https)

References
- [https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-tls-docker.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-tls-docker.html)
- [https://www.elastic.co/fr/blog/elasticsearch-security-configure-tls-ssl-pki-authentication](https://www.elastic.co/fr/blog/elasticsearch-security-configure-tls-ssl-pki-authentication)
- [https://www.elastic.co/guide/en/kibana/6.3/using-kibana-with-security.html](https://www.elastic.co/guide/en/kibana/6.3/using-kibana-with-security.html)
  
Define elastic and kibana password in **.env** file 
```

ELASTIC_PASSWORD=es_ChangeMe 
KIBANA_PASSWORD=kibana_ChangeMe
```
Generate the certificates (only needed once):

> `docker-compose -f create-certs.yml run --rm create_certs`


Start two Elasticsearch nodes configured for SSL/TLS:

>`docker-compose up -d`

You now have access to kibana at : [https://localhost:5601](https://localhost:5601)

user : elastic / password : es_ChangeMe

Remove all
>`docker-compose down -v`

<hr>

Access the Elasticsearch API over SSL/TLS using the bootstrapped password:

>`docker run --rm -v es_certs:/certs --network=es_default docker.elastic.co/elasticsearch/elasticsearch:7.3.2 curl --cacert /certs/ca/ca.crt -u elastic:es_ChangeMe https://es01:9200`

Change kibana password to communicate with es

>`docker run --rm -v es_certs:/certs --network=es_default docker.elastic.co/elasticsearch/elasticsearch:7.3.2 curl -XPOST --cacert /certs/ca/ca.crt -u elastic:es_ChangeMe  https://es01:9200/_security/user/kibana/_password -H 'Content-Type: application/json' -d '{"password": "new_kibana_password"}'`

Now kibana can't communicate with ES.

(Re)apply password defined in **.env** file

> `docker-compose  run  --rm create_passwords`


Change elastic password

>`docker run --rm -v es_certs:/certs --network=es_default docker.elastic.co/elasticsearch/elasticsearch:7.3.2 curl -XPOST --cacert /certs/ca/ca.crt -u elastic:es_ChangeMe  https://es01:9200/_security/user/elastic/_password -H 'Content-Type: application/json' -d '{"password": "new_es_password"}'`

Now you have acces to kibana with user : elastic / password : new_es_password