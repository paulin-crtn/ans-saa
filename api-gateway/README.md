# 🚪 API Gateway

Public AWS Service. **Create** and **manage** APIs.

- Endpoint/entry-point for applications
- Sits between applications (clients) & integrations (services)
- Highly available, scalable, handles authorisation, throttling, caching, CORS, transformations, OpenAPI spec, direct integration, etc.
- Can connect to services/endpoints in AWS or on-premises
- HTTP APIs, REST APIs and WebSocket APIs
- `29 seconds` for any requests to API Gateway

## 👀 Overview

1. REQUEST: Authorize, Validate, Transform
2. Integrations (DDB, SNS, Lambda, etc.)
3. RESPONSE: Transform, Prepare, Return

> [!NOTE]
> CloudWatch Logs can store and manage full stage request and response logs

> [!NOTE]
> API Gateway Cache can be used to reduce the number of calls made to backend integrations and improve client performance

## 👋 Authentication

- Authenticate with `Cognito` and receive token: token verification is handled natively
- Client calls API Gateway with a Bearer token: call a custom Lambda Authorizer -> call to ID provider to check token validity

## 📍 Endpoint Types

- **Edge-Optimized**: routed to the nearest CloudFront POP (Point Of Presence)
- **Regional**: clients in the same region (does NOT use CloudFront POP)
- **Private**: endpoint accessible only within a VPC via Interface Endpoint

## 🏟️ Stages

- APIs are deployed to stages (e.g. test, prod, etc.)
- Each of these stages has its own unique endpoint URL
- Stages can be enabled for canary deployments (if done, deployments are made to the canary not the stage)

> [!NOTE]
> Stages enabled for canary deployments can be configured so a certain percentage of traffic is sent to the canary (this can be adjusted over time - or the canary can be promoted to make it the new base stage)

## 😡 Errors

### 4XX - Client Error - Invalid request on client side

- `400` - Bad Request - Generic
- `403` - Access denied (authorizer denies, WAF filtered)
- `429` - API Gateway can throttle (too many requests)

### 5XX - Server Error - Valid request, backend issue

- `502` - Bad Gateway Exception (bad output returned by lambda)
- `503` - Service Unavailable (server might be down)
- `504` - Gateway Timeout (29s limit)

## 💾 Caching

Cache is defined per stage within API Gateway.

- TTL default is `300` seconds (configurable between `0` and `3600` second)
- Can be **encrypted**
- Cache size from `500MB` to `237GB`
