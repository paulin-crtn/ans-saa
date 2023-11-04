# 🚚 Serverless - Kinesis

## 🍃 Data Streams

Scalable **streaming service**: design to **INGEST data** in **realtime** (huge amount of data from lots of devices or applications).

- Public service & highly available by design
- `Producers` send data into a kinesis stream
- Streams can scale from low to near infinite data rates
- Streams store a `24-hour` moving window of data (can be increased to `365 days` with additional cost)
- Data is stored on a stream via Kinesis Data Records (max size of `1MB`) 
- Streams are composed of one or many shards (each shard has a 1MB ingestion and 2MB consumption)
- Multiple `Consumers` access data from that moving window

> [!NOTE]
> Great for **data ingestion**, analytics, dashboards, monitoring, App clicks, etc.

## 🎛️ Data Firehose

Once data have passed the 24-hour window, they're deleted. Firehose can save the data somewhere else 🙌

- Fully managed service to load data for **data lakes**, **data stores** and **analytics services**
- Automatic scaling, fully serverless and resilient
- Supports **DATA TRANSFORMATION** (with Lambda)
- **NEAR** realtime delivery: even if Firehose receives data in realtime, it's not a realtime data service (data is buffered for delivery)
- Delivery happens when buffer size is `1MB` or buffer interval is `60sec`
- For realtime delivery, we will need to implement a custom logic from Kinesis Data Stream
- Billed based on data volume (cost-effective)

Can deliver data (transformed or not) to:

- HTTP
- Splunk
- Redshift (uses S3 as an intermediate)
- ElasticSearch
- S3

> [!NOTE]
> Producers can send records to data streams or directly at Firehose. Firehose can also read from a data stream as a consumer.

## 📊 Data Analytics

Process and analyze realtime streaming data using standard SQL.

- Ingests from Kinesis Data Streams or Firehose (optionally pull in static reference data from S3 to enrich the streaming input)
- Application code **processes Input** and **produces Output**
- Destination to Kinesis Data Streams, Firehose or Lambda

> [!NOTE]
> Useful for **REALTIME** time-series analytics, dashboards or metrics

## 🎞️ Video Stream

Ingest live video data from Producers: security cameras, smartphones, cars, drones, time-serialised audio, thermal, depth and radar data (1 Kinesis Video Stream per camera).

- Consumers can access data frame by frame... or as needed
- Can **persist** and **encrypt** data (in-transit or at rest) data
- Integrates with other AWS services (e.g. Rekognition and Connect)
- Can be used for event-driven video analytics workflow

> [!IMPORTANT]
> CANNOT access data directly via storage (EBS, EFS, S3): only via service APIs
