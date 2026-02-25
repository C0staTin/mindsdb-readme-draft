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

MindsDB is a popular open-source query engine for AI analytics, powering AI agents that need to answer questions directly from databases, data warehouses, and applications, with no ETL required.

<div align="center">
  <a href="https://www.youtube.com/watch?v=HN4fHtS4mvo" title="Watch demo">
    <img
      src="/assets/mindsdb_demo.gif"
      alt="MindsDB demo - answer questions in plain English from live enterprise data"
      title="Click to watch the full video"
      width="80%"
    />
  </a>
</div>

## What you can build with MindsDB Query Engine

| TEXT-TO-SQL AGENTS | SEMANTIC SEARCH AGENTS |
| --- | --- |
| Get precise, data-driven answers using natural language. <br /><br /> Unify and query data across sources (MySQL, Salesforce, Shopify, etc.), without ETL. <br /><br /> <a href="https://www.youtube.com/watch?v=QIdPpzcaxXg">Watch video</a> | Ground LLM responses in your most relevant internal knowledge. <br /><br /> Search across unstructured sources like documents, support tickets, Google Drive, and more. <br /><br /> <a href="https://www.youtube.com/watch?v=HN4fHtS4mvo">Watch video</a> |

## How MindsDB works

MindsDB follows a simple workflow: **Connect → Unify → Respond**. At the center is an SQL-compatible data language with additional constructs for searching unstructured data, managing workflows (jobs/triggers), and building agents.

<table style="width:100%; border-collapse:collapse; border:none;">
  <tr>
    <td style="width:25%; border:none; padding:8px 16px; vertical-align:middle;">
      <strong><a href="https://docs.mindsdb.com/integrations/data-overview?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Connect</a></strong>
    </td>
    <td style="width:75%; border:none; padding:8px 16px; vertical-align:middle;">
      <strong>Universal data access:</strong> Give your agents federated access to 200+ live data sources (Postgres, MongoDB, Slack, files, and more).
    </td>
  </tr>
  <tr>
    <td style="width:25%; border:none; padding:8px 16px; vertical-align:middle;">
      <strong><a href="https://docs.mindsdb.com/mindsdb-unify?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Unify</a></strong>
    </td>
    <td style="width:75%; border:none; padding:8px 16px; vertical-align:middle;">
      <strong>Dynamic context engine:</strong> Fuse structured tables with vectorized data (text, PDFs, HTML) inside a Knowledge Base.
    </td>
  </tr>
  <tr>
    <td style="width:25%; border:none; padding:8px 16px; vertical-align:middle;">
      <strong><a href="https://docs.mindsdb.com/mindsdb-respond?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Respond</a></strong>
    </td>
    <td style="width:75%; border:none; padding:8px 16px; vertical-align:middle;">
      <strong>Autonomous reasoning:</strong> Deploy agents that blend and retrieve data points across your stack to produce grounded answers.
    </td>
  </tr>
</table>

## Quickstart: build your first agent in 5 minutes

Install MindsDB via Docker (or Docker Desktop) and configure system default models settings. Docs: <a href="https://docs.mindsdb.com/setup/self-hosted/docker?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Installation</a>.

```bash
docker run --name mindsdb_container \
  -e MINDSDB_APIS=http,mysql \
  -p 47334:47334 -p 47335:47335 \
  mindsdb/mindsdb

# open http://127.0.0.1:47334
```

1) Connect and blend live data.
   Docs: <a href="https://docs.mindsdb.com/mindsdb-connect?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Connect sources</a>.

```sql
--1. Connect SQL database
CREATE DATABASE postgres_demo
WITH ENGINE = 'postgres', 
PARAMETERS = {
    "user": "demo_user",
    "password": "demo_password",
    "host": "samples.mindsdb.com",
    "port": "5432",
    "database": "demo",
    "schema": "sample_data"
};

--2. Connect document database
CREATE DATABASE mongodb_demo
WITH
  ENGINE = 'mongodb',
  PARAMETERS = {
    "host": "mongodb+srv://demouser:MindsDB_demo@mindsdb-demo.whljnvh.mongodb.net/public-demo"
  };

--3. Blend MongoDB and Postgres data into a single view
CREATE VIEW mindsdb.enterprise_sales AS (
  SELECT *
  FROM postgres_demo.websales_sales AS sales
  JOIN mongodb_demo.customers AS customers
     ON sales.customer_id = customers.customer_id
  WHERE
     customers.segment = "Enterprise"
     AND sales.sales > 1000
);
```

2) Vectorize unstructured data by creating a "knowledge base."
   Docs: <a href="https://docs.mindsdb.com/mindsdb_sql/knowledge_bases/overview?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Knowledge bases</a>.

