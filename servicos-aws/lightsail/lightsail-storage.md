
# AWS Lightsail — Storage: Buckets e Disks

> Anotações de estudo sobre as opções de armazenamento do Amazon Lightsail, com foco em conceitos, casos de uso e operação em Linux.

## 1. Visão geral

O Lightsail oferece diferentes recursos de armazenamento. Dois deles são:

- **Bucket (armazenamento de objetos):** guarda objetos, como imagens, documentos, arquivos estáticos e backups. O acesso normalmente ocorre por APIs HTTP compatíveis com Amazon S3, e não como um disco Linux tradicional.
- **Disk (armazenamento em bloco):** fornece um volume de blocos para ser anexado a uma instância Lightsail. O sistema operacional pode utilizá-lo como dispositivo de armazenamento, após a preparação necessária.

> **Custo:** armazenamento de objetos costuma ser mais econômico por GB em muitos cenários, mas não é uma comparação direta. Avalie os planos, limites, operações e necessidades de acesso de cada opção.

## 2. Bucket — Object Storage

### Conceito

Um **bucket** é um contêiner lógico de objetos. Cada objeto possui conteúdo e metadados, sendo identificado por uma chave (*key*).

Exemplo:

```text
bucket: fotos-app
└── usuarios/
    └── 123/
        └── perfil.jpg
```

`usuarios/123/perfil.jpg` é uma chave de objeto; as “pastas” representam prefixos no nome da chave.

### Casos de uso

- Fotos e arquivos enviados por usuários.
- Documentos e arquivos estáticos.
- Backups e exportações.
- Arquivos acessados por aplicações via API.

### Características

- Não é um sistema de arquivos POSIX convencional.
- Não precisa ser particionado, formatado ou montado como um volume Linux.
- É acessado por APIs, SDKs e ferramentas compatíveis com S3, respeitando as permissões configuradas.
- Controle de acesso e exposição pública exigem atenção.

## 3. Disk — Block Storage

### Conceito

Um **disk** é um volume de armazenamento em bloco, utilizado como um dispositivo pelo sistema operacional. No Lightsail, discos adicionais devem estar na **mesma zona de disponibilidade** da instância à qual serão anexados.

Exemplos de uso:

- Dados persistentes de aplicações.
- Diretórios que precisam de um sistema de arquivos Linux.
- Armazenamento adicional além do disco incluído na instância.

### Fluxo de preparação no Linux

```text
Criar disco no Lightsail
       ↓
Anexar à instância compatível
       ↓
Identificar o dispositivo no Linux
       ↓
[Opcional] Criar partição
       ↓
[Somente se for novo/vazio] Criar filesystem
       ↓
Montar em um diretório
       ↓
Configurar montagem persistente, se necessário
```

**Importante:** anexar um disco não significa que ele estará automaticamente montado e pronto para uso. Por outro lado, um volume que já contém filesystem e dados **não deve ser formatado novamente**.

### Etapa 1 — Identificar os dispositivos

```bash
lsblk
```

Exibe discos, partições e pontos de montagem.

Outras opções úteis:

```bash
sudo fdisk -l
sudo sfdisk -l
lsblk -f
```

`lsblk -f` ajuda a identificar filesystem, UUID e ponto de montagem.

> **Atenção:** os nomes de dispositivos variam conforme a instância e o ambiente. Não presuma que o novo disco será `/dev/xvdf` ou `/dev/nvme1n1`. Identifique-o antes de executar qualquer comando destrutivo.

### Etapa 2 — Particionar (opcional)

É possível criar uma partição no volume, mas **não é obrigatório**. Um filesystem pode ser criado diretamente no dispositivo de bloco, dependendo do planejamento.

Em ambientes reais, a escolha entre disco inteiro e partições deve ser deliberada.

### Etapa 3 — Criar o filesystem (somente disco novo e vazio)

Exemplo **ilustrativo** com ext4:

