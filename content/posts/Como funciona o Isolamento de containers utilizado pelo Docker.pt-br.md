+++
author = ["Valdir Junior"]
title = "Como funciona o Isolamento de containers utilizado pelo Docker"
date = "2024-01-02"
description = "Entenda como o Docker utiliza recursos do kernel Linux para isolar contêineres, garantindo que aplicações funcionem de forma independente em um ambiente compartilhado."
tags = ["Docker", "Linux", "CGroups", "Namespaces", "Unshare"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQHVJusYLjguHA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1693822466067?e=1758758400&v=beta&t=bPRZ8xzT7u3DhwjV3quxfZMEjyDlriIElSfE-EKA2h0)

O Docker revolucionou a forma como as aplicações sao implantadas e gerenciadas, utilizando o
conceito de contêineres. Um dos pilares fundamentais dessa recurso é o isolamento, que possibilita a
execução de várias aplicações em um único host sem que elas interfiram umas nas outras.

Esse isolamento é alcançado por meio de recursos essenciais do kernel Linux, como Control Groups
(CGroups), Namespaces e o comando Unshare. Neste artigo, vamos ter uma compreensão desses recursos e
como eles são empregados para criar ambientes isolados no Docker.

### Introdução ao Isolamento no Docker

O isolamento é crucial para garantir que as aplicações em contêineres funcionem de maneira
independente em um ambiente compartilhado. Sem um isolamento adequado, as aplicações poderiam entrar
em conflito umas com as outras, tornando a implantação mais complexa e arriscada.

Para compreender o isolamento no Docker, é essencial conhecer três componentes-chave:

- **Control Groups (CGroups)**

Control Groups, ou CGroups, é uma funcionalidade que possibilita o controle e a limitação de
recursos de um processo. O Docker utiliza CGroups para impor limites de CPU, memória, E/S e outros
recursos em contêineres. Isso garante que uma aplicação em um contêiner não monopolize todos os
recursos do sistema, tornando o ambiente mais equilibrado e seguro.

- **Namespaces**

Namespaces fornecem isolamento de recursos do sistema, como processos, sistemas de arquivos e redes.
O Docker utiliza namespaces para criar um ambiente isolado para cada contêiner. Isso significa que
cada contêiner "enxerga" apenas seus próprios processos, sistemas de arquivos e interfaces de rede,
criando a ilusão de que está sendo executado em um sistema separado.

- **Unshare**

O comando Unshare permite criar um novo namespace para um processo existente, possibilitando a
execução de processos em um ambiente isolado, sem a necessidade do Docker ou de outros gerenciadores
de contêineres. O Unshare é uma ferramenta poderosa para experimentação, testes e situações em que
você deseja criar um ambiente de contêiner personalizado.

---

### Executando um Contêiner no Linux

Vamos agora explorar os passos necessários para criar e executar um contêiner no Linux, utizizando
algumas ferramentas e conceitos essenciais, como CGroups, chroot, namespaces, Unshare e debootstrap.

### Conceitos Básicos

1. **_CGroups (Control Groups):_** É fundamental para a gestão de recursos permitindo o controle de
   CPU, memória...
2. **_Chroot:_** Altera o diretório raiz de um processo, permitindo que ele veja apenas um
   subdiretório específico como sua raiz.
3. **_Namespaces:_** Criar instâncias isoladas de recursos do sistema, como PID, rede, sistema de
   arquivos e outros.
4. **_Unshare:_** O comando Unshare é usado para criar namespaces isolados para um processo ou grupo
   de processos.
5. **_Debootstrap:_** O Debootstrap é uma ferramenta que permite criar sistemas de arquivos raiz
   mínimos para distribuições Linux. É comumente usado para criar sistemas de arquivos iniciais de
   contêineres.

---

### Passo 1: Preparar o Sistema de Arquivos Raiz

Utilizaremos o Debootstrap para criar um sistema de arquivos raiz mínimo. Vamos utilizar o Ubuntu
como exemplo:

```zsh
sudo mkdir /container
debootstrap focal /container http://archive.ubuntu.com/ubuntu/
```

### Passo 2: Configurar o Isolamento de Recursos

Vamos usar o comando Unshare para criar namespaces de PID, rede e sistema de arquivos para o
contêiner:

