# Resilence4J
> Used for handaling errors,service_down and make application robust. 

## Patterns of Resilence
### 1) TimeLimiter / TimeOut :
> Limits the time being spent on calling other services.
#### Configs:
```
resilience4j.timelimiter:
    instances:
        backendA:
            timeoutDuration: 2s
            cancelRunningFuture: true
```
> Add @TimeLimiter annotation on the method which is calling another service.
```
@TimeLimiter(name = "GetProducts")
public String fetchProducts() {
    webclient call
}
```
Usage:
-Preventing Thread Starvation

### 2) Circuit Breaker Pattern :
> Monitors number of failures or errors occured within a time period and compares it aginst configured threshold, based on the result request redirection or required reponse can be send. 

![Resilience4J](/resilience4j_flowChart.webp)

```
resilience4j.circuitbreaker:
    instances:
        backendA:
            registerHealthIndicator: true
            slidingWindowSize: 100
        backendB:
            registerHealthIndicator: true
            slidingWindowSize: 10
            permittedNumberOfCallsInHalfOpenState: 3
            slidingWindowType: TIME_BASED
            minimumNumberOfCalls: 20
            waitDurationInOpenState: 50s
            failureRateThreshold: 50
            eventConsumerBufferSize: 10
            recordFailurePredicate: io.github.robwin.exception.RecordFailurePredicate
// More configs can be added..
```
Usage:
-Rate Limiting and Throttling
-Dependency Failures
-Load Shedding

### 3) Retry Pattern : 
> In case of resource(service) failure we can use Retry pattern to call same service multiple times to get the expected output.
> we can mentioned for each errors we want to call the service again.

```
resilience4j.retry:
    instances:
        backendA:
            maxAttempts: 3
            waitDuration: 10s
            enableExponentialBackoff: true
            exponentialBackoffMultiplier: 2
            retryExceptions:
                - org.springframework.web.client.HttpServerErrorException
                - java.io.IOException
            ignoreExceptions:
                - io.github.robwin.exception.BusinessException
```
> Add @Retry annotation on the method which is calling another service.
```
@Retry(name = "GetProducts")
public String fetchProducts() {
    webclient call
}
```
Usage: 
-Temporary Resource Exhaustion
-Rate Limiting
-Dependency Services Recovery

### 4) Rate Limiter Patter :
> This pattern can be used to limit number of request within a specific time frame.
```
resilience4j.ratelimiter:
    instances:
        backendA:
            limitForPeriod: 10
            limitRefreshPeriod: 1s
            timeoutDuration: 0
            registerHealthIndicator: true
            eventConsumerBufferSize: 100
```
> Add @RateLimiter annotation on the method which is calling another service.
```
@RateLimiter(name = "GetProducts")
public String fetchProducts() {
    webclient call
}
```
Usage:
-DoS (Denial of Service) and DDoS (Distributed Denial of Service) attacks
-Web/data scraping
-Brute force attacks
