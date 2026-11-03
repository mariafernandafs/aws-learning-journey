
# AWS Lightsail — Containers

> Anotações de estudo sobre containers no Amazon Lightsail, escalabilidade, deployments e diferenças em relação às máquinas virtuais e ao Kubernetes.

## 1. O que são containers?

Containers são ambientes isolados utilizados para executar aplicações e suas dependências.

Uma aplicação Java, por exemplo, pode ser empacotada em uma imagem Docker contendo:

- A aplicação compilada (JAR).
- O Java Runtime necessário.
- Bibliotecas e dependências.
- Configurações necessárias à execução.

Diferentemente das máquinas virtuais, containers normalmente compartilham o kernel do sistema operacional hospedeiro.

### Imagem vs. Container

- **Imagem:** artefato que serve como modelo para criar containers.
- **Container:** instância criada a partir de uma imagem, que pode estar em execução ou parada.

**Relação importante:**

- Um container é criado a partir de uma imagem.
- Uma mesma imagem pode originar vários containers.
- Cada container possui seu próprio estado de execução.

Exemplo:

```text
Imagem Docker: book-api:v1
          |
          |----------------|
          |                |
     Container 1      Container 2
       Book API         Book API
```

Os dois containers executam a mesma versão da aplicação, mas são instâncias independentes.

---

## 2. Containers vs. Máquinas Virtuais

O Lightsail oferece duas alternativas diferentes para executar aplicações.

### Lightsail Instances

São máquinas virtuais que você provisiona e administra.

Você é responsável por atividades como:

- Configuração do sistema operacional.
- Instalação de runtimes e dependências.
- Atualizações e manutenção do ambiente.
- Configuração dos processos da aplicação.
- Administração da infraestrutura da instância.

Se precisar de três máquinas virtuais, deverá provisionar três instâncias.

### Lightsail Container Service

É um serviço gerenciado para executar aplicações containerizadas.

A AWS administra a infraestrutura subjacente, enquanto você configura as imagens e os recursos necessários à aplicação.

**Principais vantagens:**

- Menor esforço de administração do sistema operacional.
- Deploy simplificado de imagens de containers.
- Escalabilidade horizontal configurável.
- Endpoint público para aplicações HTTP/HTTPS.
- Distribuição de tráfego entre as réplicas do serviço.

### Comparação

| Característica | Lightsail Instance | Lightsail Container Service |
|---|---|---|
| Infraestrutura | Máquina virtual | Containers gerenciados |
| Administração do SO | Responsabilidade do usuário | Gerenciada pela AWS |
| Publicação | Configuração na VM | Deployment de imagens |
| Escalabilidade | Provisionar/redimensionar instâncias | Configurar Power e Scale |
| Acesso SSH ao host | Sim | Não |
| Controle da infraestrutura | Maior | Menor |
| Complexidade operacional | Maior | Menor |

### Custo

Containers não são necessariamente mais caros que máquinas virtuais.

No Lightsail, o serviço gerenciado de containers pode custar mais do que uma VM pequena, dependendo da configuração.

Entretanto, ele reduz atividades operacionais, como administração do sistema operacional hospedeiro.

**Trade-off:**

- VM: maior controle e responsabilidade operacional.
- Container Service: menor controle da infraestrutura e maior simplicidade operacional.

A escolha deve considerar custo, produtividade, requisitos técnicos e esforço de manutenção.

---

## 3. Power e Scale no Lightsail

Ao criar um Lightsail Container Service, configuramos dois parâmetros principais.

### Power — Capacidade computacional

Define a quantidade de CPU e memória disponível por nó de capacidade do serviço.

Exemplos de planos:

- Nano
- Micro
- Small
- Medium
- Large

Aumentar o Power significa disponibilizar mais recursos computacionais por nó.

**Conceito relacionado: Scale Up (escalabilidade vertical).**

### Scale — Quantidade de réplicas

Define quantos nós de capacidade executarão o conjunto de containers configurado no deployment.

Exemplo:

```text
Power: Small
Scale: 3
```

Resultado:

- 3 nós de capacidade Small.
- Cada nó executa uma cópia do conjunto de containers definido.
- A AWS administra os nós subjacentes.

