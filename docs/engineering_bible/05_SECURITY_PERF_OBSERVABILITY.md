# ENGINEERING BIBLE: SECURITY, PERFORMANCE, & OBSERVABILITY

## 1. Security Standards

### 1.1. Secrets & Environment Variables
*   **Never commit secrets.** `.env.local` must be in `.gitignore`.
*   **Management:** Use a secrets manager (e.g., AWS Secrets Manager, Vercel Env Vars, Doppler).
*   **Prefixes:** Frontend variables MUST be explicitly prefixed (e.g., `NEXT_PUBLIC_`) to expose them to the browser. Everything else remains securely on the server.

### 1.2. JWT & Cookies
*   Access Tokens (Short TTL) live in memory/state.
*   Refresh Tokens (Long TTL) live strictly in `HttpOnly`, `Secure`, `SameSite=Strict` cookies to prevent XSS exfiltration.

### 1.3. Attack Mitigation
*   **XSS (Cross-Site Scripting):** React handles output encoding automatically. `dangerouslySetInnerHTML` is strictly forbidden unless rendering sanitized Markdown via `DOMPurify`.
*   **CSRF (Cross-Site Request Forgery):** Prevented natively by `SameSite=Strict` cookies. APIs additionally verify custom Origin headers.
*   **SQL Injection:** Handled automatically by Prisma ORM's parameterized queries. Raw SQL via `prisma.$queryRaw` must use tagged template literals, NEVER string concatenation.
*   **Rate Limiting:** Global rate limit applied at the Kong API Gateway (Redis backed) based on IP and Tenant ID.
*   **File Uploads:** S3 buckets do not execute code. Files are scanned via ClamAV on the processing worker before being marked 'safe' for CDN distribution.

### 1.4. Dependency Scanning
*   Dependabot or Snyk enabled on the repo. High-severity CVEs in libraries block the CI pipeline immediately.

---

## 2. Performance Engineering

### 2.1. Performance Budgets (Core Web Vitals)
*   **LCP (Largest Contentful Paint):** < 1.2s for Public Menu, < 2.0s for Dashboard.
*   **CLS (Cumulative Layout Shift):** 0.00. Layouts must not jump. Skeletons and `aspect-ratio` CSS lock layouts in place.
*   **INP (Interaction to Next Paint):** < 100ms. Button clicks must respond instantly.

### 2.2. Bundle & Image Limits
*   **JS Bundle:** Initial route payload < 100kb (gzipped). RSCs ensure this remains low.
*   **Images:** CDN automatically negotiates format (AVIF/WebP). Client never downloads an image wider than its display container (`sizes` attribute).

### 2.3. Query Limits
*   Postgres queries must execute in < 50ms. Any query taking > 100ms triggers a Datadog APM alert for missing indexes.
*   N+1 query problems are prevented using Prisma's dataloader logic.

---

## 3. Observability Standards

### 3.1. Tracing
*   **OpenTelemetry (OTel):** Instrumentation injected at the Node/Go runtime layer.
*   **Correlation ID:** The API Gateway generates `X-Trace-Id`. This ID is injected into every subsequent log, Kafka event, and database query comment to trace a single request across the entire distributed system.

### 3.2. Metrics
*   **Four Golden Signals:** We track Latency, Traffic, Errors, and Saturation.
*   Prometheus scrapes `/metrics` endpoints.

### 3.3. Logging
*   **Format:** Strict JSON. No unstructured text logs.
*   **Levels:**
    *   `DEBUG`: Tracing logic flow (Turned off in Prod).
    *   `INFO`: Business events (e.g., "Order Placed").
    *   `WARN`: Non-fatal issues (e.g., 3rd party API took 3 seconds).
    *   `ERROR`: Uncaught exceptions, failed critical processes. Triggers alerts.

### 3.4. Alerts & Dashboards
*   **Datadog/Grafana:** Dashboards exist for DB load, API latency, and Kafka queue depth.
*   **PagerDuty:** P0 alerts (5xx error rate > 1%) page the on-call engineer at 3 AM. P2 alerts (High queue depth) ping a Slack channel during business hours.
