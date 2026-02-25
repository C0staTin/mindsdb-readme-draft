<a name="readme-top"></a>

<p align="center">
  <a href="https://github.com/mindsdb/mindsdb">
    <img src="/assets/mindsdb-header-github.png" alt="Query engine for AI analytics, powering agents to answer questions across all your live data" width="100%" />
  </a>
</p>

<div align="center">
  <a href="https://pypi.org/project/MindsDB/" target="_blank">
    <img src="https://badge.fury.io/py/MindsDB.svg" alt="MindsDB Release" />
  </a>
  <a href="https://www.python.org/downloads/" target="_blank">
    <img src="https://img.shields.io/badge/python-3.10.x%7C%203.11.x%7C%203.12.x%7C%203.13.x-brightgreen.svg" alt="Python supported" />
  </a>
  <a href="https://hub.docker.com/r/mindsdb/mindsdb" target="_blank">
  <img src="https://img.shields.io/docker/pulls/mindsdb/mindsdb.svg?logo=docker&label=Docker%20pulls&cacheSeconds=86400" alt="Docker pulls" />
  </a>

  <p align="center">
    <a href="https://mindsdb.com?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Website</a>
    ·
    <a href="https://docs.mindsdb.com?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Docs</a>
    ·
    <a href="https://mindsdb.com/contact?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Contact us for a demo</a>
    ·
    <a href="https://mindsdb.com/joincommunity?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Community Slack</a>
  </p>
</div>

---

MindsDB is a widely used open-source query engine for AI analytics. It enables AI agents to answer questions directly from databases, data warehouses, and applications – without requiring ETL processes.

The key features include:

- <a href="https://docs.mindsdb.com/integrations/data-overview">Federated Query Engine</a>
- <a href="https://docs.mindsdb.com/mindsdb_sql/knowledge_bases/overview">Knowledge Bases</a>
- <a href="https://docs.mindsdb.com/mindsdb_sql/agents/agent">AI Agents</a>
- <a href="https://docs.mindsdb.com/model-context-protocol/overview">MCP API</a>
- <a href="https://docs.mindsdb.com/mindsdb_sql/sql/create/jobs">Automation</a>

Watch MindsDB demo <a href="https://www.youtube.com/watch?v=HN4fHtS4mvo">here</a>.

## Enterprise Solution Built with MindsDB

Minds are specialized AI agents designed to answer questions across large-scale enterprise data.

- Securely connect your enterprise systems and operate at scale.
- Get accurate, data-driven insights through simple natural language queries.
- Share resources and insights across your teams.

Watch Minds demo <a href="https://www.youtube.com/watch?v=QIdPpzcaxXg">here</a>.

## Setup

Users can install MindsDB via <a href="https://docs.mindsdb.com/setup/self-hosted/docker">Docker</a>, <a href="https://docs.mindsdb.com/setup/self-hosted/docker-desktop">Docker Extension</a>, or <a href="https://docs.mindsdb.com/contribute/install">PyPI</a>.

Here is how to pull and run MindsDB via Docker:
```bash
docker run --name mindsdb_container \
-e MINDSDB_APIS=http,mysql \
-p 47334:47334 -p 47335:47335 \
mindsdb/mindsdb:latest
```
Here is how to install MindsDB for development locally via PyPI:
```bash
git clone https://github.com/<username>/mindsdb.git
python -m venv mindsdb-venv
Windows: .\mindsdb-venv\Scripts\activate
macOS/Linux: source mindsdb-venv/bin/activate
cd mindsdb
pip install -e .
pip install -r requirements/requirements-dev.txt
python -m mindsdb
```
## Usage

**Follow the <a href="https://docs.mindsdb.com/quickstart-tutorial">quickstart guide</a> to get started with MindsDB.**

Users can connect data from different  <a href="https://docs.mindsdb.com/integrations/data-overview">data sources</a> to MindsDB. No ETL, no data movement or transformation are required.
```sql
CREATE DATABASE postgresql_conn
WITH ENGINE = 'postgres', 
PARAMETERS = {
    "user": "demo_user",
    "password": "demo_password",
    "host": "samples.mindsdb.com",
    "port": "5432",
    "database": "demo",
    "schema": "sample_data"
};
```
MindsDB enables querying data across connected data sources.

Users can  <a href="https://docs.mindsdb.com/mindsdb_sql/sql/create/view">create views</a> with federated data from disparate data sources.
```sql
CREATE VIEW aggregated_data (
SELECT *
FROM postgresql_conn.travel p
JOIN files.transportation f
ON p.transporation = f.vehicletype
);
```
MindsDB enables aggregation of data through views.

Users can create RAG pipelines by inserting all their data into  <a href="https://docs.mindsdb.com/mindsdb_sql/knowledge_bases/overview">knowledge bases</a> that embed and store data for efficient AI retrieval.
```sql
CREATE KNOWLEDGE_BASE movies_kb
USING
   embedding_model = {
        "provider": "openai",
        "model_name" : "text-embedding-3-large",
        "api_key": "sk-..."
    },
    reranking_model = {
        "provider": "openai",
        "model_name": "gpt-4o",
        "api_key": "sk-..."
    },
  storage = my_pgvector.moviekb,
  metadata_columns = ['origin', 'release_year', 'title', 'genre'],
  content_columns = ['plot'],
  id_column = 'unique_id';
```
MindsDB enables semantic search across unstructured data.

