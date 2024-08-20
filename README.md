# Resilence4J
## Circuit Breaker Pattern
> Monitors number of failures or errors occured within a time period and compares it aginst configured threshold, based on the result request redirection or required reponse can be send. 

![Resilience4J](/resilience4j_flowChart.webp)

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
