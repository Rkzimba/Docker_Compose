# Docker Compose — Conceitos e Configuração

## 1. O que é o Docker Compose?

O **Docker Compose** é uma ferramenta utilizada para definir e executar aplicações que possuem vários containers.

Em vez de criar e configurar cada container manualmente através de vários comandos Docker, podemos descrever toda a estrutura da aplicação em um único arquivo YAML, normalmente chamado `compose.yml` ou `docker-compose.yml`.

Assim, podemos definir os serviços, imagens, portas, volumes, redes, variáveis de ambiente e outras configurações da aplicação em um só lugar.

Depois, o Compose pode criar e iniciar toda essa estrutura com um único comando:

```bash
docker compose up
```

---

## 2. Qual é a finalidade de um arquivo `compose.yml`?

O arquivo `compose.yml` serve para **descrever como os containers de uma aplicação devem ser criados e executados**.

Nele podemos definir, por exemplo:

* quais serviços a aplicação possui;
* quais imagens Docker serão utilizadas;
* quais portas serão disponibilizadas;
* quais volumes serão utilizados;
* quais redes existirão;
* variáveis de ambiente;
* dependências entre os serviços;
* políticas de reinicialização dos containers.

Dessa forma, toda a configuração necessária para executar a aplicação fica centralizada em um arquivo.

---

## 3. Qual a diferença entre imagem, container, serviço, volume e rede?

### Imagem

A **imagem** é um modelo utilizado para criar containers.

Ela contém os arquivos, dependências, bibliotecas e configurações necessárias para executar determinado software.

Por exemplo:

```yaml
image: nginx
```

Nesse caso, a imagem do Nginx será utilizada para criar o container.

Uma forma simples de entender é pensar na imagem como uma **receita**.

---

### Container

O **container** é uma instância em execução de uma imagem.

Se a imagem é a receita, o container é o **prato preparado a partir dessa receita**.

Um container possui seu próprio ambiente isolado, mas utiliza o kernel do sistema operacional hospedeiro.

Por exemplo, a partir da imagem `nginx` podemos criar um container executando um servidor web.

---

### Serviço

O **serviço** representa uma aplicação ou componente que queremos executar dentro do ambiente definido pelo Compose.

Por exemplo:

```yaml
services:
  web:
    image: nginx

  banco:
    image: postgres
```

Nesse exemplo existem dois serviços:

* `web`;
* `banco`.

Cada serviço normalmente resulta em um ou mais containers, dependendo da configuração e da quantidade de réplicas.

---

### Volume

Um **volume** é utilizado para armazenar dados de forma persistente fora do ciclo de vida do container.

Isso é importante porque, normalmente, os dados armazenados diretamente dentro do container podem ser perdidos quando ele é removido.

Por exemplo, um banco de dados pode utilizar um volume para que seus dados continuem existindo mesmo se o container for recriado.

---

### Rede

A **rede Docker** permite que os containers se comuniquem entre si de maneira isolada.

No Docker Compose, os serviços normalmente são colocados automaticamente em uma rede criada pelo próprio Compose.

Assim, um serviço pode acessar outro utilizando o nome do serviço como endereço.

---

## 4. Para que serve a propriedade `services`?

A propriedade `services` é onde definimos os **serviços que fazem parte da aplicação**.

Por exemplo:

```yaml
services:
  web:
    image: nginx

  banco:
    image: postgres
```

Nesse caso, temos dois serviços:

* `web`, responsável pelo servidor web;
* `banco`, responsável pelo banco de dados.

Dentro de cada serviço podemos definir configurações como imagem, portas, volumes, variáveis de ambiente, dependências e redes.

---

## 5. O que significa?

```yaml
ports:
  - "8080:80"
```

Essa configuração cria um **mapeamento entre uma porta do computador e uma porta do container**.

A estrutura é:

```text
PORTA_DO_HOST:PORTA_DO_CONTAINER
```

Portanto:

```yaml
ports:
  - "8080:80"
```

significa que:

* a porta `8080` do computador será direcionada para;
* a porta `80` do container.

Assim, se um servidor web estiver escutando na porta `80` dentro do container, podemos acessá-lo através da porta `8080` do computador.

Por exemplo:

