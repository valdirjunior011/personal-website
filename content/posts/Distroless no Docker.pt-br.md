+++
author = ["Valdir Junior"]
title = "Distroless no Docker: Segurança e Monitorização"
date = "2024-02-02"
description = "Aprenda como usar imagens distroless no Docker para aumentar a segurança e monitorização de contêineres com healthchecks e reinicialização automática."
tags = ["Docker", "Distroless", "Healthchecks", "Segurança", "Monitorização"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQGwhxdbDGnDjA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1696490979793?e=1758758400&v=beta&t=ImtknOIDkgq-8dIyXQIsdUdtaiwKKM6Cty88-4Ina9M)

## Simplificando a Eficiência das Imagens de Contêiner

### Distroless: O que são e por que são importantes?

As imagens distroless são uma classe especial de imagens Docker que se destacam por sua abordagem
minimalista.

Ao contrário das imagens tradicionais, que incluem um sistema operacional completo, as imagens
distroless contêm apenas o necessário para executar uma aplicação. Isso significa que não há pacotes
ou utilitários desnecessários, o que reduz significativamente a superfície de ataque do containers.

A importância das imagens distroless é dupla. Primeiro, elas diminuem a quantidade de software
potencialmente vulnerável presente no contêiner. Em segundo lugar, tornam os contêineres mais leves
e rápidos de implantar, economizando recursos de hardware e melhorando a escalabilidade.

Para verificar as camadas de uma imagem distroless e garantir sua origem, você pode usar a
ferramenta "dive".

[Dive - Analyze Docker Images (](https://gochronicles.com/dive/)[gochronicles.com](http://gochronicles.com/)[)](https://gochronicles.com/dive/)

Ela permite que você inspecione as camadas de uma imagem e verifique se não há conteúdo não
desejado.

Por exemplo:

```zsh
dive gcr.io/distroless/base-debian10
```

---

### Healthchecks: Mantendo a Saúde do Contêiner

Agora que sabemos como tornar nossos contêineres mais seguros com imagens distroless, é hora de
adicionar uma camada adicional de robustez: healthchecks. Um healthcheck é uma função que verifica
se o contêiner está em um estado saudável e funcional. Isso é fundamental para a monitorização e
garantia da disponibilidade contínua de sua aplicação.

Os healthchecks podem ser implementados diretamente no Dockerfile. Vamos dar uma olhada em como
fazer isso:

```Dockerfile
# Use uma imagem distroless como base
FROM gcr.io/distroless/base-debian10

# Copie os arquivos da sua aplicação para o contêiner
COPY minha_app /minha_app

# Defina a porta em que sua aplicação está ouvindo
EXPOSE 8080

# Adicione um healthcheck
HEALTHCHECK --interval=5s --timeout=3s \
  CMD curl -f http://localhost:8080/health || exit 1

# Comando para iniciar sua aplicação
CMD ["/minha_app"]
```

Neste exemplo, usamos o comando _HEALTHCHECK_ para definir uma verificação de saúde que executa o
comando curl para verificar se a rota /health de nossa aplicação está respondendo corretamente. Se a
verificação de saúde falhar, o contêiner será considerado em um estado não saudável e pode ser
reiniciado automaticamente.

---

### Reinicialização Automática com Healthchecks

A reinicialização automática é um dos benefícios dos healthchecks. Quando você utiliza
orquestradores de contêineres, como Kubernetes, Docker Compose ou Swarm, eles podem monitorar o
status dos healthchecks e tomar medidas automáticas quando um contêiner se torna não saudável. Por
exemplo, no Kubernetes, você pode especificar a política de reinicialização para seus pods:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: meu-app
spec:
  containers:
    - name: meu-app-container
      image: minha-imagem:tag
      ports:
        - containerPort: 8080
      livenessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 5
```

Neste exemplo, o Kubernetes monitora a rota /health do contêiner e reinicializa automaticamente o
pod se a verificação de saúde falhar. Isso garante que sua aplicação esteja sempre em execução e
saudável.

Refer. Documentação Kubernetes.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQEya34_r8_tAQ/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1696491718381?e=1758758400&v=beta&t=K4LJm_aPtT5kovSuXgS9yAZuYR1H-UURoVtD46GcQd0)

Configure Liveness, Readiness and Startup Probes | Kubernetes

---

### Conclusão

A segurança e a monitorização são elementos cruciais para o sucesso de contêineres em ambientes de
produção. A utilização de imagens distroless, a implementação de healthchecks e a personalização
adequada de seu Dockerfile ou yaml no Kubernetes são práticas recomendadas para alcançar esses
objetivos. Ao minimizar a superfície de ataque, manter o controle sobre a saúde de seus contêineres
e aproveitar as vantagens da reinicialização automática, você está dando passos importantes em
direção a uma arquitetura de contêiner mais segura e confiável. Lembre-se sempre de adaptar essas
práticas às necessidades específicas de sua aplicação e ambiente.
