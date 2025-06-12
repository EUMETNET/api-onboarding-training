# Steps to publish data trough MeteoGate
https://eumetnet.github.io/meteogate-documentation/3-publishing-data/
There are 3 patterns to publis data. Today we concentrate on pattern 2 MeteoGate API Gateway.

## Pattern 1: Using a MeteoGate HVD Service
User MeteoGate community hostes services as trained in previous session
* Observations (E-SOH)
* Radar (ORD)
* Climate
* Warnings (MeteoAlarm)
  
## Pattern 2: Managed and Proxied Access Through MeteoGate API Gateway

The Data Supply component is registered with an appropriate MeteoGate API Gateway​. Currently, these are deployed on the EWC, in ECMWF and EUMETSAT runtime environments. EUMETNET Members will connect their data services via EWC irrespective of the location of their data (e.g., EWC, public cloud, on-premises).​ ​In the future, if requirements change, MeteoGate API Gateway can be implemented also on public clouds.

The data is requested from and goes through the MeteoGate API Gateway allowing:

Managed data requests and data flow e.g., authorization, access control, and rate limiting
Collection of insights on data requests and access​.
Read more about the MeteoGate API Gateway features on MeteoGate Architecture.

This pattern is appropriate for the following situations​:

There is no suitable HVD service for the type of data to be shared.
Data is hosted on EWC, or data access APIs will be hosted on EWC.
Prefer minimal IT investment while ensuring the data is discoverable, accessible, and compliant with MeteoGate requirements.
Want to benefit from improved security, efficiency, manageability, and cost-effectiveness, as MeteoGate handles security, rate limiting, and monitoring.

## Pattern 3: Direct Access
* use your own API / file hostings and adverise direct links to data
* add discovery metadata to WIS 2
* add notifications to WIS 2 (that point directly to your service)

## Exercise
1. create file in the config folder in this repository called yourinstitute.yaml
2. configure your excisting API to MeteoGate API Gateway
    * any API is ok (WMS, EDR, openapi anything goes)
    * choose rate limit (optional)
    * choose to allow anonymous and / or registered users access
    * set upstream api authentication (between MeteoGate API GW and your API)
    * set CORS header
4. push your changes to repository 

### Example 1. no limits
```
id: fi <-- create id 
version: 1.0.0
platforms: 
  - EUMETSAT
  - ECMWF
routes:
  - route:
      id: fi/edr <-- choose route
      endpoint: https://opendata.fmi.fi/edr <-- set you API endpoint
      cors: true
```

### Example 2. rate limits
```
id: fi--ratelimit
version: 1.0.0
platforms: 
  - EUMETSAT
  - ECMWF
routes:
  - route:
      id: fi/ratelimit/edr
      endpoint: https://opendata.fmi.fi/edr
      ratelimitAuth:
        requestRate:
          rate: 10
          burst: 20
        quota:
          count: 100
          time_window: 300
      ratelimitAnon:
        requestRate:
          rate: 1
          burst: 5
        quota:
          count: 50
          time_window: 300
      cors: true
```

### Example 3. upstream requires authentication
```
id: dk
version: 1.0.0
platforms: 
  - EUMETSAT
  - ECMWF
routes:
  - route:
      id: dk/edr
      endpoint: https://dmigw.govcloud.dk/v1/forecastedr
      cors: true
      apikey:
        headerParam: 'X-Gravitee-Api-Key'
        secretName: DMIAPIKEY
```

