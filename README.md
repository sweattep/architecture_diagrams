## A Production system I've deployed on AWS
```
                   +----------------------+
                   |      API Gateway     |
                   |  (webhooks ingress)  |
                   +----------+-----------+
                              |
                              v
+---------------------+   receives events   +----------------------+
|   External Systems  | ------------------> |        SQS Queue     |
| (webhooks, clients) |                     |   (queue messages)   |
+---------------------+                     +----------+-----------+
                                                      |
                                                      v
                                      +-------------------------------+
                                      |         Step Functions        |
                                      |   (automation + workflows)    |
                                      +-------------------------------+
                                                      ^
                                                      |
                                      +----------------+----------------+
                                      |     EventBridge Scheduler      |
                                      | (timed triggers / cron events) |
                                      +--------------------------------+
```

## Structure of IaC for multiple environments
```
                 GITHUB REPOSITORY
        -------------------------------------
        |   develop   |     qa     |  main  |
        | (branch)    | (branch)   |(prod)  |
        -------------------------------------
               |             |           |
               |             |           |
               v             v           v
        +-------------+ +-------------+ +-------------+
        |  CI/CD VAR  | |  CI/CD VAR  | |  CI/CD VAR  |
        | ENV=develop | | ENV=qa      | | ENV=prod    |
        +------+------+ +------+------+ +------+------+
               |             |           |
               |             |           |
               v             v           v
        ===============================================
                     YAML PIPELINE (single file)
        ===============================================
        |  environment: $ENV                               |
        |                                                  |
        |  if ENV=develop  → run develop jobs              |
        |  if ENV=qa       → run QA jobs                   |
        |  if ENV=prod     → run production jobs           |
        ===============================================
               |             |           |
               |             |           |
               v             v           v
        +-------------+ +-------------+ +-------------+
        | DEVELOP ENV | |   QA ENV    | | PROD ENV    |
        |  (runtime)  | |  (runtime)  | | (runtime)    |
        +-------------+ +-------------+ +-------------+
```

## Production Incident I've Handled
```
                    +----------------------+
                    |      Users Login     |
                    |    (authentication)  |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |     Login Service    |
                    |   (API / backend)    |
                    +----------+-----------+
                               |
                               v
        -------------------------------------------------
        |                INCIDENT OCCURS                |
        -------------------------------------------------
                               |
                               v
                    +----------------------+
                    |   DDoS Attack Hits   |
                    |  (traffic overload)  |
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |   Login Fails /      |
                    |   Not Working        |
                    +----------+-----------+
                               |
                               v
        -------------------------------------------------
        |                ROOT CAUSE FOUND               |
        -------------------------------------------------
                               |
                               v
                    +----------------------+
                    |   Excessive traffic  |
                    |   overwhelms service |
                    +----------------------+
                               |
                               v
        -------------------------------------------------
        |                SOLUTION APPLIED               |
        -------------------------------------------------
                               |
                               v
                    +----------------------+
                    |     Add WAF Rules    |
                    | (block DDoS patterns)|
                    +----------+-----------+
                               |
                               v
                    +----------------------+
                    |   Login Restored     |
                    |   System Stable      |
                    +----------------------+
```

## Security Improvement I've Made in an AWS service 
```
                    +----------------------+
                    |   Incoming Phone #   |
                    |   (SMS submission)   |
                    +----------+-----------+
                               |
                               v
                +-------------------------------+
                | Extract Country Calling Code  |
                |   e.g., +1, +1-787, +1-939    |
                +-------------------------------+
                               |
                               v
        ---------------------------------------------------------
        |                 COUNTRY CODE VALIDATION               |
        ---------------------------------------------------------
                               |
                               v
        +-------------------------------------------------------+
        |  Is country code +1 (United States)?                  |
        +----------------------+--------------------------------+
                               | yes
                               v
                    +----------------------+
                    |   ACCEPT NUMBER      |
                    |  (US mainland valid) |
                    +----------------------+
                               |
                               v
        ---------------------------------------------------------
        |      CHECK EXCEPTIONS FOR U.S. TERRITORIES           |
        ---------------------------------------------------------
                               |
                               v
        +-------------------------------------------------------+
        |  Is code one of the two allowed territories?          |
        |                                                       |
        |   - Puerto Rico: +1-787 / +1-939                      |
        |   - Guam: +1-671                                      |
        +----------------------+--------------------------------+
                               | yes
                               v
                    +----------------------+
                    |   ACCEPT NUMBER      |
                    | (territory allowed)  |
                    +----------------------+
                               |
                               v
        ---------------------------------------------------------
        |                     OTHERWISE                         |
        ---------------------------------------------------------
                               |
                               v
                    +----------------------+
                    |    REJECT NUMBER     |
                    | (non‑US, non‑exception)|
                    +----------------------+
```

