### Methods and Resources

APIs can be public or private

Invoke URL points at the API Gateway Endpoint, stage, and resource.

format : ``https:// {API Gateway Endpoint} / [stage] / [resource]

 e.g.     `` http://apifoartesmecher/dev/listPersons

- APIs can be deployed into stages


### Integrations

- Mock - used for testing, no backend involvement
- HTTP - Backend http endpoint (a.k.a http custom integration) --- needs mapping templates
- HTTP Proxy - pass to an integration unmodified, return to the client unmodified (backend needs to use supported format)
- AWS - Lets an API expose AWS Service Actions
- AWS_PROXY (LAMBDA) - Low admin overhead Lambda Endpoint

When using an API Gateway, 2 requests happen:
1. ***The initial request*** (to the api gateway)
2. ***The integration request*** (from the gateway to the backend)



***Mapping templates :***

- are used for AWS and HTTP (non proxy) integrations
- modify or rename params
- modify the body or headers
- filtering - removing what isn't needed
- uses Velocity Template Language
- Common exam scenario is REST api (on gateway) to a SOAP API


### Stages and Deployments

- changes made in an API gateway are **not LIVE**
- the current API state need to be deployed to a stage (mostly representing environments - DEV, QA, PROD)

- we can also use Stage Variables
- Integrations point at function alias using the ***ENV*** Stage Variable (ex. /dev points at DEV Alias Lambda Function)
- but then configuration changes happen ***via changes to aliases***


### OpenAPI and Swagger

![[Screenshot 2025-07-31 at 11.35.02.png]]

- we also have the option of exporting openAPI definition with postman extension


Next Chapter: [[Chapter 19 - NoSQL & DynamoDB]]

#AWS_Learning 