```text
http://localhost:8080
```

A porta `8080` está no computador, enquanto a porta `80` está dentro do container.

---

## 6. Qual a diferença entre `ports` e `expose`?

A principal diferença está na **disponibilidade da porta fora da rede Docker**.

### `ports`

A propriedade `ports` publica uma porta do container no computador hospedeiro.

Exemplo:

```yaml
ports:
  - "8080:80"
```

Nesse caso, podemos acessar o serviço a partir do computador através da porta `8080`.

### `expose`

A propriedade `expose` apenas informa ou disponibiliza uma porta para comunicação dentro das redes Docker associadas ao serviço.

Exemplo:

```yaml
expose:
  - "80"
```

Essa porta não é publicada diretamente para o computador hospedeiro.

Em resumo:

| Propriedade | Acesso entre containers | Acesso pelo host |
| ----------- | ----------------------- | ---------------- |
| `ports`     | Sim                     | Sim              |
| `expose`    | Sim                     | Não diretamente  |

Portanto, se precisamos acessar um serviço através do navegador no computador, normalmente utilizamos `ports`.

---

## 7. Como o Docker Compose cria uma rede entre os serviços?

Quando executamos uma aplicação através do Docker Compose, ele normalmente cria automaticamente uma **rede padrão para o projeto**.

Os serviços definidos no arquivo Compose são conectados a essa rede.

Por exemplo:

```yaml
services:
  web:
    image: nginx

  banco:
    image: postgres
```

Os containers dos serviços `web` e `banco` ficam na mesma rede criada pelo Compose.

Isso permite que eles se comuniquem diretamente sem que seja necessário criar manualmente uma rede para cada aplicação.

Também podemos criar redes personalizadas no arquivo Compose quando precisamos de uma configuração mais específica.

---

## 8. Como um container consegue acessar outro container?

Quando dois containers estão na mesma rede Docker, eles conseguem se comunicar através da rede interna do Docker.

No Docker Compose, normalmente podemos utilizar o **nome do serviço** como endereço.

Por exemplo:

```yaml
services:
  app:
    image: minha-app

  banco:
    image: postgres
```

A aplicação do serviço `app` pode acessar o banco utilizando:

```text
banco
```

como nome do servidor/host.

Por exemplo, uma string de conexão poderia utilizar:

```text
postgresql://usuario:senha@banco:5432/meubanco
```

Nesse caso, `banco` é o nome do serviço definido no Compose.

---

## 9. Por que, dentro da rede Docker, normalmente usamos o nome do serviço em vez de `localhost`?

Porque `localhost` dentro de um container **se refere ao próprio container**, e não ao computador hospedeiro nem a outro container.

Por exemplo, imagine:

```yaml
services:
  app:
    image: minha-app

  banco:
    image: postgres
```

Se a aplicação dentro do container `app` tentar acessar:

```text
localhost:5432
```

ela estará procurando um banco PostgreSQL **dentro do próprio container `app`**.

Isso não significa que ela está procurando o serviço `banco`.

Para acessar o outro container, devemos utilizar o nome do serviço:

```text
banco:5432
```

O Docker possui um mecanismo de resolução de nomes dentro da rede que permite que `banco` seja associado ao endereço IP correto do container correspondente.

Portanto:

```text
localhost → próprio container
banco      → serviço/container do banco
```

Essa é uma das diferenças mais importantes quando começamos a trabalhar com vários containers.

---

## 10. Para que serve `environment`?

A propriedade `environment` serve para definir **variáveis de ambiente dentro do container**.

Essas variáveis podem ser utilizadas pela aplicação para receber configurações sem precisar alterar o código.

Por exemplo:

```yaml
environment:
  POSTGRES_USER: usuario
  POSTGRES_PASSWORD: senha
  POSTGRES_DB: sistema
```

Nesse exemplo, o container recebe três variáveis:

* `POSTGRES_USER`;
* `POSTGRES_PASSWORD`;
* `POSTGRES_DB`.

Aplicações e imagens Docker podem utilizar essas variáveis para configurar seu funcionamento.

Também é possível utilizar valores provenientes de um arquivo `.env`, o que é especialmente útil para evitar colocar informações sensíveis diretamente no `compose.yml`.