```sql
-- 4. Create a knowledge base
CREATE KNOWLEDGE BASE reviews_kb
USING
  content_columns  = ['review_text'],
  metadata_columns = ['review_id','product_id','customer_id','rating','review_date'];

-- 5. Insert data into a knowledge base
INSERT INTO reviews_kb
SELECT review_text, review_id, product_id, customer_id, rating, review_date
FROM mongodb_demo.reviews;
```

3) Ask questions in natural language.
   Docs: <a href="https://docs.mindsdb.com/mindsdb_sql/agents/agent?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">Agents</a>.
```sql
-- 6. Create an agent
CREATE AGENT my_agent
USING
data = { 
   "knowledge_bases": ["mindsdb.reviews_kb"],
   "tables": ['mindsdb.enterprise_sales']
},
prompt_template='you are a skilled data analyst. Provide accurate responses based on the data available to you:
"mindsdb.reviews_kb" has data about product reviews
"mindsdb.enterprise_sales" has data about sales to enterprise customers
';

-- 7. Query agent
SELECT answer
FROM my_agent 
WHERE question = 'What do enterprise customers say about our best-selling product?';
```

Integrate via API or MCP: <a href="https://docs.mindsdb.com/overview_sdks_apis?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">SDKs & APIs</a>.

## 📃 Tutorials
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

## 🫴 Help and support

Stuck on a query? Found a bug? We’re here to help.
<table style="width:100%; border-collapse:collapse;">
  <tr>
    <td style="width:30%; border:1px solid #d0d7de; padding:12px; vertical-align:top;">
      Ask a question
    </td>
    <td style="width:70%; border:1px solid #d0d7de; padding:12px; vertical-align:top;">
      Join our <a href="https://mindsdb.com/joincommunity">Slack Community</a>.
    </td>
  </tr>
  <tr>
    <td style="width:30%; border:1px solid #d0d7de; padding:12px; vertical-align:top;">
      Report a bug
    </td>
    <td style="width:70%; border:1px solid #d0d7de; padding:12px; vertical-align:top;">
      Open a <a href="https://github.com/mindsdb/mindsdb/issues">GitHub Issue</a>. Please include reproduction steps!
    </td>
  </tr>
  <tr>
    <td style="width:30%; border:1px solid #d0d7de; padding:12px; vertical-align:top;">
      Get commercial support
    </td>
    <td style="width:70%; border:1px solid #d0d7de; padding:12px; vertical-align:top;">
      Contact the <a href="https://mindsdb.com/contact?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">MindsDB Team</a> for enterprise SLAs and custom solutions.
    </td>
  </tr>
</table>

**Security Note:** If you find a security vulnerability, please do not open a public issue. Refer to our <a href="https://github.com/mindsdb/mindsdb/security">security policy</a> for reporting instructions.

## 🤝 Contribute to MindsDB

MindsDB is open source and contributions are welcome, whether you're fixing a bug, adding an integration, improving docs, or building agent capabilities.

<strong>Ways you can help:</strong>
- 🔌 Add a data integration: Connect new databases, SaaS APIs, or vector stores.
- 🧠 Add an AI handler: Integrate new LLMs or specialized models into the reasoning engine.
- 📚 Improve documentation: Clarify guides, fix typos, add examples.
- 🐛 Report bugs: File issues with clear steps to reproduce.

<strong>Ready to start?</strong>

- Read the <a href="https://docs.mindsdb.com/contribute/contribute?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">contribution guide</a> to get set up.
- Browse <a href="https://github.com/mindsdb/mindsdb/issues">open issues</a>.
- Join the #contributors channel in <a href="https://mindsdb.com/joincommunity">Slack</a>.
- Explore <a href="https://mindsdb.com/community?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">community rewards and programs</a>.

<div align="center">

<strong>Our top 100 contributors</strong>

<a href="https://github.com/mindsdb/mindsdb/graphs/contributors">
<img src="https://contrib.rocks/image?repo=mindsdb/mindsdb&max=100&columns=10" />
</a>
	
Made with [contrib.rocks](https://contrib.rocks)
</div>

## 📚 Useful Resources

- Product news & guides (<a href="https://mindsdb.com/blog?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">blog</a>)
- Live demos & webinars (<a href="https://mindsdb.com/events?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">events</a>)
= Community support (<a href="https://mindsdb.com/joincommunity">Slack</a>)
- Brand guidelines (<a href="https://mindsdb.com/press-kit?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">press kit</a>)
- Contact (<a href="https://mindsdb.com/contact?utm_medium=community&utm_source=github&utm_campaign=mindsdb%20repo">form</a>)
