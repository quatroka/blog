---
layout:     post
title:      "Docker #02 - Primeiro container"
subtitle:   "Entendendo um pouco sobre a baleia azul."
date:       2016-02-21 00:22:00
author:     "Leonardo S. Monteiro"
header-img: "/i.imgur.com/pBImEY3.jpg"
comments : true
permalink: "4"
---

Possuindo o docker instalado, agora podemos criar e executar nossos primeiros
 containers! Nesse post criaremos um container com Fedora. _Here we go_.

No ultimo post utilizamos o comando _docker info_, na saída nos mostrava que já
 possuíamos uma image. Vamos consultar as docker images que possuímos:

    docker images

![](http://i.imgur.com/B8ZZ37u.jpg)

Eles nos retornou a image _hello-world_, a image que baixamos para executar o
 comando `docker run hello-world`.

---
Sintaxe básica:

    docker images [parâmetros] [repositório[:tag]]

Parâmetros do comando _docker images_:

| Parâmetros          | Ação                                                       |
|---------------------|:-----------------------------------------------------------|
| `-a`, `--all`       | Mostra todas as Docker Images incluindo as intermediárias. |
| `--digests`         | Mostra no resultado um espécie de chave de conteúdo, na qual representa em forma de dígitos o conteúdo da image, se a imagem for alterada, o _digest_ também será alterado. |
| `-f`, `--filter=[]` | Parâmetro que permite adicionar filtro ao resultado.       |
| `--no-trunc`        | Faz o resultado não ser truncado(abreviado).               |
| `-q`, `--quiet`     | Mostra somente os IDs das images.                          |

---

Para realizar a criação do container precisamos antes de uma image em nosso
 sistema, para isso usaremos o comando _pull_:

{% highlight bash %}
docker pull fedora:23
{% endhighlight %}

![](http://i.imgur.com/rPwdYJ3.jpg)

    fedora ->  Nome da imagem escolhida
    :      ->  Separador de argumentos
    23     ->  Número da versão

Obs.: Como imagens são constantemente atualizadas, as imagens geralmente possuem
 versões como `stable`, `unstable`, `latest`, sendo essa última a escolhida
 quando não se coloca o numero da versão.

---
Sintaxe básica:

    docker pull [parâmetros] [nomeDaImagem[:tag|@digest]]

Parâmetros do comando _docker pull_:

| Parâmetros                     | Ação                                                                       |
|--------------------------------|----------------------------------------------------------------------------|
| `-a`, `--all-tags`             | Realiza o download de todas as images com tags no repositório              |
| `--disable-content-trust=true` | Pula a verificação da integridade da image após o download. (Não use isto) |

---

Se executarmos o comando _docker images_ veremos que a image _fedora:23_ será
 listada também. Agora criaremos nosso primeiro container usando a imagem baixada:

{% highlight bash %}
docker create -i -t fedora:23 /bin/bash
{% endhighlight %}

![](http://i.imgur.com/p7zzv3a.jpg)

    -i, -t    -> parâmetros
    fedora:23 -> nome e versão da image que será utilizada na criação do container
    /bin/bash -> comando que será executado quando o container executar

---
Sintaxe básica:

    docker create [parâmetros] [nomeDaImagem] [comando]

Parâmetros do comando _docker pull_:

| Parâmetro | Ação                                                                         |
|-----------|------------------------------------------------------------------------------|
| `-t`      | Cria uma emulação do terminal do container para terminal do host.            |
| `-i`      | O docker mantem aberto a captura das saídas(output) originadas do container. |

Obs.: Existem mais parâmetros do comando _docker create_, porém a sua abordagem
 será feita posteriormente.

**ADENDO**:_Para que fique melhor entender como os parâmetros `-i -t`, imagine
o seguinte: `-t` nos possibilita entrar no terminal do container, porém sem ver
 nada, e o `-i` nos possibilita ver o que está acontecendo. Entenderemos melhor
 quando trabalharmos dentro do container._

---

Pronto! Ele retornou o ID do nosso container criado! Agora vamos verificar os
 containers disponíveis:

    docker ps

![](http://i.imgur.com/r2W03dK.jpg)

Ficou confuso agora certo? Nenhum sinal de um _Fedora_ ou o ID que recebemos
 anteriormente. O comando _docker ps_ retorna somente os containers que estão
 iniciados. Para visualizar TODOS os containers, use `-a`:

    docker ps -a

![](http://i.imgur.com/zvPFp2Y.jpg)

Agora sim! Aí está o container que criamos. Ele nos retorna os seguintes resultados
 respectivamente: ID do container, image utilizada, o comando que vai/está sendo
 executado, quando foi criado, o status dele(Se está parado ou ativo), as portas
 TCP/IP que ele está utilizado e por fim o nome do container.

---
Sintaxe básica:

    docker ps [parâmetros]

Parâmetros do comando _docker ps_:

| Parâmetro           | Ação                                                                   |
|---------------------|------------------------------------------------------------------------|
| `-a`, `--all`       | Inclui todos os containers nos resultados.                             |
| `-f`, `--filter=[]` | Parâmetro que permite adicionar filtro ao resultado.                   |
| `--format`          | Permite definir uma formatação dos resultados através de um modelo GO. |
| `-l`, `--latest`    | Exibe o container mais recente.                                        |
| `-n=`               | Define a quantidade de containers será exibido no resultado.           |
| `--no-trunc`        | Faz o resultado não ser truncado(abreviado).                           |
| `-q`, `--quiet`     | Exibe somente os IDs dos containers.                                   |
| `-s`, `--size`      | Exibe o tamanho em disco dos containers.                               |

---

Vamos iniciar o nosso container utilizando o nome ou ID do container:

    docker start 7c11804a8879

![](http://i.imgur.com/ms6QLI3.jpg)

Para verificar se o docker está executando mesmo:

    docker ps

![](http://i.imgur.com/A2fucnV.jpg)

As alterações no container deixaremos para o próximo post, pois mexeremos com
 muitas outras coisas, dentre elas as portas TCP/IP dos containers para que assim
 possamos ter acessos a serviços que instalaremos nele. Vamos parar ele:

    docker stop 7c11804a8879

![](http://i.imgur.com/Os3PW3j.jpg)

---
Sintaxe básica:

    docker stop [parâmetros] [nomeDoContainer/ID]

Parâmetros do comando _docker stop_:

| Parâmetro           | Ação                                           |
|---------------------|------------------------------------------------|
| `-t`, `--time=`     | Segundos de espera antes de matar o container. |

#### Mega Adendo

Existe uma forma de agilizar tudo o que fizemos com o comando _docker run_, na
 qual explicarei melhor no próximo post junto com o comando _docker create_. Ele
 verifica se existe a image solicitada no sistema, se não houver ele realiza o
 download, faz a criação do container e logo em seguida o inicia. Foi o que
 executamos quando utilizamos o comando _docker run hello-world_.Exemplo utilizando
 a imagem do Ubuntu 15.04:

![](http://i.imgur.com/3QfB7ox.jpg)

:)

---
Links:

>[Docker #01 - O que é e instalação](http://quatroka.github.io/2/)
>
>[Docker Hub](https://hub.docker.com/)
>
>[Documentação do Docker](https://docs.docker.com/)
