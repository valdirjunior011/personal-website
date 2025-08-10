![](https://media.licdn.com/dms/image/v2/D4E12AQFgNMFMfdYHnA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1693557973466?e=1758758400&v=beta&t=7h4EVwvYmt7k-1ArSh7o1IUs3rYhN73Eqeb-BfSyfgU)

A segurança das imagens Docker é uma preocupação crucial para qualquer equipe de TI. Garantir a autenticidade e integridade das imagens Docker se tornou uma prioridade inegável. As imagens Docker são os blocos de construção fundamentais para a implantação de aplicativos em contêineres, e qualquer comprometimento de sua segurança pode resultar em sérias consequências, desde violações de dados até interrupções de serviços críticos.

Neste artigo, vamos verificar como podemos utilizar soluções para proteger sua infraestrutura. Uma dessas soluções é o Cosign, uma ferramenta de segurança de imagens Docker que se tornou uma parte essencial do arsenal de qualquer administrador de contêineres para dar autenticidade a suas imagens.

Com o Cosign você garanti que suas imagens Docker sejam autênticas, e que não foram adulteradas.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQEphSCuFt6o3w/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1693560241827?e=1758758400&v=beta&t=kXQDGI9bhucEbQsRqr6y5GfObMJSnqIylp2nKAsvnY4)

Signing Containers - Sigstore

Além disso, exploraremos como complementar a segurança com o Trivy, uma ferramenta de verificação de vulnerabilidades, para criar uma abordagem completa de segurança de imagens Docker.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQHwGPoxwItGLQ/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1693560312187?e=1758758400&v=beta&t=DcMGSYCsHFjTh1w1JpEjAxln7U0h6QN5ANrQsvjH-jI)

Trivy Home - Trivy

---

- **_Entendendo a Segurança em Imagens Docker_**

A segurança de imagens Docker é fundamental em ambientes de contêineres porque imagens corrompidas ou comprometidas podem resultar em consequências extremamente graves, ameaçando a estabilidade e a integridade de sistemas inteiros. As ameaças que pairam sobre imagens Docker são variadas e abrangentes.

1. **Imagens Maliciosas**: Uma das ameaças mais comuns é a possibilidade de imagens Docker maliciosas. Isso ocorre quando uma imagem foi projetada para explorar vulnerabilidades ou abrir portas para ataques. Essas imagens podem ser distribuídas de maneira enganosa, muitas vezes disfarçadas como recursos legítimos ou populares.
2. **Vulnerabilidades Não Corrigidas**: Outro grande risco reside nas vulnerabilidades não corrigidas presentes nas imagens Docker. Uma imagem pode conter componentes desatualizados ou vulnerabilidades conhecidas que podem ser exploradas por atacantes para obter acesso não autorizado ou comprometer a integridade do sistema. A falta de gerenciamento de patches e atualizações regulares torna a infraestrutura vulnerável a esses tipos de ameaças.
3. **Ataques de Supply Chain**: Os ataques de supply chain têm se tornado uma preocupação crescente. Nesses casos, os atacantes comprometem o processo de construção e distribuição de imagens Docker legítimas.

A compreensão dessas ameaças é essencial pois a segurança de imagens Docker não é apenas uma preocupação técnica, mas também uma questão crítica de proteção de dados e continuidade dos negócios. É por isso que ferramentas como o Cosign e o Trivy desempenham um papel crucial na mitigação dessas ameaças.

---

- **_O que é o Cosign?_**

O Cosign é uma ferramenta de assinatura digital que fornece autenticação e verificação de imagens Docker. Ele permite que você assine imagens com chaves de segurança, garantindo que apenas imagens autenticadas e confiáveis sejam implantadas em seu ambiente.

- **_Benefícios do Uso do Cosign_**

Ao adotar o Cosign, você pode garantir que suas imagens Docker sejam provenientes de fontes confiáveis. Isso protege contra a implantação de imagens falsificadas ou comprometidas. Além disso, o Cosign fornece uma camada adicional de segurança, ajudando a manter sua infraestrutura segura e confiável.

---

- **_Como Começar com o Cosign_**

Para começar a usar o Cosign, você pode seguir estas etapas simples.

Instale o Cosign em seu ambiente.

- **MacOS**

```zsh
brew install cosign
```

- **Binary or rpm/dpkg package** ([Cosign release page)](https://github.com/sigstore/cosign/releases/latest)

```zsh
# binary
curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64"
mv cosign-linux-amd64 /usr/local/bin/cosign
chmod +x /usr/local/bin/cosign

# rpm
LATEST_VERSION=$(curl https://api.github.com/repos/sigstore/cosign/releases/latest | grep tag_name | cut -d : -f2 | tr -d "v\", ")
curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign-${LATEST_VERSION}.x86_64.rpm"
rpm -ivh cosign-${LATEST_VERSION}.x86_64.rpm

# dkpg
LATEST_VERSION=$(curl https://api.github.com/repos/sigstore/cosign/releases/latest | grep tag_name | cut -d : -f2 | tr -d "v\", ")
curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign_${LATEST_VERSION}_amd64.deb"
dpkg -i cosign_${LATEST_VERSION}_amd64.deb
```

Em seguida, gere um par de chaves. *Guarde sua chave .key ela é de extrema importancia para assinar suas imagens.

```zsh
cosign generate-key-pair
```

Finalmente, assine suas imagens Docker com o Cosign usando o comando

- Altere o cosign.key para o nome da sua chave
- Altere o repositorio para o seu no DockerHub por exemplo
- Altere imagem e tag para as imagens que existe no seu repositorio.

```zsh
cosign sign --key cosign.key repositorio/imagem:tag
```

e para verificar alguma imagem assinada docker

```zsh
cosign verify --key cosign.pub repositorio/imagem:tag
```

> [!info] a chave publica pode ser compartilhada para que outras pessoas verifiquem a autenticidade da sua imagem.

---

- **_Melhores Práticas de Segurança_**

Além de usar o Cosign, é fundamental incorporar uma verificação de vulnerabilidades em suas imagens Docker. Como mensionado anteriormente uma ferramenta valiosa para essa finalidade é o Trivy. O Trivy examina imagens de contêineres em busca de vulnerabilidades conhecidas e fornece informações detalhadas sobre qualquer risco potencial. Ele pode ser integrado facilmente aos seus pipelines de CI/CD para garantir que apenas imagens seguras sejam implantadas.

Para começar com o Trivy, você pode instalá-lo

- MacOs

```zsh
brew install trivy
```

Ubuntu

```zsh
wget https://github.com/aquasecurity/trivy/releases/download/v0.45.0/trivy_0.45.0_Linux-64bit.deb
sudo dpkg -i trivy_0.45.0_Linux-64bit.deb
```

Para fazer o scan nas suas imagens utilize

```zsh
trivy image name_of_image:tag
```

A ferramenta verificará as camadas da imagem em busca de vulnerabilidades e fornecerá uma lista de quaisquer problemas encontrados. Isso permite que você tome medidas proativas para corrigir vulnerabilidades ou escolher imagens mais seguras.

Lembre-se de que a segurança é um processo contínuo. Portanto, agendar verificações regulares com o Trivy é uma prática recomendada para garantir que suas imagens Docker permaneçam protegidas ao longo do tempo.

- **_Conclusão_**

Em resumo, o uso do Cosign e do Trivy em conjunto pode fortalecer significativamente a segurança de suas imagens Docker. Ao adotar essas melhores práticas, você pode proteger sua organização contra ameaçass, garantindo que suas imagens sejam autênticas e tendo as vulnerabilidades mitigadas.