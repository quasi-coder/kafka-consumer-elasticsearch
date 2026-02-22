# Kafka Consumer → Elasticsearch

A Java application that consumes messages from a Kafka topic and indexes them into Elasticsearch using the Elasticsearch High Level REST Client, with bulk indexing and idempotent writes.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Apache Kafka | Message source |
| Elasticsearch REST High Level Client | Indexing to Elasticsearch |
| Gson | JSON parsing for tweet data |
| SLF4J | Logging |
| Maven | Build tool |

---

## How It Works

1. **Kafka Consumer** — Subscribes to the `twitter_tweets` topic on `localhost:9092`
2. **Batch poll** — Polls records in batches (100ms timeout)
3. **Idempotent writes** — Extracts tweet ID from JSON as the Elasticsearch document ID to prevent duplicates on re-processing
4. **Bulk indexing** — Uses `BulkRequest` to index all records in a batch in a single Elasticsearch call
5. **Manual offset commit** — Commits offsets after successful bulk indexing (consumer group: `kafka-demo-elasticsearch`)

---

## Configuration

| Setting | Value |
|---------|-------|
| Kafka bootstrap server | `localhost:9092` |
| Consumer group | `kafka-demo-elasticsearch` |
| Topic | `twitter_tweets` |
| Auto offset reset | `earliest` |
| Enable auto commit | `false` (manual commit) |
| Elasticsearch index | `twitter` |

**Hosted Elasticsearch (Bonsai):** Set `hostname`, `username`, and `password` in `ElasticSearchConsumer.java` to connect to a hosted cluster instead of local.

---

## Getting Started

1. **Start Kafka and create the topic**
   ```bash
   kafka-topics --zookeeper 127.0.0.1:2181 --topic twitter_tweets --create --partitions 3 --replication-factor 1
   ```

2. **Start Elasticsearch** (local or Bonsai)

3. **Run the kafka-producer-twitter project** to populate `twitter_tweets`

4. **Build and run**
   ```bash
   mvn clean package
   # Run ElasticSearchConsumer via IDE or maven exec
   ```

5. **Verify in Elasticsearch**
   ```bash
   curl http://localhost:9200/twitter/_search?pretty
   ```