## DevOps Automation
```
                     +----------------------+
                     |     YAML Files       |
                     |   (config / CI/CD)   |
                     +----------+-----------+
                                |
                                v
                    +---------------------------+
                    |     YAML Parsing Step     |
                    | (pipeline / application)  |
                    +-------------+-------------+
                                  |
                                  v
        ---------------------------------------------------------
        |                 PARSING ERRORS OCCUR                  |
        ---------------------------------------------------------
                                  |
                                  v
                    +---------------------------+
                    |   Malformed YAML Found    |
                    |  (indentation, spacing,   |
                    |   structure, formatting)  |
                    +-------------+-------------+
                                  |
                                  v
        ---------------------------------------------------------
        |                ROOT CAUSE IDENTIFIED                  |
        ---------------------------------------------------------
                                  |
                                  v
                    +---------------------------+
                    |   YAML not linted or      |
                    |   auto‑corrected before   |
                    |   pipeline execution      |
                    +-------------+-------------+
                                  |
                                  v
        ---------------------------------------------------------
        |                 SOLUTION IMPLEMENTED                  |
        ---------------------------------------------------------
                                  |
                                  v
                    +---------------------------+
                    |   Git hook + yamllint = :)|
                    |  (lint + auto‑fix mode)   |
                    +-------------+-------------+
                                  |
                                  v
                    +---------------------------+
                    |   Clean, Valid YAML       |
                    |   (no parsing errors)     |
                    +---------------------------+
```

## Front-End Dashboard with Responsive Design
```
                     +----------------------+
                     |     Data Source      |
                     |  (metrics / values)  |
                     +----------+-----------+
                                |
                                v
                    +---------------------------+
                    |       SVG Chart           |
                    |  (visual representation)  |
                    +-------------+-------------+
                                  |
                                  v
        ---------------------------------------------------------
        |                 ACCESSIBILITY LAYER                   |
        ---------------------------------------------------------
                                  |
                                  v
                    +---------------------------+
                    |   Invisible Data Table    |
                    |  <table aria-hidden="true">|
                    |  (semantic structure)     |
                    +-------------+-------------+
                                  |
                                  v
                    +---------------------------+
                    |   WAI-ARIA Attributes     |
                    |  (roles, labels, regions) |
                    +-------------+-------------+
                                  |
                                  v
        ---------------------------------------------------------
        |            SCREEN READERS & ASSISTIVE TECH            |
        ---------------------------------------------------------
                                  |
                                  v
                    +---------------------------+
                    |  Accessible Experience    |
                    |  - Chart visually in SVG  |
                    |  - Data read from table   |
                    +---------------------------+
```

## Deploy Frontend Code to AWS
```
                     +----------------------+
                     |   CloudFormation     |
                     |     (stack)          |
                     +----------+-----------+
                                |
                                v
        ---------------------------------------------------------
        |            RESOURCES CREATED BY THE STACK             |
        ---------------------------------------------------------
                                |
                                v
                +-------------------------------+
                |    Git => CI/CD => S3 Bucket  |
                |   (static assets / content)   |
                +-------------------------------+
                                |
                                v
                +-------------------------------+
                |       CloudFront Distribution |
                |   (CDN caching + HTTPS edge)  |
                +-------------------------------+
                                ^
                                |
        ---------------------------------------------------------
        |        STACK BINDS S3 → CLOUDFRONT ORIGIN            |
        ---------------------------------------------------------
                                |
                                v
                +-------------------------------+
                |  Origin = S3 Bucket URL       |
                |  Behaviors, TTLs, caching     |
                |  configured in CloudFront     |
                +-------------------------------+
                                |
                                v
                     +----------------------+
                     |   End Users Access   |
                     |  via CloudFront URL  |
                     +----------------------+
```
