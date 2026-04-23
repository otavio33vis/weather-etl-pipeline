# Weather ETL Pipeline — Cascavel/PR

Pipeline de dados meteorológicos com arquitetura Medallion (Silver + Gold), orquestrado pelo Apache Airflow e deployado em servidor cloud.

---

## Sobre

Coleta dados climáticos de Cascavel/PR via API OpenWeatherMap a cada hora, transforma com Pandas e armazena em PostgreSQL com camadas analíticas prontas para dashboard.

---

## Stack

Python, Apache Airflow, PostgreSQL, Pandas, SQLAlchemy, Docker, Redis, Hetzner Cloud

---

## Arquitetura

```
OpenWeatherMap API
        ↓ 
  extract_date.py
        ↓ 
 transform_data.py	
        ↓ 
 load_data.py	
        ↓
Silver: cascacity_weather
        ↓ 
gold_transforms.py	
        ↓
Gold: 5 tabelas analíticas
```

---

## Camada Gold

- `gold_temperatura_diaria` — média, máxima e mínima por dia
- `gold_amplitude_termica` — amplitude + hora mais quente e mais fria
- `gold_pressao_tendencia` — variação de pressão com indicador de tendência
- `gold_padrao_climatico` — frequência de condições climáticas por hora
- `gold_sensacao_termica` — divergência entre temperatura real e sensação térmica

---

## Como executar

Clone o repositório e crie o arquivo `config/.env` com base no `.env.template` da raiz:

```env
# Airflow
AIRFLOW_UID=1000
AIRFLOW_IMAGE_NAME=apache/airflow:3.1.7
_AIRFLOW_WWW_USER_USERNAME=admin
_AIRFLOW_WWW_USER_PASSWORD=sua_senha

# Gere com: python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
AIRFLOW__CORE__FERNET_KEY=

# Postgres
POSTGRES_USER=airflow
POSTGRES_PASSWORD=sua_senha
POSTGRES_DB=airflow


# App
api_key=sua_chave_openweathermap
user=airflow
postgres_password=sua_senha
database=weather_db
```

Crie o banco e suba os serviços:

```bash
docker compose up postgres -d
docker compose exec postgres psql -U airflow -c "CREATE DATABASE weather_db;"
docker compose up -d
```

Acesse o Airflow em `http://localhost:8080`, ative a DAG `weather_etl_pipeline` e dispare manualmente.

---

## Estrutura

```
weather-etl-pipeline/
├── dags/
│   └── weather_dag.py
├── notebooks/
│   └── analysis_data.ipynb
├── src/
│   ├── extract_date.py
│   ├── transform_data.py
│   ├── load_data.py
│   └── gold_transforms.py
├── config/
│   └── .env              # não versionado — crie a partir do .env.template
├── docker-compose.yaml
├── .env.template
└── pyproject.toml
```

---

## Autor

Ismael Otávio Fernandes de Oliveira
[LinkedIn](https://www.linkedin.com/in/ismael-otavio-fernandes-de-oliveira/) 