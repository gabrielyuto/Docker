
# Comunicação com Docker

Quando se cria um banco de dados Postgres com Docker, podemos nos conectar ao banco pelo comando:

```bash
docker exec -it <container_name> psql -U postgres
```

Com isso, teremos acesso ao banco do container. Junto a isso podemos listar as tabelas no banco através do comando:

```bash
\l
```

![alt text](/teoria/images/image.png)


Para listar as tabelas, primeiro precisamos nos conectar ao banco. Note que no exemplo acima, o banco que esta conectado é o postgres.
Dessa forma, para trocar de conexão, podemos rodar o comando:

```bash
\c <nome_do_database>
```

![alt text](/teoria/images/image.png)

E assim, podemos listar as tabelas com o comando:

```bash
\dt 
```

![alt text](/teoria/images/image-1.png)

E dai em diante é so navegar com os comandos SQL do postgres:

```bash
# Lista de comandos:

select 
```