```bash
# SUBSTITUA pelo dispositivo correto, identificado previamente.
# ESTE COMANDO APAGA/RECRIA ESTRUTURAS DE FILESYSTEM NO ALVO.
sudo mkfs.ext4 /dev/EXEMPLO
```

- **Particionar** organiza regiões do disco.
- **Formatar** cria as estruturas de um sistema de arquivos, como `ext4` ou `xfs`.

**Nunca execute `mkfs` em um volume com dados que precisam ser preservados.**

### Etapa 4 — Montar o filesystem

Exemplo ilustrativo:

```bash
sudo mkdir -p /mnt/dados
sudo mount /dev/EXEMPLO /mnt/dados
```

Conferir:

```bash
lsblk -f
df -h
```

O comando `mount` associa o filesystem a um diretório do Linux. Para montagem automática após reinicialização, normalmente configura-se `/etc/fstab`, preferencialmente usando **UUID**, com validação cuidadosa antes do reboot.

### Etapa 5 — Persistência após reboot

Obtenha o UUID:

```bash
sudo blkid
```

Uma entrada ilustrativa em `/etc/fstab` seria:

```fstab
UUID=<UUID_DO_VOLUME>  /mnt/dados  ext4  defaults,nofail  0  2
```

O filesystem, UUID e opções precisam corresponder ao volume real. Uma configuração incorreta pode prejudicar a inicialização ou a disponibilidade do sistema.

## 4. Comparativo rápido

| Critério | Bucket | Disk |
|---|---|---|
| Modelo | Objetos | Blocos |
| Acesso típico | API/SDK compatível com S3 | Sistema operacional / filesystem |
| Montagem Linux tradicional | Não é necessária | Normalmente sim |
| Particionamento e formatação | Não | Podem ser necessários em disco novo |
| Uso comum | Imagens, documentos, backups | Dados de aplicação e diretórios persistentes |
| Zona de disponibilidade | Não é anexado como volume a uma instância | Disco adicional precisa estar na mesma AZ da instância |

## 5. Boas práticas e alertas

1. **Faça backup ou snapshot** antes de operações arriscadas com volumes.
2. **Identifique o dispositivo corretamente** antes de particionar ou formatar.
3. **Não confunda anexar com montar:** são operações distintas.
4. **Evite armazenar segredos ou disponibilizar objetos publicamente sem necessidade.**
5. **Considere a persistência:** dados no disco da instância e em volumes adicionais têm características de ciclo de vida diferentes; revise as políticas de backup.
6. **Compare custo e padrão de acesso**, não apenas preço por GB.

## 6. Conexão com outros serviços AWS

- **Amazon S3:** serviço de armazenamento de objetos ao qual o modelo de buckets do Lightsail se relaciona.
- **Amazon EBS:** armazenamento em bloco utilizado com instâncias EC2; conceito semelhante ao de discos adicionais do Lightsail.
- **Amazon EC2:** oferece mais opções de configuração e controle de infraestrutura que o Lightsail.

## 7. Perguntas de revisão

1. Por que não preciso formatar um bucket?
2. Qual é a diferença entre **anexar** um volume e **montá-lo** no Linux?
3. Por que `lsblk -f` deve ser executado antes de `mkfs.ext4`?
4. Um disco novo precisa obrigatoriamente de partição?
5. Qual opção escolheria para armazenar fotos de usuários? E para adicionar um diretório persistente à instância?

## Referências oficiais

- [Amazon Lightsail — documentação](https://docs.aws.amazon.com/lightsail/)
- [Lightsail — discos de armazenamento em bloco](https://docs.aws.amazon.com/lightsail/latest/userguide/what-is-block-storage.html)
- [Lightsail — armazenamento de objetos](https://docs.aws.amazon.com/lightsail/latest/userguide/buckets-in-amazon-lightsail.html)
- [Linux `lsblk` — manual](https://man7.org/linux/man-pages/man8/lsblk.8.html)
- [Linux `mount` — manual](https://man7.org/linux/man-pages/man8/mount.8.html)
