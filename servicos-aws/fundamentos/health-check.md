# ❤️ Health Checks

**Monitoramento de saúde e disponibilidade de aplicações e serviços.**

`Cloud Computing` · `Availability` · `Load Balancing` · `Backend`

---

## 📖 O que é Health Check?

Um **Health Check** é uma verificação utilizada para avaliar se um serviço, aplicação ou recurso está funcionando conforme determinadas condições.

Em arquiteturas com Load Balancers, os Health Checks permitem identificar destinos saudáveis (*healthy*) e não saudáveis (*unhealthy*), auxiliando no direcionamento do tráfego.

Seu objetivo é reduzir o envio de requisições para instâncias ou aplicações que não estejam aptas a atendê-las.

---

## ⚙️ Como funciona?

De forma simplificada:

1. O Load Balancer realiza verificações periódicas nos destinos configurados.
2. Cada destino responde à verificação.
3. O resultado é avaliado conforme critérios definidos, como código HTTP, intervalo e quantidade de sucessos ou falhas.
4. O destino é classificado como saudável ou não saudável.
5. O Load Balancer utiliza essa informação para orientar o encaminhamento de novas requisições.

> **Importante:** Health Checks ajudam a reduzir falhas percebidas pelos usuários, mas não garantem que todas as requisições serão atendidas sem erros.

---

## 📄 Health Check com arquivo estático

Uma abordagem simples consiste em disponibilizar um arquivo de texto por HTTP.

**Exemplo de caminho:**

`/health.txt`

**Conteúdo:**

```text
OK
```

O Load Balancer pode verificar esse caminho e considerar o destino saudável quando receber uma resposta HTTP dentro dos critérios configurados.

### ⚠️ Limitação

A disponibilidade do arquivo não garante que a aplicação esteja funcionando corretamente.

Por exemplo, o servidor web pode responder `200 OK` para `/health.txt` mesmo quando a API estiver indisponível.

Portanto, esse tipo de verificação é útil para determinados cenários, mas pode ser insuficiente para avaliar a saúde real de uma aplicação.

---

## ☕ Health Checks em aplicações Java

Em aplicações desenvolvidas com **Spring Boot**, é possível utilizar o Spring Boot Actuator para disponibilizar endpoints de monitoramento.

| Endpoint | Finalidade |
|---|---|
| `/actuator/health` | Consultar o estado de saúde da aplicação. |
| `/actuator/health/liveness` | Verificar se a aplicação está em execução e não precisa ser reiniciada. |
| `/actuator/health/readiness` | Verificar se a aplicação está pronta para receber tráfego. |

Os endpoints de *liveness* e *readiness* dependem de configuração e disponibilidade no ambiente.

### 💡 Health vs. Readiness

Uma aplicação pode estar em execução, mas temporariamente não estar pronta para receber requisições.

Por exemplo, durante a inicialização ou enquanto realiza operações necessárias para começar a atender tráfego.

Por isso, é importante distinguir:

- **Liveness:** a aplicação continua operacional?
- **Readiness:** a aplicação está pronta para receber requisições?

---

## 💡 Lessons Learned

- Health Checks são importantes para a disponibilidade de aplicações.
- Load Balancers podem utilizar Health Checks para selecionar destinos aptos a receber tráfego.
- É possível realizar verificações HTTP utilizando arquivos estáticos.
- Um arquivo acessível não garante que todos os componentes da aplicação estejam saudáveis.
- A escolha da verificação depende do comportamento e dos requisitos da aplicação.

**Verificar disponibilidade não é necessariamente o mesmo que verificar prontidão.**

---
 🏗️ Health Checks na prática

O diagrama apresenta duas perspectivas: o funcionamento geral de um Load Balancer e a utilização de endpoints do Spring Boot Actuator para verificar a saúde das aplicações.

![Health Check — Load Balancer e Spring Boot Actuator](../servicos-aws/loadBalancer.png)

*Figura 1 — Health Checks com Load Balancer e Spring Boot Actuator.*

---
## 📚 Referências

- [Amazon Lightsail — Load Balancers](https://docs.aws.amazon.com/lightsail/latest/userguide/understanding-lightsail-load-balancers.html)
- [Elastic Load Balancing — Health Checks](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/target-group-health-checks.html)
- [Spring Boot Actuator — Endpoints](https://docs.spring.io/spring-boot/reference/actuator/endpoints.html)

