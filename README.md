# Anotações sobre o exemplo do Full Cycle

```
$ docker-compose up -d
```

👆Sobe o container sem travar o terminal.


Como o container foi criado na versão alpine do nginx, é necessário adicionar alguns recursos:

```
$ docker-compose exec nginx apk add bash vim
```

👆Instala o bash e o vim no container rodando


```
$ docker-compose exec nginx bash
```

👆 Entra no container do nginx

## Dentro do container

```
$ vim /etc/nginx/nginx.conf
```
👆Acessa o principal arquivo de configuração gerais do servidor, o nginx.conf. Não mexer. A linha que carrega as configurações dos sites é a última, em `include /etc/nginx/conf.d/*.conf`.


```
$ vim /etc/nginx/conf.d/default.conf
```
👆Acessa o arquivo de configuração que deve ser editado para criar sites. Na linha `listen 80;` ele está apontando para a porta do nginx (dentro do container). `server_name localhost` é o nome do domínio. Ainda não mexer.

```
$ nginx -t
```
👆_Importante:_ Teste a sintaxe do arquivo configuração do nginx antes de reiniciar o container.


## Criando uma máquina NODE 1

Duplicar o nginx do arquivo docker-compose.yml e renomear node1, sem expôr a porta para fora do container. O Nginx central é que vai acessar o node1. 

Adicione o bash e o vim usando o exec nesse container também.

### No container do node1

```
$ vim /usr/share/nginx/html/index.html
```
👆Acessar o html de resposta padrão do nginx sóvpara demonstrar. O certo seria que esse node1 fosse uma das instâncias da aplicação.


## Criando o proxy reverso


```
$ vim /etc/nginx/conf.d/default.conf
```
👆Acessar novamente o default.conf do container do *nginx* base para editar.



```
location {
    proxy_pass http://node1;
}
```
👆Dentro do location, substituir todo o conteúdo pelo proxy_pass apontando para o node1 e salve.


```
$ nginx -t
$ nginx -s reload
```

👆Testar e dar um reload no nginx para que ele bata no node1. Depois, acessar o localhost:8000 para ver a requisição batendo no node1.

## Criando uma máquina NODE 2

Replicar os passos tomados para criar o node 1. E depois acessar a máquina do nginx de novo para editar o default.conf.

```
upstream nodes {
    server node1;
    server node2;
}
```

👆Adicione o upstream no início do default.conf, fora do server. Cada linha aponta para uma instância da aplicação. 

```
location {
    proxy_pass http://nodes;
}
```
👆Apontar o proxy_pass para a variável do upstream. Salvar, testar a configuração e dar um reload no nginx.


Acessar o localhost:8000 para ver a requisição batendo no node1 e no node2, conforme o nginx faz o load balance.
