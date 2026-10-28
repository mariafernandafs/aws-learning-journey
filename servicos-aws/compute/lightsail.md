# ☁️ Amazon Lightsail

**Hospedagem simplificada na AWS: conceitos, recursos, armazenamento e comparações arquiteturais.**

`AWS` · `Compute` · `Lightsail` · `EC2` · `Block Storage` · `Multicloud`

---

## 📖 O que é Amazon Lightsail?

O **Amazon Lightsail** é um serviço da AWS que reúne recursos de computação, armazenamento, rede e bancos de dados em uma plataforma simplificada, voltada principalmente à hospedagem de sites e aplicações.

Embora ofereça máquinas virtuais, o Lightsail **não se limita a instâncias de computação**.

Sua proposta é facilitar o provisionamento e o gerenciamento de recursos, com planos de preços mais simples e custos mensais relativamente previsíveis.

Isso não significa que todos os componentes sejam totalmente gerenciados pela AWS ou que não existam cobranças adicionais.

---

## 🧩 Principais recursos

| Recurso | Descrição |
|---|---|
| **Instances** | Servidores virtuais com sistemas operacionais ou aplicações pré-configuradas (*blueprints*). |
| **Block Storage** | Discos persistentes, baseados em SSD, que podem ser anexados às instâncias para armazenamento adicional. |
| **Object Storage** | Buckets para armazenamento de arquivos e outros objetos. |
| **Containers** | Serviço para implantação e execução de aplicações conteinerizadas. |
| **Databases** | Bancos relacionais gerenciados, com suporte a MySQL e PostgreSQL. |
| **Networking** | Recursos como IP estático, DNS, firewall e balanceamento de carga. |
| **Snapshots** | Capturas do estado de determinados recursos, utilizadas para recuperação e criação de novos recursos. |
| **CDN** | Distribuição de conteúdo para usuários em diferentes localidades. |

---

## ⚖️ Amazon Lightsail vs. Amazon EC2

Embora ambos permitam executar servidores virtuais na AWS, o **Lightsail** prioriza simplicidade e facilidade de gerenciamento, enquanto o **EC2 (Elastic Compute Cloud)** oferece maior flexibilidade e controle sobre a infraestrutura.

| Característica | Amazon Lightsail | Amazon EC2 |
|---|---|---|
| **Proposta** | Plataforma simplificada para hospedagem de aplicações | Serviço de computação com maior flexibilidade e controle |
| **Configuração** | Planos predefinidos, com recursos agrupados | Configuração granular de instâncias, armazenamento e rede |
| **Máquinas virtuais** | Instâncias com opções de sistema operacional e blueprints | Ampla variedade de famílias e tipos de instâncias |
| **Armazenamento** | Disco de sistema incluído no plano e discos adicionais | Volumes EBS configuráveis e outras opções de armazenamento |
| **Rede** | Configurações simplificadas, IP estático e firewall | Integração avançada com VPC, sub-redes, security groups e outros recursos |
| **Escalabilidade** | Opções mais limitadas de dimensionamento e automação | Ampla variedade de opções de escalabilidade, incluindo Auto Scaling |
| **Preço** | Planos mensais com valores mais previsíveis | Diferentes modelos de cobrança e componentes de custo |
| **Casos de uso** | Sites, blogs, pequenas aplicações e projetos com infraestrutura simples | Aplicações corporativas, arquiteturas complexas e workloads com requisitos específicos |

### 💡 Quando utilizar cada um?

**Amazon Lightsail**

- Quando a prioridade é provisionar uma aplicação rapidamente.
- Para projetos que não exigem configurações avançadas de infraestrutura.
- Quando a simplicidade operacional e a previsibilidade de custos são importantes.

**Amazon EC2**

- Quando há necessidade de maior controle sobre computação, armazenamento e rede.
- Para aplicações que exigem arquiteturas personalizadas.
- Quando são necessárias opções avançadas de escalabilidade e integração com outros serviços AWS.

> **Importante:** Lightsail não é necessariamente mais barato que EC2 em todos os cenários. A comparação deve considerar os recursos necessários, o padrão de utilização e o custo total da solução.

---

