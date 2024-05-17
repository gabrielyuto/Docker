## **Principais Comandos do Docker via CLI**

- Fonte: https://gist.github.com/geovannaotoni/7fe8c8589132152290ca49cc8541deea
 
### **Gerenciando Imagens**
- `docker images`: visualizar todas as imagens Docker presentes na máquina;
- `docker pull <nome-da-imagem>:<tag>?`: baixar uma imagem do Docker Hub sem necessariamente executar esta imagem como um container;
- `docker rmi <nome/id-da-imagem>`: remover uma imagem Docker (acrônimo das palavras ReMover Imagem);
- `docker build`: construir uma imagem a partir de um Dockerfile;

### **Gerenciando Container**
- `docker <comando> <subcomando> <parâmetros>`: (Os  <parâmetros> são opcionais na execução dos comandos);
- `docker container ls`: listar todos os containers em execução no momento na máquina;
- `docker container ls -a`: exibe também containers que foram parados ou que terminaram sua execução;
  - Ou `docker ps -a`
- `docker container run <flags>? <imagem>:<tag> <argumentos>?`: comando padrão para subir um container
- `docker rm <nome-do-container>`: remove pelo nome os containers parados ou com execução terminada;
- `docker rm <id-do-container>`: remove pelo id;
- `docker rm -f <nome/id-do-container>`: apaga um container pelo seu nome ou id, até mesmo se ele estiver ativo (utilizando a flag -f para forçar o comando);
- `docker container start <nome/id-do-container>`: iniciar um container já criado e parado na lista;
- `docker container stop <nome/id-do-container>`: comando que força o container a terminar sua execução atual;
- `docker logs <flags> <nome-do-container>`: comando que permite ver  informações (logs, erros, etc) que o programa em segundo plano está executando ou ainda logs de um container parado (execução finalizada)
- `docker container prune`: remove todos os containers inativos do computador;
- `docker top <nome-do-container>`: verificar a lista de processos de um container específico;
- `docker exec -it <nome-do-container> <comando-a-ser-executado>`: Executa um comando em um contêiner em execução.
  - `<comando-a-ser-executado>`: *exemplo*: programa `sh` (programa de terminal linux), permite simular um acesso de terminal dentro do container que já está em execução;
- `docker attach <nome/id-do-container>`: acessar dentro do container e se conectar ao seu terminal, caso esse container esteja executando um processo interativo;
- `docker cp index.js <nome-do-container>:/pasta`: copiar o arquivo index.js do computador para a pasta do container;
- `docker create <imagem>:<tag>?`: cria um novo container, sem inicializá-lo.

*Exemplo:*
- `docker container run alpine:3.14 echo "Hello World"`: (`docker container run <flags>? <imagem>:<tag> <argumentos>?`)
  - o run cria um novo container;
  - Os parâmetros `<flags>?` e `<argumentos>?` são opcionais;
  - os parâmetros `<imagem>:<tag>` são a imagem Docker e a versão, exemplo alpine 3.14;

***Principais Flags***
- `--name <nome-da-sua-escolha>`: flag para dar um nome específico ao container criado, em vez de obter um nome aleatório do Docker;
- `--rm`: flag para que um container seja removido ao final da sua execução (Modo “auto-remover”);
- `-d` ou `--detach`: flag para que a execução do container ocorra em segundo plano, ou seja, embora não esteja visível, o container executará de forma assíncrona;
- `-it`:
  - Flag `-t`: solicita a criação de uma sessão de terminal;
  - Flag `-i`: necessária para que a sessão seja interativa; 
- `-v <caminho-da-pasta-com-arquivo-local>:<caminho-do-container>`: criar um volume a ser compartilhado entre o computador local e o container.
- `-e <nome_variavel>=<valor>`: informar as variáveis de ambiente.

***Comandos dentro de uma sessão interativa de terminal:***
- `ps aux`: lista todos os processos em execução no momento;
- `exit`: sair da do terminal do container.

## **Definições**
- **Imagem**: aplicação empacotada com todas as dependências necessárias já instaladas dentro dela.
- **Container**: processo totalmente isolado que representa a execução de uma imagem já construída anteriormente. Pode-se ter vários containers reproduzindo uma mesma imagem Docker.
- **Dockerfile**: arquivo que contém as instruções necessárias para construir uma imagem Docker.

## **Criando o arquivo Dockerfile**
1) Principais palavras reservadas, utilizadas dentro do arquivo:
- `FROM <nome-da-imagem>:<tag>?`: nome da imagem base para construção da nova imagem;
- `FROM <nome-da-imagem>:<tag>? AS <nome-do-estagio>` : serve para nomear o estágio atual durante o build (a última referência de FROM é a imagem final);
- `WORKDIR /pasta`: indica qual é pasta atual de trabalho dentro da imagem;
- `COPY index.html /caminho/da/imagem`: copiar um arquivo no computador local e colocá-lo dentro da imagem, no caminho especificado à frente;
  - Flag `--from=nome-do-estagio-anterior /caminho/do/arquivo /caminho/estagio/atual`: possui a capacidade de copiar arquivos entre os estágios, indica que deve-se copiar o seguinte arquivo ou pasta de um estágio para o estágio atual;
