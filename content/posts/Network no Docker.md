![](https://media.licdn.com/dms/image/v2/D4E12AQFcvRWeihIBbg/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1694442094516?e=1758758400&v=beta&t=FHEiicoOMbEloPH5RMlnSD8_vAUMSe_ILdJP2PUn4yk)

**Fundamentos da Rede Docker**

Antes de mergulharmos nos tópicos mais complexos, é importante entender os conceitos fundamentais da rede no Docker.

- **Bridge Network**

Uma **bridge network** é a rede padrão criada quando você instala o Docker. Ela permite que os contêineres se comuniquem uns com os outros na mesma rede usando nomes de host.

Vamos criar uma rede bridge chamada "my_network" para demonstrar como funciona:

```zsh
docker network create my_network
```

Agora, vamos executar dois containers nessa rede:

```zsh
docker run --name container1 --network my_network -d nginx
docker run --name container2 --network my_network -d nginx
```

Os containers **_container1_** e **_container2_** podem se comunicar usando os nomes de host **_container1_** e **_container2_**.

- **Port Forwarding**

O Docker permite que você encaminhe portas de contêineres para o host. Isso é útil quando você deseja acessar serviços dentro de um contêiner diretamente do seu sistema host.

Por exemplo, para encaminhar a porta 8080 do contêiner para a porta 80 do host:

```zsh
docker run -d -p 8080:80 nginx
```

> [!tip] Agora, você pode acessar o conteúdo do contêiner Nginx em http://localhost:8080 no seu navegador.

- **Redes Personalizadas**

As redes bridge são úteis, mas você também pode criar redes personalizadas para isolamento e maior controle. Por exemplo, você pode criar uma rede overlay para comunicação entre contêineres em diferentes hosts.

```zsh
docker network create --driver overlay my_overlay_network
```

> [!warning] You need the following ports open to traffic to and from each Docker host participating on an overlay network: TCP port 2377 for cluster management communications TCP and UDP port 7946 for communication among nodes UDP port 4789 for overlay network traffic

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQGvUz-_HmErbQ/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1694512271901?e=1758758400&v=beta&t=hd8vawzYXp6TtLjoTTb1D_J1NheS2RYhgLi0p9we4fQ)

https://docs.docker.com/network/drivers/overlay/

---

### Anexar e Desanexar Containers

Para adicionar contêineres a uma rede existente, use o comando docker network connect:

```zsh
docker network connect my_network container3
```

Para remover um contêiner de uma rede, use o comando docker network disconnect:

```zsh
docker network disconnect my_network container3
```

---

### IPvlan, Macvlan e None Network

Além das redes bridge, overlay e personalizadas, o Docker oferece recursos avançados de rede que podem ser úteis em cenários mais complexos. Vamos explorar um pouco esses três recursos: **IPvlan**, **Macvlan** e **None Network**.

- **IPvlan Network**

A rede IPvlan permite que você atribua endereços IP individuais a cada contêiner, tornando-os acessíveis diretamente na rede externa. Isso é especialmente útil quando você deseja evitar a camada de encapsulamento da rede Docker e assim obter um melhor desempenho.

Exemplo de Uso do IPvlan

Primeiro, crie uma rede IPvlan:

```zsh
docker network create -d ipvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 my_ipvlan_network
```

Agora, inicie um contêiner com um IP específico dentro dessa rede:

```zsh
docker run --name container_ipvlan --network my_ipvlan_network --ip=192.168.1.2 -d nginx
```

O contêiner **_container_ipvlan_** agora tem um endereço IP válido na rede externa.

- **Macvlan Network**

A rede Macvlan é semelhante à IPvlan, mas permite que cada contêiner tenha seu próprio endereço MAC. Isso é útil quando você precisa de isolamento de camada de link e um endereço MAC exclusivo para cada contêiner.

Exemplo de Uso do Macvlan

Crie uma rede Macvlan:

```zsh
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 my_macvlan_network
```

Inicie um contêiner na rede Macvlan:

```zsh
docker run --name container_macvlan --network my_macvlan_network -d nginx
```

Agora, o contêiner **_container_macvlan_** tem seu próprio endereço MAC e IP na rede externa.

- **None Network**

A rede None é uma rede que não fornece conectividade de rede externa para um contêiner somente a loopback é criada. Isso pode ser útil quando você deseja que um contêiner tenha sua rede desativada completamente, mas ainda esteja em execução.

Exemplo de Uso da None Network

Crie um contêiner com a rede None:

```zsh
docker run --name container_none --network none -d nginx
```

O contêiner **_container_none_** não terá conectividade de rede externa, mas ainda pode ser usado para tarefas internas.

---

### Network Scopes

Os contêineres têm diferentes níveis de isolamento de rede. Eles podem estar em um escopo de rede global ou local. A rede bridge é um exemplo de escopo local, onde os contêineres podem se comunicar apenas na mesma máquina. As redes overlay são globais e permitem a comunicação entre hosts.

### Comunicação entre Hosts

Quando você tem contêineres em diferentes hosts, pode usar redes overlay para conectar os contêineres entre si. Para fazer isso, configure um serviço de descoberta de chave-valor como o Consul ou o etcd para que os contêineres possam se registrar e se descobrir em diferentes hosts.

### Conclusão

A rede no Docker é uma parte fundamental da orquestração de contêineres e da criação de aplicativos distribuídos. Neste artigo, exploramos alguns conceitos, como redes personalizadas, brigde, overlay, ipvlan, macvlan, none, além de fornecer exemplos básicos para ilustrar esses conceitos. Certifique-se de explorar a documentação do Docker [https://docs.docker.com/network/](https://docs.docker.com/network/) e experimentar os conceitos discutidos neste artigo em seu próprio ambiente de desenvolvimento para obter uma compreensão completa desses tópicos.