## 🖥️ Imagens pré-configuradas: Blueprints vs. AMIs

Tanto o Amazon Lightsail quanto o Amazon EC2 permitem provisionar instâncias a partir de imagens pré-configuradas. A principal diferença está na nomenclatura, no catálogo disponível e nas possibilidades de personalização.

| Característica | Lightsail — Blueprints | EC2 — AMIs |
|---|---|---|
| **O que são?** | Modelos predefinidos de sistemas operacionais ou aplicações | Imagens utilizadas como base para inicializar instâncias EC2 |
| **Exemplos** | Ubuntu, WordPress e aplicações pré-configuradas | Amazon Linux, Ubuntu, Windows Server e imagens com softwares instalados |
| **Catálogo** | Mais enxuto e simplificado | Mais amplo, incluindo imagens oficiais, de terceiros e do AWS Marketplace |
| **Personalização** | Seleção de blueprints disponibilizados pelo Lightsail | Possibilidade de criar e utilizar AMIs personalizadas |
| **Objetivo** | Facilitar a criação rápida de instâncias | Oferecer flexibilidade e padronização no provisionamento |

**💡 Lessons Learned**

O uso de imagens pré-configuradas não é exclusivo do Lightsail.

No Lightsail, os **Blueprints** simplificam a escolha de sistemas operacionais e aplicações prontas.

No EC2, as **AMIs (Amazon Machine Images)** oferecem maior flexibilidade, inclusive para criar imagens próprias e reutilizá-las no provisionamento de novas instâncias.

---

## 💾 Block Storage e Availability Zones (AZs)

Tanto no **Amazon Lightsail** quanto no **Amazon EC2**, os discos adicionais de armazenamento em bloco possuem regras relacionadas às zonas de disponibilidade (*Availability Zones — AZs*).

No EC2, esse armazenamento é fornecido pelo **Amazon EBS (Elastic Block Store)**. No Lightsail, o recurso é apresentado como **Block Storage Disks**.

### 📍 1. Disco e instância devem estar na mesma AZ

Um disco adicional precisa estar na **mesma zona de disponibilidade da instância** à qual será anexado.

Isso acontece porque os volumes de armazenamento em bloco são recursos vinculados a uma zona de disponibilidade.

| Recurso | Availability Zone | Anexação |
|---|---|---|
| Instância A + Disco 1 | us-east-1a | ✅ Permitida |
| Instância B + Disco 1 | us-east-1b | ❌ Não permitida |

### 🔄 2. Como utilizar o disco em outra AZ?

Não é possível simplesmente mover um volume existente para outra zona de disponibilidade.

Uma alternativa é utilizar **snapshots**:

1. Criar um snapshot do disco original.
2. Criar um novo disco a partir desse snapshot na AZ de destino.
3. Anexar o novo disco à instância localizada nessa AZ.
4. Validar a integridade dos dados e o funcionamento da aplicação.

**Exemplo:**

`Disco (AZ A) → Snapshot → Novo disco (AZ B) → Instância (AZ B)`

O snapshot permite recriar o armazenamento em outra zona, sem exigir a transferência direta do volume original.

### 🔌 3. Anexar e desanexar discos

Discos adicionais podem ser **desanexados de uma instância e anexados a outra**, desde que sejam respeitadas as restrições do serviço e a compatibilidade entre os recursos.

Para um volume convencional utilizado por uma única instância:

- A instância de destino deve estar na mesma AZ do disco.
- O disco deve ser desanexado corretamente antes de ser conectado a outra instância.
- É importante desmontar o sistema de arquivos e garantir que as gravações tenham sido concluídas antes da desanexação.

**Exemplo:**

`Instância A → Desanexar disco → Anexar disco → Instância B`

Essa operação permite reutilizar o armazenamento sem necessariamente apagar seus dados.

> **Observação:** o Amazon EBS possui funcionalidades específicas, como Multi-Attach em volumes e instâncias compatíveis. Portanto, a regra de uma única instância não é universal para todos os tipos de volume EBS.

### 💰 4. Discos desanexados continuam gerando cobranças

A cobrança pelo armazenamento não depende de o disco estar anexado a uma instância.