**Conceito relacionado: Scale Out (escalabilidade horizontal).**

### Scale Up vs. Scale Out

| Estratégia | Significado | Exemplo |
|---|---|---|
| Scale Up | Aumentar os recursos por instância | Mais CPU e memória |
| Scale Out | Aumentar a quantidade de réplicas | Scale de 2 para 4 |
| Scale Down | Reduzir a quantidade de réplicas | Scale de 4 para 2 |

**Importante:** no Lightsail Container Service, Scale é uma configuração de quantidade. Não significa que o serviço automaticamente aumentará ou reduzirá as réplicas com base no uso de CPU.

---

## 4. O que é um Deployment?

Deployment é a configuração que informa ao Lightsail quais containers devem ser executados.

Nele definimos, por exemplo:

- Nome dos containers.
- Imagens utilizadas.
- Portas.
- Variáveis de ambiente.
- Comando de inicialização, quando necessário.
- Container e porta associados ao endpoint público.
- Configurações de health check.

### Relação entre Container Service e Deployment

```text
Lightsail Container Service
    |
    |-- Power: Small
    |-- Scale: 3
    |
    |-- Deployment ativo
            |
            |-- Container: book-api
            |-- Imagem: book-api:v1
            |-- Porta: 8080
```

Nesse exemplo, o Lightsail executará três cópias da Book API.

### Um serviço pode ter vários Deployments?

O Lightsail Container Service possui **um deployment ativo por vez**.

É possível publicar novas versões e manter um histórico de deployments.

Exemplo:

```text
Deployment v1: book-api:v1
Deployment v2: book-api:v2
Deployment v3: book-api:v3 (ativo)
```

Os deployments anteriores não são aplicações independentes executadas permanentemente em paralelo.

### Um Deployment pode conter vários containers?

Sim!

Exemplo:

```text
Deployment
    |
    |-- Container A: book-api
    |-- Container B: worker
```

Se o Scale for 3:

```text
Nó 1: book-api + worker
Nó 2: book-api + worker
Nó 3: book-api + worker
```

**Resultado:**

- 1 Container Service.
- 1 deployment ativo.
- 2 containers definidos.
- Scale = 3.
- 6 containers executados no total.

Portanto:

**Scale multiplica o conjunto de containers do deployment, não necessariamente um único container.**

Se duas aplicações precisarem de escalabilidade e deployments independentes, podemos utilizar Container Services separados.

---

## 5. Endpoint público e Load Balancing

O Lightsail Container Service pode disponibilizar um endpoint público para receber requisições HTTP/HTTPS.

Quando há múltiplas réplicas, o serviço distribui o tráfego entre elas.

Exemplo:

```text
           Cliente
              |
              v
       Endpoint público
              |
       Distribuição HTTP
              |
       -----------------
       |       |       |
       v       v       v
    Book API Book API Book API
       1       2       3
```

### Health Checks

O Lightsail pode verificar a saúde do container associado ao endpoint público por meio de requisições HTTP.

Podemos configurar um caminho, por exemplo:

```text
/actuator/health
```

Ou um endpoint próprio:

```text
/health
```

O nome não precisa ser fixo: deve corresponder ao caminho configurado e disponibilizado pela aplicação.

**Atenção:** o health check do Lightsail não é a mesma coisa que as probes do Kubernetes.

---

## 6. Comparação com Kubernetes

No Kubernetes, a organização dos recursos é diferente.

### Kubernetes

```text
Deployment
    |
    v
ReplicaSet
    |
    |-- Pod 1
    |     |-- Container Book API
    |
    |-- Pod 2
    |     |-- Container Book API
    |
    |-- Pod 3
          |-- Container Book API
```

O Deployment define o estado desejado, incluindo a quantidade de réplicas de Pods.

Um Pod pode conter um ou mais containers.

### Lightsail vs. Kubernetes

| Conceito | Lightsail Container Service | Kubernetes |
|---|---|---|
| Unidade de configuração | Deployment do Lightsail | Deployment Kubernetes |
| Escalabilidade | Scale | replicas |
| O que é replicado | Conjunto de containers do deployment | Pods |
| Unidade que agrupa containers | Nó de capacidade gerenciado | Pod |
| Múltiplos deployments ativos | Um por Container Service | Vários por cluster |
| Administração | AWS gerencia a infraestrutura | Depende da plataforma Kubernetes |

