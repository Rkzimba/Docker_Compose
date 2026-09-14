Docker Compose — Conceitos e Configuração

1. O que é o Docker Compose?

O Docker Compose é uma ferramenta utilizada para definir e executar aplicações que possuem vários containers. Em vez de configurar cada container manualmente, é possível descrever a estrutura da aplicação em um único arquivo YAML, normalmente chamado compose.yml ou docker-compose.yml.

Nesse arquivo podem ser definidos os serviços, imagens, portas, volumes, redes, variáveis de ambiente e outras configurações da aplicação. Depois, o Compose pode criar e iniciar toda essa estrutura com um único comando.

2. Qual é a finalidade de um arquivo compose.yml?

O arquivo compose.yml serve para descrever como os containers de uma aplicação devem ser criados e executados.

Nele podem ser definidos os serviços da aplicação, as imagens utilizadas, as portas disponibilizadas, os volumes, as redes, as variáveis de ambiente, as dependências entre os serviços e as políticas de reinicialização dos containers.

Dessa forma, a configuração necessária para executar a aplicação fica centralizada em um único arquivo.

3. Qual a diferença entre imagem, container, serviço, volume e rede?

Imagem

A imagem é um modelo utilizado para criar containers. Ela contém os arquivos, dependências, bibliotecas e configurações necessárias para executar determinado software.

Container

O container é uma instância em execução de uma imagem. Ele possui um ambiente isolado, mas utiliza o kernel do sistema operacional hospedeiro.

Serviço

O serviço representa uma aplicação ou componente que será executado dentro do ambiente definido pelo Compose. Cada serviço pode resultar em um ou mais containers, dependendo da configuração e da quantidade de réplicas.

Volume

O volume é utilizado para armazenar dados de forma persistente fora do ciclo de vida do container. Isso permite que os dados continuem existindo mesmo quando o container é removido ou recriado.

Rede

A rede Docker permite que os containers se comuniquem entre si de maneira isolada. No Docker Compose, os serviços normalmente são conectados automaticamente a uma rede criada pelo próprio Compose. Assim, um serviço pode acessar outro utilizando o nome do serviço como endereço.

4. Para que serve a propriedade services?

A propriedade services é utilizada para definir os serviços que fazem parte da aplicação.

Dentro de cada serviço podem ser configuradas informações como imagem, portas, volumes, variáveis de ambiente, dependências e redes.

5. O que significa a configuração de portas?

A configuração de portas cria um mapeamento entre uma porta do computador e uma porta do container.

Quando uma porta do container é associada a uma porta do computador, um serviço executado dentro do container pode ser acessado pelo computador por meio da porta definida no host.

6. Qual a diferença entre ports e expose?

A principal diferença está na disponibilidade da porta fora da rede Docker.

A propriedade ports publica uma porta do container no computador hospedeiro, permitindo que o serviço seja acessado externamente.

A propriedade expose disponibiliza ou informa uma porta para comunicação dentro das redes Docker associadas ao serviço, sem publicá-la diretamente para o computador hospedeiro.

Portanto, quando é necessário acessar um serviço pelo navegador ou por outra aplicação no computador, normalmente utiliza-se ports.

7. Como o Docker Compose cria uma rede entre os serviços?

Quando uma aplicação é executada através do Docker Compose, normalmente é criada automaticamente uma rede padrão para o projeto. Os serviços definidos no arquivo Compose são conectados a essa rede.

Isso permite que os containers se comuniquem diretamente sem a necessidade de criar manualmente uma rede para cada aplicação. Também é possível criar redes personalizadas quando uma configuração específica é necessária.

8. Como um container consegue acessar outro container?

Quando dois containers estão na mesma rede Docker, eles conseguem se comunicar por meio da rede interna do Docker.

No Docker Compose, normalmente é possível utilizar o nome do serviço como endereço. Dessa forma, uma aplicação pode acessar outro serviço utilizando o nome definido para ele no arquivo Compose.

9. Por que, dentro da rede Docker, normalmente usamos o nome do serviço em vez de localhost?

Porque localhost dentro de um container se refere ao próprio container, e não ao computador hospedeiro ou a outro container.

Por isso, quando uma aplicação precisa acessar outro serviço que está em um container diferente, deve utilizar o nome do serviço correspondente.

O Docker possui um mecanismo de resolução de nomes dentro da rede que permite associar o nome do serviço ao endereço correto do container.

10. Para que serve environment?

A propriedade environment serve para definir variáveis de ambiente dentro do container.

Essas variáveis podem ser utilizadas pela aplicação para receber configurações sem que seja necessário alterar o código. Também é possível utilizar valores provenientes de um arquivo .env, o que pode ser útil para evitar informações sensíveis diretamente no arquivo compose.yml.

11. O que significa depends_on?

A propriedade depends_on é utilizada para indicar que um serviço possui dependência de outro serviço.

Ela controla principalmente a ordem de inicialização dos serviços. Isso significa que um serviço dependente pode ser iniciado depois do serviço do qual depende.

Entretanto, depends_on não significa necessariamente que o serviço já esteja completamente pronto para receber conexões. Por exemplo, um banco de dados pode ter iniciado o container, mas ainda estar realizando sua inicialização.

Quando é necessário aguardar que o serviço esteja realmente disponível, pode-se utilizar um healthcheck combinado com condições apropriadas de dependência.

12. O que significa restart: unless-stopped?

A propriedade restart define a política de reinicialização do container.

A opção unless-stopped faz com que o Docker tente reiniciar automaticamente o container caso ele seja encerrado inesperadamente. Porém, se o usuário parar o container manualmente, ele permanecerá parado.

Principais opções de restart:

no: é o comportamento padrão e não reinicia automaticamente o container.

always: tenta reiniciar o container sempre que ele for encerrado e também pode fazer com que ele seja iniciado novamente quando o daemon do Docker for reiniciado.

on-failure: reinicia o container somente quando ele termina com um código de erro. Também é possível definir um número máximo de tentativas.

unless-stopped: reinicia o container após falhas, mas respeita uma parada manual feita pelo usuário.

Conclusão

O Docker Compose facilita a execução de aplicações compostas por vários containers porque permite definir a infraestrutura necessária em um único arquivo.

Com ele é possível configurar imagens, containers, serviços, volumes, redes, portas, variáveis de ambiente, dependências e políticas de reinicialização.

A principal vantagem é permitir a reprodução do mesmo ambiente de desenvolvimento ou execução de maneira mais simples e consistente.

Dificuldades Encontradas e Soluções

A principal dificuldade encontrada durante o desenvolvimento foi estabelecer corretamente a comunicação entre o Front-end e o Back-end utilizando o Docker Compose. Inicialmente, foi utilizado o endereço localhost para realizar as requisições. Entretanto, verificou-se que, dentro da rede criada pelo Docker, o Front-end não deveria acessar o Back-end dessa forma, sendo necessário utilizar o nome do serviço definido no docker-compose.yml.

Também foram encontradas dificuldades relacionadas à configuração do Nginx e ao uso da variável de ambiente API_URL. Após realizar os ajustes necessários nessas configurações, o Front-end passou a conseguir realizar as requisições ao Back-end corretamente.

Durante a fase de testes, também foram identificados alguns problemas menores, como erros relacionados ao CORS e a exibição duplicada de alguns alunos. Esses problemas foram analisados e corrigidos ao longo do desenvolvimento, contribuindo para o funcionamento adequado da aplicação.
