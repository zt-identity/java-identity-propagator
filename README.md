# java-identity-propagator
A library to automatically propagate the end user identity from the incoming APIs in one 
Java service to other Java services in micro services world. 

The fundamental idea is:
1. Read the "Authorization" header or other user identity related headers in the incoming API requests
2. Propagate the identity across different threads in the Java service using BCI (Byte Code Instrumentation)
3. Send the identity in the headers of outgoing requests automatically.

As you can tell, this whole idea draws a lot of inspiration from the distributed tracing and
APM (Application Performance Monitoring) Java agents. In fact, we have even contemplated doing 
this in opentelemetry-java itself but that not necessarily is a good idea.
See https://github.com/open-telemetry/opentelemetry-java/discussions/2419 for more details on that.

## Why propagate the identity across services?
Typically, the end-user is authenticated at the API gateway level in the microservices/APIs
world and after that the rest of the internal backend calls may not always carry the end-user
context. Very often, either the user's role is used or some coarse grained system/service account
is used to make the upstream calls. However, that's not good because that potentially makes your
service vulnerable to some previlege escalation and IDOR attacks.

The right architecture is to propagate the actual end-user's identity across all internal 
microservices and follow the zero-trust principles. Each microservice should check whether 
the identity is valid and if the user is really authorized to perform the operation.
