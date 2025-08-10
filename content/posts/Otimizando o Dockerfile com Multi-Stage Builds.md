
![](https://media.licdn.com/dms/image/v2/D4E12AQFiVMmK1xCdPA/article-cover_image-shrink_600_2000/article-cover_image-shrink_600_2000/0/1693653766393?e=1758758400&v=beta&t=Bn7TSy9VPWrDtZ0sT9O2sPazDNEfMBP7Kwz8AT67IKw)

Um dos desafios ao trabalhar com Docker é criar imagens eficientes em termos de tamanho e, ao mesmo tempo, garantir a segurança do conteúdo dessas imagens. Este artigo mostrará uma opção como otimizar o Dockerfile usando Multi-Stage Builds e como a ferramenta Chainguard pode ser usada para garantir a segurança das imagens Docker.

---

- **Otimização com Multi-Stage Builds**

Multi-Stage Builds é uma técnica avançada no Docker que permite criar imagens mais eficientes e menores em tamanho. A ideia básica é dividir a construção da imagem em várias etapas, chamadas de "estágios", cada uma com seu próprio ambiente de construção. Isso é particularmente útil quando você precisa compilar código-fonte ou realizar outras tarefas complexas durante a construção da imagem.

- **Como Funcionam as Camadas**

Antes de explorarmos o Multi-Stage Builds, é importante entender como funcionam as camadas em um Dockerfile. Cada instrução em um Dockerfile cria uma nova camada na imagem. As camadas são armazenadas em cache, o que significa que, se você não alterar uma instrução em um Dockerfile, o Docker reutilizará a camada armazenada em cache, economizando tempo e espaço em disco.

No entanto, cada camada adiciona tamanho à imagem final. Isso é importante porque imagens grandes podem ser problemáticas em ambientes com recursos limitados e podem tornar a distribuição mais lenta.

---

- **Exemplo de Multi-Stage Build**

Um exemplo simples de Multi-Stage Build para uma aplicação Node.js:

```zsh
# Estágio 1: Construir a aplicação
FROM node:14 as builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Estágio 2: Criar uma imagem final menor
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Neste exemplo:

1. **Estágio 1:** constrói a aplicação Node.js e cria uma imagem temporária com todas as dependências de construção.
2. **Estágio 2:** criamos uma imagem final menor usando nginx:alpine e copiamos apenas o resultado da compilação da aplicação, economizando espaço.

Então basicamente temos duas diferenças:

1. **Sem Multi-Stage Builds:** Uma imagem que inclui todas as dependências de compilação e artefatos de construção pode facilmente crescer para vários gigabytes, tornando-a pesada e demorada para distribuir.
2. **Com Multi-Stage Builds:** O uso de estágios intermediários resulta em uma imagem final muito menor, contendo apenas o que é necessário para executar a aplicação. Isso pode reduzir o tamanho da imagem final para dezenas de megabytes.

---

- **Imagens base Seguras com Chainguard**

Agora que sabemos como otimizar nossos Dockerfiles e vamos sempre abordar a questão da segurança das imagens Docker e desta vez com Chainguard. A Chainguard Images é uma extensão poderosa do Chainguard que oferece maior eficaz a segurança das imagens.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQEuRTRHiypjig/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1693673715207?e=1758758400&v=beta&t=Gmbjzj2PAF4OsnV4QyeHwM6VpCnoxuA4-6RQ8OpJBls)

https://www.chainguard.dev/chainguard-images

- **Chainguard Images**

As imagens Chainguard contêm apenas o que é necessário para construir ou executar seu aplicativo, proporcionando, em média, uma redução de 80% no tamanho e menos CVEs.

1. **Imagens geradas diariamente:** Garantia que as imagens estejam atualizadas e incluam patches de segurança disponíveis.
2. **Assinado por Sigstore/Cosign:** Imagens assinada criptograficamente para prova de origem e garantias anti-adulteração.
3. **SBOM:** SBOMs zerados atestando a procedência de todos os artefatos.

---

- **Utilizando Docker History para Ver Camadas**

Como mencionamos camadas anteriormente um comando excelente que temos para ver as camadas de uma imagem Docker, é o comando **_docker history_**.

como utilizar:

```zsh
docker history minha-imagem:latest
```

Isso exibirá uma lista de todas as camadas que compõem a imagem, juntamente com seus IDs, tamanhos e instruções correspondentes no Dockerfile.

> [!tldr] O comando docker history é útil para entender como uma imagem foi construída, quais comandos foram executados em cada camada e o tamanho de cada camada. Isso pode ser útil para otimizar o tamanho das imagens Docker e entender como elas foram criadas.

### Conclusão

O uso de Multi-Stage Builds no Dockerfile permite criar imagens menores e mais eficientes, reduzindo significativamente o tamanho das imagens finais. Isso é crucial para a eficiência na distribuição e implantação de contêineres. Além disso, utilizando imagens da Chainguard ajuda a garantir a segurança das imagens Docker, verificando a origem e a integridade de cada camada.

Ao combinar essas práticas, você pode criar imagens Docker mais seguras e eficientes, melhorando tanto o desempenho quanto a confiabilidade de suas aplicações contêinerizadas.
