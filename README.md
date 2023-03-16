# Модуль 7. Системы мониторинга
Ссылки на тестовые развертывания, продемонстрированные на лекции. 

## ELK

Репозиторий с развертыванием в docker-compose: https://github.com/deviantony/docker-elk

Конфигурация Filebeat, `filebeat.docker.yml`:
```
filebeat.config:
  modules:
    path: ${path.config}/modules.d/*.yml
    reload.enabled: false

filebeat.autodiscover:
  providers:
    - type: docker
      hints.enabled: true

processors:
- add_cloud_metadata: ~

output.elasticsearch:
  hosts: '${ELASTICSEARCH_HOSTS:elasticsearch:9200}'
  username: '${ELASTICSEARCH_USERNAME:}'
  password: '${ELASTICSEARCH_PASSWORD:}'
```

Запуск filebeat:
```
docker run -d \
  --name=filebeat \
  --user=root \
  --volume="$(pwd)/filebeat.docker.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro" \
  --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
  --network docker-elk_elk \
  -e ELASTICSEARCH_USERNAME=elastic \
  -e ELASTICSEARCH_PASSWORD=changeme \
  docker.elastic.co/beats/filebeat:8.6.2 filebeat -e --strict.perms=false \
  -E "output.elasticsearch.hosts=["docker-elk-elasticsearch-1:9200"]"
```

## Prometheus

Репозиторий с развертыванием в docker-compose: https://github.com/vegasbrianc/prometheus

