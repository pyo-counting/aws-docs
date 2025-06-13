## Namespaces
## Metrics
### Time stamps
### Metrics retention
cloudwatch metric은 아래와 같이 보관한다.
- 60초 보다 짧은 주기를 갖는 data point는 3시간 동안 조회 가능하다. 이러한 data point는 high-resolution custom metric에 해당한다.
- 60초(1분)은 15일, 300초(5분)은 63일, 3600초(1시간)은 15개월 동안 조회 가능하다.

위와 같은 동작을 위해 cloudwatch는 data point를 수집할 때 장기 보관을 위해 집계 및 저장 동작도 수행한다. 예를 들어, 1분 주기로 데이터를 수집하면 해당 데이터는 15일 동안 1분 해상도로 조회할 수 있다. 15일이 지나면 이 데이터는 여전히 사용 가능하지만, 집계되어 5분 해상도로만 조회할 수 있다. 63일이 지나면 데이터는 추가로 집계되어 1시간 해상도로만 조회할 수 있다.

## Dimensions
## Resolution
## Statistics
## Units
## Periods
## Aggregation
## Percentiles
## Alarms