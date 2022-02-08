## Fault handling with Polly
[source](https://www.youtube.com/watch?v=DSMdUvL8N30)
[code](https://github.com/binarythistle/Polly-Beginners-Guide)

###

Transient fualts: faults that exist for  short period of time
for example:
- network connection unavailable
- microservice starting up
- service refusing connection due to pool exhaustion

Retry Policy- try again and again
configuration: 
- number of retries
- interval between tries

Polly- transient resilient fault handling for .NET
You create policies in polly
- circuit breaker
- timeout 
- bulkhead isolation

policies are created in their namespace Policies, we create classes for policies for examplke:
``` cs
using System;
using System.Net.Http;
using Polly;
using Polly.Retry;

namespace RequestService.Policies
{
    public class ClientPolicy
    {
        public AsyncRetryPolicy<HttpResponseMessage> ImmediateHttpRetry { get;}
        public AsyncRetryPolicy<HttpResponseMessage> LinearHttpRetry {get;}
        public AsyncRetryPolicy<HttpResponseMessage> ExponentialHttpRetry {get;}

        public ClientPolicy()
        {
            ImmediateHttpRetry = Policy.HandleResult<HttpResponseMessage>(
                res => !res.IsSuccessStatusCode)
                .RetryAsync(10);

            LinearHttpRetry = Policy.HandleResult<HttpResponseMessage>(
                res => !res.IsSuccessStatusCode)
                .WaitAndRetryAsync(5, retryAttempt => TimeSpan.FromSeconds(3));

            ExponentialHttpRetry = Policy.HandleResult<HttpResponseMessage>(
                res => !res.IsSuccessStatusCode)
                .WaitAndRetryAsync(5, retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)));            
        }
    }
}
```

to enable polly we add service singleton client policy for DI and inject policy to controller

how to use policy:
``` cs
//var response = await client.GetAsync("https://localhost:5001/api/response/25");

var response = await _clientPolicy.ExponentialHttpRetry.ExecuteAsync(()
    => client.GetAsync("https://localhost:5001/api/response/25"));
```

always use http client with factory since it manages poool exhaustion for you!


you want to set up policies in httpclientfactory so that no policy code is present in controller

```cs
builder.Services.AddHttpClient("Test").AddPolicyHandler(
    request=> request.Method == HttpMethod.Get ? new ClientPolicy().ImmediateHttpRetry : new ClientPoolicy.RetryAfter()
)
```
potem w kodzie kontrolera 
```cs
var client = _clientfactory.CreateClient("Test");
```