- `EXPOSE port`: dizer em qual porta a aplicação vai rodar (por padrão é 80), esse comando não abre nenhuma porta, é apenas para fins de documentação;
- `ENV key value`: setar variáveis de ambiente que podem ser utilizadas dentro do container
- `RUN`: indica que o comando à frente deve ser executado imediatamente, durante o processo de build;
- `ENTRYPOINT`: indica qual comando deve ser executado ao iniciar a imagem como um container;
- `CMD ["echo", "hello world"] ou CMD echo "hello world"`: sugestão de comando a ser executado ao iniciar essa imagem como um container (aceita uma lista de parâmetros ou apenas os comandos). Pode ser substituído ao executar o comando `docker run <nome-da-imagem> <comando> <argumentoX>`;
2) No terminal:
- `docker build <flags>? -t <nome-da-imagem>:<tag>? <contexto>`: Construir a imagem
  - `-t`: indica qual será o nome da imagem;
  - `<contexto>`: caminho de pasta para o Docker se basear para processar o arquivo Dockerfile. Geralmente utiliza-se apenas  `.` , que indica a pasta atual.
- `docker run -d -p 1234:80 --name <nome-do-novo-container> <nome-da-minha-imagem>` : Executar um novo container com a imagem criada
  - `-p 1234:80`: solicita ao Docker que faça um mapeamento da porta 1234 do nosso computador para a porta 80 da rede do container (faz o bind das portas)
  - `-P`: com P maiúsculo, escolhe uma porta da máquina aleatoriamente para, ao acessar o localhost naquela porta específica, redirecionar para a porta 80 do container.

## **Docker Compose: Gerenciar múltiplos containers**
1) Dentro do arquivo `docker-compose.yaml`:
```js
version: "3"
services:
  frontend:
    build: frontend/
    restart: always
    ports:
      - 3000:3000
    depends_on:
      - backend
    volumes:
      - ./logs:/var/log/frontend
    networks:
      - rede-virtual-1
  backend:
    build: backend/
    restart: always
    ports:
      - 3001:3001
    environment:
      - DB_HOST=database
    depends_on:
      - database
    networks:
      - rede-virtual-1
      - rede-virtual-2
  database:
    image: betrybe/docker-compose-example-database:v1
    restart: always
    volumes:
      -dados-do-banco:data/db
    networks:
      - rede-virtual-2

volumes:
  dados-do-banco:

networks:
  rede-virtual-1:
  rede-virtual-2:
```

- `version`: todo arquivo deve iniciar com a chave version, que indica a versão do Arquivo Compose;
- `services`: Definir os serviços e especificar a imagem de cada um:
  - `image: <nome-da-imagem>`: imagem Docker pronta, seja local ou a ser baixada;
  - `build: pasta/`: pasta contendo um arquivo Dockerfile a partir do qual o Compose vai executar o comando docker build automaticamente;
- `ports: - portaLocal:portaContainer`: mapear as portas entre o computador local e as portas do container;
- `restart: <comando>`: Política de reinicialização: através da chave restart.
  - `no`: o container não reiniciará automaticamente (padrão);
  - `on-failure`: o container será reiniciado caso ocorra alguma falha apontada pelo exit code (diferente de zero);
  - `always`: sempre que o serviço parar, seja por uma falha ou porque ele simplesmente finalizou sua execução, ele deverá ser reiniciado;
  - `unless-stopped`: o container sempre será reiniciado, a menos que se utilize o comando docker stop <container> manualmente.
- `environment: - NOME_DA_VARIÁVEL=VALOR`: criar variáveis de ambiente dentro dos containers;
- `depends_on: - <nome-do-serviço>`: lista de serviços que o Compose deve executar antes de executar o serviço atual;
- `volumes:`:
  - **ao final do arquivo**: especifica para o Compose que ele deve criar um volume virtual com o nome passado como argumento.
  - **no frontend**: mapeia uma pasta do computador local para uma pasta dentro do serviço de front-end: `caminho/no/computador:caminho/no/container`
  - **no database**: `<nome-do-volume>:<caminho-no-container-para-montar>`
- `networks:`: redes virtuais que permitem criar isolamento entre os serviços.
  - **ao final do arquivo**: declara-se o nome de uma nova rede virtual por linha;
  - **dentro de cada serviço**: declara-se a rede para permitir a comunicação direta entre os serviços;
- `container_name: <nome-do-container>`: nomear o container a ser criado;
- `command: <comando>`: executar o comando ao iniciar o container (mesma função do CMD no Dockerfile);

2) No terminal, dentro da pasta que contém o arquivo *docker-compose.yaml*:
- `docker-compose up -d`: subir todos os serviço.
- `docker-compose ps`: verificar o status dos serviços.
- `docker-compose up -d --build`: reconstruir as Imagens Docker após alguma alteração.
- `docker-compose down`: parar a execução de todos os serviços.
- `docker-compose up <serviço>`: subir serviços específicos.
- `docker-compose logs <nome-do-serviço>`: visualizar os logs de um dos serviços.
- `docker-compose up --scale service=<número-de-replicas>`: configurar a quantidade de réplicas (cópias de containers) para um mesmo serviço.


*Demais comandos Docker na documentação oficial: [https://docs.docker.com/engine/reference/commandline/docker/](https://docs.docker.com/engine/reference/commandline/docker/)*.
*Todos os direitos reservados à documentação oficial do Docker e à Trybe, cujos materiais serviram de base para a construção deste gist.*