Users can ask questions over their federated data using <a href="https://docs.mindsdb.com/mindsdb_sql/agents/agent_syntax">agents</a>.
```sql
CREATE AGENT my_agent
USING
    model = {
        "provider": "openai",
        "model_name" : "gpt-4o",
        "api_key": "sk-..."
    },
    data = {
         "knowledge_bases": ["mindsdb.movies_kb"],
         "tables": ["postgresql_conn.travel"]
    },
    prompt_template = 'mindsdb.movies_kb stores data about movies,
                      postgresql_conn.travel stores data about travel details';
```
See MindsDB’s recommended usage of agents <a href="https://docs.mindsdb.com/mindsdb_sql/agents/agent">here</a>.

Users can automate workflows with <a href="https://docs.mindsdb.com/mindsdb_sql/sql/create/jobs">jobs</a>.
```sql
CREATE JOB save_agent_output AS (

INSERT INTO my_db.agent_output (question, answer)
SELECT question, answer
FROM my_agent
WHERE question = “Which movies are recommended for children?”;

INSERT INTO my_db.agent_output (question, answer)
SELECT question, answer
FROM my_agent
WHERE question = “What are the most common travel destinations?”;
      
)
EVERY hour;
```
This job runs every hour and saves the output from the agent into the my_db database.

## Resources

Explore the latest updates, events, community channels, and company resources below to stay connected with MindsDB.

- Product news & guides (<a href="https://mindsdb.com/blog?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">blog</a>)
- Live demos & webinars (<a href="https://mindsdb.com/events?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">events</a>)
= Community support (<a href="https://mindsdb.com/joincommunity">Slack</a>)
- Brand guidelines (<a href="https://mindsdb.com/press-kit?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">press kit</a>)
- Contact (<a href="https://mindsdb.com/contact?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">form</a>)

Explore step-by-step tutorials and real-world use cases to see how MindsDB powers AI-driven search, analytics, automation, and enterprise intelligence across industries.

- Enterprise Knowledge Search (<a href="https://mindsdb.com/blog/fast-track-knowledge-bases-how-to-build-semantic-ai-search-by-andriy-burkov?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Advanced Semantic Search  (<a href="https://mindsdb.com/blog/blend-hybrid-retrieval-with-structured-data-using-mindsdb-knowledge-bases?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Customer Support Automation (<a href="https://mindsdb.com/blog/building-janus-an-ai-powered-customer-support-helpdesk-system-powered-by-mindsdb?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example1</a>, <a href="https://mindsdb.com/blog/building-agentic-workflow-auto-banking-customer-service-with-mindsdb?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example2</a>)
- Intelligent Content Discovery (<a href="https://mindsdb.com/blog/mysql-mindsdb-unlocks-intelligent-content-discovery-for-web-cms-with-knowledge-bases-and-cursor?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Financial Analysis Agents (<a href="https://mindsdb.com/blog/streamline-financial-analysis-with-mindsdb-s-knowledge-bases-and-hybrid-search?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Real-time AI-powered analytics (<a href="https://mindsdb.com/blog/mariadb-mindsdb-turns-woocommerce-data-to-insights-with-real-time-ai-analytics-for-ecommerce-teams?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Conversational Data Assistants (<a href="https://mindsdb.com/blog/unlocking-operational-intelligence-in-energy-utilities-with-mindsdb-knowledge-bases-hybrid-search?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- CRM Intelligence (<a href="https://mindsdb.com/blog/unlocking-salesforce-crm-intelligence-with-mindsdb-s-ai-powered-knowledge-bases?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Compliance & Customer Intelligence (<a href="https://mindsdb.com/blog/enterprise-software-vendors-drive-compliance-customer-insights-with-mindsdb-knowledge-bases-hybrid-search?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)
- Conversation Intelligence (<a href="https://mindsdb.com/blog/introducing-mindsdb-s-integration-with-gong-ai-analytics-on-call-data?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">example</a>)

Subscribe to our (<a href="https://mindsdb.com/blog?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">blog</a>) for more

## Contributing

Thank you for contributing to MindsDB!

Developers can contribute to MindsDB by submitting code changes through pull requests or by opening issues to report bugs, suggest new features or enhancements.

Here are some ideas for contributors:

- <a href="https://docs.mindsdb.com/contribute/data-handlers">Develop a database integration</a>
- <a href="https://docs.mindsdb.com/contribute/app-handlers">Develop an app integration</a>
- Identify and fix bugs

How to contribute:

- <a href="https://docs.mindsdb.com/contribute/install">Install MindsDB for development</a>
- <a href="https://docs.mindsdb.com/contribute/contribute">Push code changes to the MindsDB repository</a>

Security Vulnerabilities
If you discover a security vulnerability, please follow the reporting process outlined in our <a href="https://github.com/mindsdb/mindsdb/security">Security Policy</a>.

## Support

If you encounter any issues or have questions about MindsDB, you can reach us through the following channels:

- Ask questions in our <a href="https://mindsdb.com/joincommunity?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Slack Community</a>
- Join the discussion on <a href="https://github.com/mindsdb/mindsdb/discussions">GitHub Discussions</a>

For commercial or enterprise support, please contact the <a href="https://mindsdb.com/contact?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">MindsDB team directly</a>.