---

## 11. O que significa `depends_on`?

A propriedade `depends_on` é utilizada para indicar que um serviço possui uma **dependência de outro serviço**.

Por exemplo:

```yaml
services:
  app:
    image: minha-app
    depends_on:
      - banco

  banco:
    image: postgres
```

Nesse caso, estamos informando ao Compose que o serviço `app` depende do serviço `banco`.

Isso faz com que o Compose **inicie o serviço `banco` antes do serviço `app`**.

Porém, existe um detalhe importante: `depends_on` controla principalmente a **ordem de inicialização**, mas não significa necessariamente que o serviço dependente já esteja completamente pronto para receber conexões.

Por exemplo, o container do PostgreSQL pode ter sido iniciado, mas o banco ainda pode estar realizando sua inicialização.

Para situações em que precisamos esperar o serviço realmente estar disponível, podemos utilizar um `healthcheck` combinado com condições apropriadas de dependência.

Exemplo:

```yaml
services:
  app:
    image: minha-app
    depends_on:
      banco:
        condition: service_healthy

  banco:
    image: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U usuario"]
      interval: 5s
      timeout: 5s
      retries: 5
```

Nesse caso, o serviço `app` espera o serviço `banco` apresentar um estado considerado saudável antes de ser iniciado.

---

## 12. O que significa `restart: unless-stopped`?

A propriedade `restart` define a **política de reinicialização do container**.

Uma das opções disponíveis é:

```yaml
restart: unless-stopped
```

Isso significa que o Docker tentará reiniciar o container automaticamente caso ele seja encerrado inesperadamente.

Entretanto, se o usuário parar o container manualmente, o Docker não deverá reiniciá-lo automaticamente enquanto ele permanecer parado.

### Principais opções de `restart`

#### `no`

```yaml
restart: "no"
```

É o comportamento padrão.

O container não será reiniciado automaticamente caso seja encerrado.

---

#### `always`

```yaml
restart: always
```

O Docker tenta reiniciar o container sempre que ele for encerrado.

Essa política também pode fazer com que o container seja iniciado novamente quando o daemon do Docker for reiniciado.

---

#### `on-failure`

```yaml
restart: on-failure
```

O container será reiniciado somente quando terminar com um código de erro.

Também podemos definir um número máximo de tentativas:

```yaml
restart: on-failure:5
```

Nesse caso, o Docker tentará reiniciar o container no máximo cinco vezes após falhas.

---

#### `unless-stopped`

```yaml
restart: unless-stopped
```

Funciona de maneira semelhante ao `always`, mas respeita uma parada manual feita pelo usuário.

Por exemplo, se o container estiver configurado com:

```yaml
restart: unless-stopped
```

e parar por causa de uma falha, o Docker pode tentar iniciá-lo novamente.

Por outro lado, se o usuário parar o container manualmente, ele permanecerá parado.

### Resumo

| Política         | Reinicia automaticamente? | Observação                          |
| ---------------- | ------------------------- | ----------------------------------- |
| `no`             | Não                       | Comportamento padrão                |
| `always`         | Sim                       | Reinicia sempre que possível        |
| `on-failure`     | Sim                       | Apenas quando ocorre uma falha      |
| `on-failure:N`   | Sim                       | Limita a quantidade de tentativas   |
| `unless-stopped` | Sim                       | Não reinicia após uma parada manual |

---

## Conclusão

O Docker Compose facilita a execução de aplicações compostas por vários containers porque permite definir toda a infraestrutura necessária em um único arquivo.

Com ele podemos configurar:

* **imagens**, que servem como base para os containers;
* **containers**, que executam os serviços;
* **serviços**, que representam os componentes da aplicação;
* **volumes**, que permitem persistir dados;
* **redes**, que permitem a comunicação entre containers;
* **portas**, que permitem expor serviços para o computador;
* **variáveis de ambiente**, que fornecem configurações aos containers;
* **dependências**, que controlam a ordem de inicialização;
* **políticas de restart**, que determinam como os containers devem ser reiniciados.

A principal vantagem é poder reproduzir o mesmo ambiente de desenvolvimento ou execução de maneira muito mais simples e consistente, evitando aquela clássica situação de "na minha máquina funciona".