```zsh
unshare --fork --pid --mount-proc --uts --ipc --net --map-root-user
```

**O que essa linha faz:** O comando unshare cria um novo processo com namespaces isolados em várias
áreas do sistema.

1. **--fork:** Esta opção indica que o unshare deve criar um novo processo filho após isolar os
   namespaces. O processo filho será executado sem nenhuma tarefa específica após a criação dos
   namespaces.
2. **--pid:** Isola o namespace PID, o que significa que o processo criado terá seu próprio espaço
   de processo separado dos processos fora do namespace PID.
3. **--mount-proc:** Monta um sistema de arquivos /proc isolado para o processo criado, permitindo
   que ele tenha sua própria visualização do sistema de arquivos /proc.
4. **--uts:** Isola o namespace UTS, que controla o nome do host. Isso permite que o processo criado
   tenha seu próprio nome de host.
5. **--ipc:** Isola o namespace IPC, que controla a comunicação interprocesso. Isso impede que o
   processo criado interaja diretamente com processos fora do namespace IPC.
6. **--net:** Isola o namespace de rede, o que significa que o processo criado terá sua própria
   pilha de rede e configurações de rede separadas.
7. **--map-root-user:** Isso permite que o processo criado seja tratado como root (superusuário)
   dentro do namespace, mesmo que não tenha permissões de root no sistema host.

### Passo 3: Iniciar o Processo no Contêiner

Dentro do ambiente chroot, você pode iniciar os processos desejados. Por exemplo:

```zsh
 chroot /container /bin/bash
```

### Passo 4: Isolar o Uso de Recursos com CGroups

Para limitar o uso de recursos pelo contêiner, você pode criar um grupo de controle (CGroup) para o
processo do contêiner e definir limites usando comandos como cgcreate, cgset e cgexec.

```zsh
# Instale o pacote cgroups para conseguir manipular esses recursos
sudo apt-get install cgroups-tools
```

---

## Aqui estão alguns exemplos de como executar um contêiner utilizando os conceitos mencionados

### Exemplo 1: Criar um Contêiner Simples

Vamos criar um contêiner ubunut mínimo e executar um shell dentro dele:

```zsh
# Preparar o sistema de arquivos raiz
debootstrap focal /container http://archive.ubuntu.com/ubuntu/

# Configurar o isolamento de recursos
unshare --fork --pid --mount-proc --uts --ipc --net --map-root-user chroot /container /bin/bash

# Iniciar o shell no contêiner
/bin/bash
```

### Exemplo 2: Isolar o Uso de CPU com CGroups

Suponha que você deseje limitar o uso de CPU do contêiner a 50%:

```zsh
# Criar um grupo de controle para o contêiner
cgcreate -g cpu:container_group

# Definir o limite de CPU para 50%
cgset -r cpu.cfs_quota_us=50000 container_group

# Executar o processo do contêiner
cgexec -g cpu:container_group /bin/bash
```

## Conclusão

Este artigo explorou de maneira basica e simples os fundamentos da criação e execução de containers
no Linux, utilizando ferramentas como cgroups, chroot, namespaces, unshare e debootstrap. Essas
técnicas permitem isolar processos em ambientes independentes, O que é a base essencial para a
containerização.

No entanto, é importante destacar que a utilização de ferramentas como Docker simplifica
consideravelmente a criação e gerenciamento de containers.

Por baixo dos panos, essas ferramentas combinam essas técnicas com automação, controle de imagem,
redes e volume de dados, oferecendo uma experiência mais fácil e completa para o desenvolvimento e
implantação de aplicações em containers.

O Docker, em particular, utiliza o conjunto de tecnologias mencionadas neste artigo para criar e
gerenciar containers, mas também fornece uma camada de abstração de alto nível que facilita a
criação, compartilhamento e execução de containers. Ele utiliza um sistema de imagens, registros e
comandos específicos para simplificar o processo.

Em resumo, enquanto a compreensão dos princípios fundamentais é valiosa para profissionais de DevOps
e sysadmins, o uso de ferramentas como Docker tornou a adoção de containers muito mais acessível e
produtiva para a maioria dos desenvolvedores e administradores de sistemas.

