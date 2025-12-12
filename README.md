# 🏗️ Data Lakehouse On-Premise para IA Generativa

<div align="center">

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker)](https://www.docker.com/)
[![Airflow](https://img.shields.io/badge/Apache-Airflow-017CEE?logo=apache-airflow)](https://airflow.apache.org/)
[![MinIO](https://img.shields.io/badge/MinIO-Object_Storage-C72E49?logo=minio)](https://min.io/)

**Construindo uma "AWS Local" para Escritórios de Contabilidade e Advocacia**

[📖 Artigo no LinkedIn](#-série-de-artigos) | [🎯 Problema](#-o-problema) | [🚀 Instalação](#-instalação) | [📊 Arquitetura](#-arquitetura)

</div>

---

## 📚 Série de Artigos

Este repositório faz parte da série **"IA Corporativa: Não é Mágica, é Engenharia"**, uma jornada prática sobre como construir infraestrutura de dados para IA Generativa respeitando soberania e segurança dos dados.

### 📑 Artigos Publicados

1. **[IA Corporativa: Não é Mágica, é Engenharia](https://www.linkedin.com/pulse/ia-corporativa-n%C3%A3o-%C3%A9-m%C3%A1gica-engenharia-como-terabytes-mauro-junior-o7onf/?trackingId=mTkBzsn2QF6j6vUTMe2GHw%3D%3D)** *(Introdução)*
   - Como Terabytes de Contratos Viram Inteligência Sem Sair do Escritório

2. **[A Fundação – Construindo um Data Lakehouse On-Premise](https://www.linkedin.com/pulse/draft/preview/7405231109510250496/)** *(Artigo 1 - Você está aqui)*
   - Infraestrutura base com MinIO e Apache Airflow
   - 📦 **Repositório:** [mauropjjr/artigos-contruindo-data-like-para-ia-generativa](https://github.com/mauropjjr/artigos-contruindo-data-like-para-ia-generativa)

3. **🚧 Próximos Artigos** *(Em breve)*
   - Ingestão de Dados e OCR
   - Processamento com IA Generativa
   - RAG (Retrieval-Augmented Generation)
   - Deploy em Produção com Docker Swarm

---

## 🎯 O Problema

### Cenário: Escritório de Contabilidade e Advocacia ("Accounting & Law Tech")

**Desafios:**
- 📁 Terabytes de contratos confidenciais e transcrições de audiências
- 🔒 **Soberania dos dados**: Não podemos enviar documentos sensíveis para nuvens públicas
- ⚖️ Conformidade com LGPD e regulamentações do setor
- 💰 Custos proibitivos de storage em cloud pública
- 🚀 Necessidade de IA Generativa para análise de documentos

**Solução:**
Construir uma **"AWS Local"** - um Data Lakehouse on-premise com arquitetura moderna de nuvem, mas rodando nos servidores do escritório.

---

## 🏛️ Arquitetura

### Componentes Principais

```
┌─────────────────────────────────────────────────────────────┐
│                    CAMADA DE ORQUESTRAÇÃO                   │
│                                                             │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐ │
│  │   Airflow    │    │   Scheduler  │    │    Redis     │ │
│  │  Webserver   │◄───┤   (Celery)   │◄───┤   (Broker)   │ │
│  └──────────────┘    └──────────────┘    └──────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  CAMADA DE ARMAZENAMENTO                    │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │              MinIO Object Storage                    │  │
│  │              (S3-Compatible API)                     │  │
│  │                                                      │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐          │  │
│  │  │  BRONZE  │  │  SILVER  │  │   GOLD   │          │  │
│  │  │   Raw    │─►│Processed │─►│ Curated  │          │  │
│  │  │  Layer   │  │  Layer   │  │  Layer   │          │  │
│  │  └──────────┘  └──────────┘  └──────────┘          │  │
│  │                                                      │  │
│  │  📄 PDFs      ▶  📝 Texto   ▶  🧠 Embeddings        │  │
│  │  🎥 Vídeos    ▶  📊 Dados   ▶  📊 Metadados        │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Medallion Architecture (Arquitetura Medalhão)

| Camada | Bucket | Propósito | Exemplo |
|--------|--------|-----------|---------|
| 🥉 **Bronze** | `lake-bronze` | Dados brutos, sem transformação | PDFs originais, vídeos de audiências |
| 🥈 **Silver** | `lake-silver` | Dados processados e limpos | Texto extraído por OCR, transcrições |
| 🥇 **Gold** | `lake-gold` | Dados prontos para consumo | Embeddings vetoriais, metadados estruturados |

---

## 🛠️ Stack Tecnológica

### Por que essas ferramentas?

| Ferramenta | Papel | Justificativa |
|------------|-------|---------------|
| **MinIO** | Object Storage | ✅ S3-Compatible<br>✅ Open-source<br>✅ Alta performance<br>✅ Migração facilitada para cloud |
| **Apache Airflow** | Orquestração | ✅ Padrão de mercado<br>✅ Python-native<br>✅ Gestão de dependências<br>✅ Escalável |
| **Docker Swarm** | Orquestração de Contêineres | ✅ Alta disponibilidade<br>✅ Failover automático<br>✅ Simples para on-premise |
| **PostgreSQL** | Metadata Store | ✅ Robusto para Airflow<br>✅ Open-source |
| **Redis** | Message Broker | ✅ Performance para Celery<br>✅ Baixa latência |

---

## 🚀 Instalação

### Pré-requisitos

- Docker Engine 20.10+
- Docker Compose 2.0+
- 4GB de RAM disponível
- 20GB de espaço em disco

### Passo 1: Clone o Repositório

```bash
git clone https://github.com/mauropjjr/artigos-contruindo-data-like-para-ia-generativa.git
cd artigos-contruindo-data-like-para-ia-generativa
```

### Passo 2: Prepare o Ambiente

```bash
# Crie as pastas necessárias para o Airflow
mkdir -p ./dags ./logs ./plugins

# Configure o UID do Airflow
echo "AIRFLOW_UID=$(id -u)" > .env
```

### Passo 3: Suba a Infraestrutura

```bash
# Para desenvolvimento local
docker compose up -d

# Para produção com Docker Swarm
docker stack deploy -c docker-compose.yml data-stack
```

### Passo 4: Verifique os Serviços

```bash
docker compose ps
```

Você deve ver todos os serviços com status `healthy`:
- ✅ minio_datalake
- ✅ postgres
- ✅ redis
- ✅ airflow-webserver
- ✅ airflow-scheduler

---

## 🎮 Configuração Inicial

### 1️⃣ Configure o Data Lake (MinIO)

1. Acesse: **http://localhost:9001**
2. Login:
   - **Usuário:** `admin`
   - **Senha:** `password123`
3. Crie os buckets da Arquitetura Medalhão:
   - 🥉 `lake-bronze` (Raw Data)
   - 🥈 `lake-silver` (Processed Data)
   - 🥇 `lake-gold` (Curated Data)

### 2️⃣ Configure o Airflow

1. Acesse: **http://localhost:8080**
2. Login:
   - **Usuário:** `airflow`
   - **Senha:** `airflow`
3. Adicione a conexão com MinIO:
   - Vá em **Admin → Connections**
   - Clique em **+ Add a new record**
   - Preencha:
     ```
     Conn Id: minio_conn
     Conn Type: Amazon Web Services
     AWS Access Key ID: admin
     AWS Secret Access Key: password123
     Extra: {"endpoint_url": "http://minio:9000"}
     ```
   - Clique em **Save**

---

## 📂 Estrutura do Projeto

```
artigos-contruindo-data-like-para-ia-generativa/
├── 📄 docker-compose.yml      # Definição da infraestrutura
├── 📄 README.md               # Este arquivo
├── 📄 .env                    # Variáveis de ambiente
├── 📁 dags/                   # DAGs do Airflow
├── 📁 logs/                   # Logs do Airflow
├── 📁 plugins/                # Plugins customizados
└── 📁 docs/                   # Documentação adicional
```

---

## 🔐 Segurança

### ⚠️ Importante para Produção

Este setup é para **desenvolvimento e demonstração**. Para produção:

1. **Senhas:**
   - ❌ NÃO use senhas hardcoded
   - ✅ USE Docker Secrets ou Vault

2. **Rede:**
   - ✅ Configure firewall
   - ✅ Use TLS/SSL (HTTPS)
   - ✅ Restrinja acesso às portas

3. **Backup:**
   - ✅ Configure backup dos volumes Docker
   - ✅ Implemente retenção de dados

4. **Monitoramento:**
   - ✅ Configure logs centralizados
   - ✅ Implemente alertas

---

## 📊 Dashboards e Interfaces

| Serviço | URL | Descrição |
|---------|-----|-----------|
| 🎯 **Airflow UI** | http://localhost:8080 | Gerenciamento de pipelines |
| 📦 **MinIO Console** | http://localhost:9001 | Gerenciamento do Data Lake |

---

## 🎓 Divisão de Papéis

### 🏗️ Arquiteto de Dados
- Define a topologia da arquitetura
- Escolhe as tecnologias (MinIO, Airflow, Swarm)
- Estabelece a estratégia de camadas (Medallion)
- Define políticas de retenção e segurança

### ⚙️ Engenheiro de Dados
- Escreve o `docker-compose.yml`
- Configura as conexões entre serviços
- Cria os buckets e estrutura de pastas
- Garante que os serviços subam sem conflitos

---

## 🎯 Próximos Passos

Após concluir este artigo, você terá:

- ✅ Cluster funcional on-premise
- ✅ MinIO pronto para terabytes de dados
- ✅ Airflow pronto para orquestrar pipelines
- ✅ Rede segura e isolada

**Problema Atual:** Os buckets estão vazios! 🤔

No **Artigo 2**, vamos:
- 📥 Criar o pipeline de ingestão
- 👁️ Monitorar pastas da rede do escritório
- 🔄 Automatizar upload para `lake-bronze`
- 📝 Implementar OCR para transformar imagem em texto

---

## 📝 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

---

## 👨‍💻 Autor

**Mauro Junior**

- LinkedIn: [@mauropjjr](https://www.linkedin.com/in/mauropjjr/)
- GitHub: [@mauropjjr](https://github.com/mauropjjr)

---

## 🙏 Agradecimentos

- Comunidade Apache Airflow
- Comunidade MinIO
- Todos que contribuem com open-source

---

<div align="center">

**⭐ Se este projeto foi útil, deixe uma estrela! ⭐**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Siga-0077B5?logo=linkedin)](https://www.linkedin.com/in/mauropjjr/)
[![GitHub](https://img.shields.io/badge/GitHub-Siga-181717?logo=github)](https://github.com/mauropjjr)

</div>