Um disco adicional provisionado continua gerando custos mesmo quando está desanexado.

| Situação | Gera cobrança pelo disco? |
|---|---|
| Disco adicional anexado | ✅ Sim |
| Disco adicional desanexado | ✅ Sim |
| Instância desligada, disco existente | ✅ Sim |
| Disco excluído | ❌ Não há novas cobranças pelo disco excluído |

Snapshots e outros recursos preservados podem continuar gerando cobranças separadamente.

### 💡 Lessons Learned — Block Storage

- Discos adicionais do Lightsail e volumes EBS são vinculados a uma Availability Zone.
- Disco e instância precisam estar na mesma AZ para a anexação.
- Snapshots permitem criar novos discos em outras AZs.
- Discos adicionais podem ser reutilizados por outras instâncias, respeitando as restrições do serviço.
- Um disco desanexado continua gerando custos enquanto permanecer provisionado.
- A exclusão de uma instância não garante que todos os discos adicionais e snapshots associados sejam removidos.

> **Atenção:** discos adicionais e discos raiz não possuem necessariamente o mesmo comportamento de exclusão. No EC2, a remoção automática dos volumes depende de configurações como `DeleteOnTermination`.

---

## 🌐 IP estático e custos

O Amazon Lightsail permite criar **endereços IPv4 estáticos**, que podem ser associados a instâncias para manter um endereço IP público fixo.

Diferentemente de um endereço IP público dinâmico, o IP estático pode ser preservado e associado a outra instância compatível, facilitando a continuidade do acesso à aplicação.

### 💰 Pontos de atenção

- Um IP estático **não associado a nenhuma instância** pode gerar cobranças adicionais.
- A cobrança de endereços IPv4 públicos também deve ser considerada quando estão associados a instâncias, conforme as regras de preços vigentes.
- Excluir uma instância não significa necessariamente liberar o IP estático reservado.
- IPs estáticos não utilizados devem ser liberados para evitar custos desnecessários.

### 💡 Lessons Learned — Networking

Recursos de rede também podem gerar custos independentemente da utilização efetiva.

**Um IP estático reservado, mesmo sem estar associado a uma instância, pode continuar gerando cobranças.**

---

## 🌎 Multicloud: AWS vs. Azure vs. Oracle Cloud

Não existe uma correspondência exata entre todos os serviços dos provedores de nuvem. Por isso, a comparação abaixo considera **a finalidade e as principais capacidades de cada serviço**, e não apenas seus nomes.

| Recurso ou necessidade | AWS | Microsoft Azure | Oracle Cloud Infrastructure (OCI) |
|---|---|---|---|
| **Máquinas virtuais** | Lightsail Instances / EC2 | Azure Virtual Machines | OCI Compute Instances |
| **Armazenamento em bloco** | Lightsail Block Storage / EBS | Azure Managed Disks | OCI Block Volume |
| **Armazenamento de objetos** | Lightsail Buckets / S3 | Azure Blob Storage | OCI Object Storage |
| **Banco relacional gerenciado** | Lightsail Databases / RDS | Azure Database for MySQL / PostgreSQL | OCI MySQL HeatWave / OCI Database with PostgreSQL |
| **Execução de containers** | Lightsail Containers / ECS / Fargate | Azure Container Apps / Azure Container Instances | OCI Container Instances |
| **Gerenciamento de DNS** | Lightsail DNS / Route 53 | Azure DNS | OCI DNS |
| **Balanceamento de carga** | Lightsail Load Balancer / ELB | Azure Load Balancer / Application Gateway | OCI Load Balancer |
| **Snapshots e backups** | Lightsail Snapshots / EBS Snapshots | Azure Disk Snapshots / Azure Backup | OCI Block Volume Backups |

### 🔎 Observações importantes

- **Lightsail não possui um equivalente direto no Azure ou na OCI.** Ele combina recursos que, nesses provedores, normalmente são contratados e configurados separadamente.
- **Azure Virtual Machines e OCI Compute Instances** são comparáveis principalmente à funcionalidade de máquinas virtuais do Lightsail, mas, em termos de flexibilidade, se aproximam mais do Amazon EC2.
- **Serviços de containers** podem ter diferenças importantes de execução, escalabilidade, gerenciamento e cobrança.
- **Snapshots não substituem uma estratégia completa de backup**, que deve considerar retenção, recuperação e proteção contra falhas.