**Importante:** um nó de capacidade do Lightsail não é equivalente a um Pod Kubernetes.

No Kubernetes, vários Pods podem compartilhar o mesmo nó computacional.

---

## 7. Cuidados arquiteturais ao escalar aplicações

Aumentar a quantidade de containers não garante, sozinho, que a aplicação esteja preparada para escalabilidade horizontal.

### Estado em memória

Imagine uma Book API que armazena livros em um ConcurrentHashMap.

Com Scale = 3:

```text
Container 1 -> Memória A
Container 2 -> Memória B
Container 3 -> Memória C
```

Cada container possui sua própria memória.

Se um POST salvar um livro no Container 1, um GET direcionado ao Container 2 poderá não encontrar esse livro.

### Como resolver?

Utilizar persistência externa compartilhada, por exemplo:

- Banco de dados PostgreSQL.
- Banco de dados gerenciado.
- Redis, quando adequado ao caso de uso.
- Armazenamento de objetos para arquivos.

A aplicação deve evitar depender de estado local para atender requisições que podem chegar a diferentes réplicas.

### Outros pontos de atenção

- Concorrência entre réplicas.
- Limites de conexões com bancos de dados.
- Gerenciamento de sessões.
- Idempotência de operações.
- Health checks.
- Logs e observabilidade.
- Consumo de CPU e memória.
- Custos conforme a quantidade de réplicas.

---

## 8. Quando escolher Lightsail Containers?

### Cenários adequados

- Aplicações web pequenas e médias.
- APIs REST containerizadas.
- Projetos de estudo.
- Aplicações com infraestrutura relativamente simples.
- Equipes que desejam reduzir o esforço operacional.

### Quando avaliar alternativas?

Para requisitos mais avançados, considerar:

- **Amazon ECS:** orquestração de containers integrada ao ecossistema AWS.
- **AWS Fargate:** capacidade computacional gerenciada para executar containers com ECS ou EKS.
- **Amazon EKS:** Kubernetes gerenciado pela AWS.

Essas alternativas oferecem diferentes níveis de flexibilidade, integração e complexidade operacional.

---

## 9. Resumo para revisão

- Imagem é o artefato utilizado para criar containers.
- Um container é criado a partir de uma imagem.
- Uma imagem pode originar vários containers.
- Lightsail Instances são máquinas virtuais.
- Lightsail Container Service executa containers em infraestrutura gerenciada.
- Power define CPU e memória por nó de capacidade.
- Scale define quantas cópias do conjunto de containers serão executadas.
- Scale Up aumenta recursos computacionais.
- Scale Out aumenta a quantidade de réplicas.
- Um Container Service possui um deployment ativo por vez.
- Um deployment pode definir vários containers.
- Kubernetes replica Pods, que podem conter um ou mais containers.
- Aplicações escaladas horizontalmente precisam considerar persistência compartilhada e gerenciamento de estado.

---

## 10. Perguntas de revisão

1. Qual a diferença entre imagem Docker e container?
2. Um container pode ser criado a partir de várias imagens?
3. O que acontece ao aumentar Power no Lightsail?
4. O que acontece ao aumentar Scale?
5. Se um deployment possui dois containers e Scale = 4, quantos containers serão executados?
6. Qual a diferença entre Lightsail Instances e Container Service?
7. Por que armazenar dados apenas na memória pode ser problemático com múltiplas réplicas?
8. Qual a diferença entre um Deployment do Lightsail e um Deployment do Kubernetes?
9. Qual a função do health check?
10. Quando seria interessante utilizar ECS ou EKS em vez do Lightsail?

---

## Referências oficiais

- [Amazon Lightsail — documentação](https://docs.aws.amazon.com/lightsail/)
- [Lightsail Container Services](https://docs.aws.amazon.com/lightsail/latest/userguide/amazon-lightsail-container-services.html)
- [Docker — conceitos básicos](https://docs.docker.com/get-started/docker-concepts/)
- [Kubernetes — Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Kubernetes — Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
