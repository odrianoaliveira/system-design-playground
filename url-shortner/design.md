# URL Shortener Design

This document aims to list all requirements, constraints, assumptions in the scope of designing a URL shortener application.

## Requirements
### Functional
1. The applications must shortener the given URL
2. Given the shortener URL the application must redirect the user to the original URL.
3. Support custom expiration time for short URLs.

### Non-functional
1. Provide high scalability
2. The application must be responsive and provides high availability

## Constraints & Assumptions
## Volume
1. Assume billions URL to start, popular URL shorteners like TinyURL or Bitly handle vast numbers or URL.
2. 10 millions new URL per month.
3. Queries per second (QPS): 1000 read and 100 write operations.

### Storage
1. Assume each URL entry is ~500 bytes. 500Gb for a billion URLs.

## High-level Design

### Components

1. User interface
2. Load balancer: distribute the traffic across backend services.
3. Backend services: where the business logic to create and redirect shortened URL runs.
4. Database: Where the shorter and original URLs mapping are stored.
5. Cache: To fast serve frequently accessed URLs.

![System Architecture Diagram](https://raw.githubusercontent.com/odrianoaliveira/system-design-playground/refs/heads/main/url-shortner/assets/UrlShortner.drawio.png)

