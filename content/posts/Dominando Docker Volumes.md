![](https://media.licdn.com/dms/image/v2/D4E12AQEerXYc8JGnFA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1694435515394?e=1758758400&v=beta&t=UxGuBTGGWEnDDex9-bjyIXHAQMHQx4tLUMqzjdeY4Us)

Uma das características fundamentais que fazem do Docker uma escolha popular é a capacidade de persistir dados entre execuções de contêineres. Isso é possível graças ao uso de volumes Docker. Neste artigo, exploraremos o conceito de Docker volumes de forma abrangente.

---

**O que são Docker Volumes?**

Em termos simples, Docker volumes são mecanismos que permitem que dados sejam compartilhados e persistidos entre contêineres e o host em que o Docker está sendo executado. Eles são especialmente úteis quando se trata de armazenar dados, como bancos de dados, logs ou qualquer outro tipo de informações que precisa "sobreviver" além do ciclo de vida de um contêiner.

Docker volumes oferecem várias vantagens, como:

1. **Persistência de Dados:** Os dados armazenados em volumes não são perdidos quando um contêiner é interrompido ou excluído.

2. **Compartilhamento de Dados:** Volumes podem ser compartilhados entre vários contêineres, tornando possível que vários contêineres acessem os mesmos dados.

3. **Desempenho Aprimorado:** Em comparação com a cópia de dados diretamente para dentro e para fora dos contêineres, o uso de volumes pode melhorar significativamente o desempenho de E/S.

4. **Backup e Restauração Simples:** Como os dados estão isolados nos volumes, fazer backup e restaurar torna-se uma tarefa mais simples.

---

Agora que sabemos por que os volumes são importantes, vamos dar uma olhada em como usá-los.

## Usando Docker Volumes

### Tipos de Montagem

- **Montagem de Volumes de Host (Bind Mounts):** Os volumes de host, ou bind mounts, permitem montar diretórios ou arquivos do sistema de arquivos do host diretamente para dentro do contêiner. Isso significa que você pode compartilhar dados específicos do host com um contêiner. Para criar um bind mount, você usa a opção -v ou --volume seguida do caminho do host e do caminho do contêiner.

```zsh
docker run -v /caminho/no/host:/caminho/no/contêiner meu_aplicativo
```

> Isso montará o diretório /caminho/no/host do host dentro do contêiner no caminho /caminho/no/contêiner.

- **Volumes de Contêiner (Container Volumes):** Os volumes de contêiner são volumes específicos do Docker que são gerenciados pelo Docker Daemon. Eles são criados automaticamente e são geralmente mais flexíveis em relação a onde e como os dados são armazenados no host. Para criar um volume de contêiner, você pode usar_:_

```zsh
 docker volume create meu_volume
```

Isso criará um volume chamado "meu_volume" que pode ser montado em um ou mais contêineres.

---

### Montando um Volume em um Contêiner

Agora, vamos criar um contêiner e montar nosso volume nele. Suponha que você tenha um aplicativo que precisa armazenar dados em um volume.

```zsh
# -d para rodar em background
docker run -d -v meu_volume:/app/data meu_aplicativo
```

Neste comando, estamos criando um novo contêiner a partir da imagem "meu_aplicativo" e montando o volume "meu_volume" em "/app/data" dentro do contêiner. Qualquer dado escrito em "/app/data" dentro do contêiner será armazenado no volume "meu_volume" no host.

**Compartilhando Volumes entre Contêineres**

Um dos benefícios dos volumes é a capacidade de compartilhá-los entre contêineres. Isso é útil quando você deseja que vários contêineres acessem os mesmos dados. Para fazer isso, você pode simplesmente montar o mesmo volume em diferentes contêineres.

```zsh
docker run -d -v meu_volume:/app/data contêiner_1
docker run -d -v meu_volume:/app/data contêiner_2
```

Agora, "contêiner_1" e "contêiner_2" podem acessar e modificar os mesmos dados armazenados no volume "meu_volume".

> [!info] Como não especificamos as permissões, por default todos tem direito rw (ler e escrever)

**Visualizando Volumes**

Para listar todos os volumes Docker no seu sistema, você pode usar o comando:

```zsh
docker volume ls
```

Para inspecionar um volume específico, use:

```zsh
docker volume inspect meu_volume
```

---

### Exemplo de como utilizar o Docker Volume

**Exemplo1: Armazenar Dados em um Banco de Dados**

Vamos executar um contêiner de banco de dados MySQL. E criar um volume para armazenar os dados do banco de dados da seguinte forma:

```zsh
docker volume create banco_de_dados
docker run -d -v banco_de_dados:/var/lib/mysql mysql:latest
```

Isso garantirá que os dados do banco de dados sejam persistidos no volume "banco_de_dados".

**Exemplo 2: Compartilhar Logs**

Agora você tem um aplicativo web e um servidor de logs em contêineres diferentes. Você pode criar um volume para compartilhar os logs entre os dois contêineres:

```zsh
docker volume create logs
docker run -d -v logs:/app/logs meu_aplicativo_web
docker run -d -v logs:/app/logs servidor_de_logs
```

Agora, ambos os contêineres podem acessar os mesmos logs no volume "logs".

---

### Conclusão

Os Docker volumes são uma ferramenta essencial para gerenciar dados de forma eficiente e persistente em contêineres. Eles permitem que você persista dados, compartilhe informações entre contêineres e melhore o desempenho de E/S. Com os conceitos e exemplos apresentados neste artigo, você ja esta pronto para começar a usar volumes Docker em seus próprios projetos. Explore mais e experimente diferentes casos de uso para aprimorar suas habilidades com Docker.

Documentação oficial [https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)