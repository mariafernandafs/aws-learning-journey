# 🏗️ Infraestrutura e Disponibilidade

**Conceitos e anotações sobre infraestrutura cloud, conectividade, disponibilidade e custos.**

---

## 🔑 SSH Key Pairs

Na AWS, as **SSH Key Pairs utilizadas em serviços como EC2 e Lightsail possuem escopo regional**.

Isso significa que uma Key Pair cadastrada em uma região não fica automaticamente disponível em outra.

### 💡 Pontos importantes

- Key Pairs são compostas por uma chave pública e uma chave privada.
- A chave pública pode ser utilizada para configurar a autenticação SSH em uma instância.
- A chave privada deve permanecer protegida e nunca ser publicada em repositórios.
- É possível utilizar a mesma chave pública em diferentes regiões, desde que seja cadastrada ou configurada nos recursos correspondentes.

**Lessons Learned:** a regionalidade está relacionada ao cadastro e gerenciamento das chaves na AWS, não a uma limitação do protocolo SSH.

---

## ⚖️ Load Balancer e custos

Um **Load Balancer** distribui tráfego entre diferentes destinos, como servidores e aplicações.

Também pode utilizar Health Checks para identificar destinos saudáveis e decidir quais estão aptos a receber requisições.

### 💰 Atenção aos custos

- Balanceadores de carga podem gerar cobranças próprias.
- A utilização do AWS Free Tier não garante gratuidade para todos os balanceadores.
- Créditos promocionais podem cobrir custos elegíveis, conforme as condições da conta.
- Recursos provisionados para laboratório devem ser revisados e removidos quando não forem mais necessários.

---

## 🖥️ Server Farm vs. Datacenter

| Conceito | Definição |
|---|---|
| **Server Farm** | Conjunto de servidores que trabalham para fornecer capacidade computacional ou executar serviços. |
| **Datacenter** | Instalação física que abriga servidores, equipamentos de rede, armazenamento, energia e refrigeração. |

Uma *server farm* pode estar localizada dentro de um datacenter, mas os conceitos não são sinônimos.

---

## 📚 Referências

- [Amazon EC2 — Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
- [Amazon Lightsail — SSH Key Pairs](https://docs.aws.amazon.com/lightsail/latest/userguide/understanding-ssh-in-amazon-lightsail.html)
- [Amazon Lightsail — Load Balancers](https://docs.aws.amazon.com/lightsail/latest/userguide/understanding-lightsail-load-balancers.html)
- [Amazon Lightsail — Pricing](https://aws.amazon.com/lightsail/pricing/)
