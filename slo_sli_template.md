# API Service

| Category     | SLI                                                                                | SLO                                                                                                     |
|------------------|----------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| Availability | Total number of successful request / total number of requests.                         | 99%                                                                                                         |
| Latency       | 90% percentile of all requests complete in less 100 ms.                               | 90% of requests below 100 ms.                                                                                |
| Error Budget | Proportion of the all failed requests against the proportion all failed requests used  | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   | Total number of successful requests by the time interval in seconds.                   | 5 RPS indicates the application is functioning.                                                              |
