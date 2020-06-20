# Anotações:

```
$ docker-compose up -d
```

Sobe o container sem travar o terminal.

```
$ docker-compose exec nginx
```

Entra no container no nginx


## Dentro do container:

Como o container foi criado na versão alpine do nginx, é necessário adicionar alguns recursos:

```
docker-compose exec nginx apk add bash vim
```

Instala o bash e o vim no container rodando