---

## 🔐 Responsabilidade compartilhada

O Lightsail simplifica o provisionamento e a administração de recursos, mas **não elimina as responsabilidades de quem utiliza o serviço**.

| Responsabilidade da AWS | Responsabilidade do usuário |
|---|---|
| Infraestrutura física e hardware | Configuração e segurança da aplicação |
| Infraestrutura subjacente de virtualização | Atualizações do sistema operacional das instâncias |
| Operação da infraestrutura do serviço | Controle de acesso e proteção dos dados |
| Segurança da infraestrutura de nuvem | Configuração de backups e recuperação |

> **Importante:** a divisão de responsabilidades varia conforme o recurso utilizado. Em serviços gerenciados, como bancos de dados, a AWS assume tarefas adicionais de infraestrutura e operação.

---

## 🚦 Quando escolher (ou não) o Lightsail?

### ✅ Lightsail pode ser uma boa escolha quando:

- A aplicação possui requisitos relativamente simples de infraestrutura.
- É necessário provisionar um ambiente rapidamente.
- A previsibilidade de custos é uma prioridade.
- O projeto não exige configurações avançadas de rede e escalabilidade.
- O objetivo é hospedar sites, aplicações web ou ambientes de aprendizado.

### ⚠️ Considere EC2 ou outros serviços AWS quando:

- São necessárias configurações avançadas de VPC e rede.
- Existem requisitos específicos de CPU, memória, GPU ou tipos de instância.
- A aplicação depende de opções avançadas de escalabilidade e automação.
- A arquitetura exige integrações ou configurações não disponíveis no Lightsail.
- É necessário maior controle sobre os componentes de infraestrutura.

A escolha deve considerar requisitos funcionais e não funcionais, segurança, disponibilidade, escalabilidade, custos e complexidade operacional.

---

## 💡 Lessons Learned

**Entendimento inicial:** Lightsail seria apenas uma máquina virtual simplificada, semelhante a uma instância EC2 com menos opções de configuração.

**Aprendizado:** Lightsail é uma plataforma que reúne diferentes recursos de infraestrutura e hospedagem, priorizando simplicidade de configuração e previsibilidade de custos.

Outros aprendizados importantes:

- Blueprints e AMIs permitem iniciar instâncias a partir de imagens pré-configuradas, mas possuem catálogos e possibilidades diferentes.
- O armazenamento em bloco possui restrições relacionadas às zonas de disponibilidade.
- Snapshots permitem recriar discos em outras AZs.
- Discos adicionais podem continuar existindo e gerando custos independentemente das instâncias.
- Endereços IP estáticos não associados também podem gerar cobranças.
- Serviços simplificados não eliminam a necessidade de boas práticas de segurança.
- Comparar serviços entre provedores exige avaliar capacidades e limitações, não apenas nomes semelhantes.

**Conceito → Experimentação → Observações → Aprendizado**

---

## 📚 Referências

- [Amazon Lightsail — AWS](https://aws.amazon.com/lightsail/)
- [Amazon Lightsail — Pricing](https://aws.amazon.com/lightsail/pricing/)
- [Amazon Lightsail Documentation](https://docs.aws.amazon.com/lightsail/)
- [Amazon Lightsail — Block Storage](https://docs.aws.amazon.com/lightsail/latest/userguide/understanding-block-storage-in-amazon-lightsail.html)
- [Amazon EC2 — AWS](https://aws.amazon.com/ec2/)
- [Amazon EC2 — Amazon Machine Images (AMIs)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html)
- [Amazon EBS — Volumes](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volumes.html)
- [Amazon EBS — Snapshots](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-snapshots.html)
- [Azure Virtual Machines — Microsoft Learn](https://learn.microsoft.com/en-us/azure/virtual-machines/)
- [Oracle Cloud Infrastructure Compute](https://docs.oracle.com/en-us/iaas/Content/Compute/home.htm)
