# Rate Limiting in ASP.NET Core

This project implements different rate-limiting strategies in **ASP.NET Core** using the built-in **Rate Limiting Middleware**. The following rate-limiting techniques are covered:

- **Fixed Window Rate Limiting**
- **Sliding Window Rate Limiting**
- **Concurrent Request Limiting**
- **Token Bucket Rate Limiting**

## 🚀 Features
- Prevents excessive API usage
- Ensures fair resource distribution
- Mitigates DDoS attacks
- Implements multiple rate-limiting strategies

## 🛠 Setup and Configuration
### 1️⃣ **Install Required Packages**
Ensure you have ASP.NET Core **7.0+** since rate limiting is natively supported.

```bash
// No additional packages needed; built into ASP.NET Core 7+
```

### 2️⃣ **Enable Rate Limiting in Program.cs**
Add rate-limiting services in `Program.cs`:

```csharp
using Microsoft.AspNetCore.RateLimiting;
using System.Threading.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRateLimiter(options =>
{
    // Fixed Window: Allows X requests per time window
    options.AddFixedWindowLimiter("fixed", opt =>
    {
        opt.Window = TimeSpan.FromMinutes(1);
        opt.PermitLimit = 100; // Max 100 requests per minute
        opt.QueueLimit = 0;
    });

    // Sliding Window: Distributes requests smoothly over time
    options.AddSlidingWindowLimiter("sliding", opt =>
    {
        opt.Window = TimeSpan.FromMinutes(1);
        opt.PermitLimit = 100;
        opt.SegmentsPerWindow = 10; // More fine-grained control
        opt.QueueLimit = 0;
    });

    // Concurrent Request Limiting: Limits active requests
    options.AddConcurrencyLimiter("concurrent", opt =>
    {
        opt.PermitLimit = 5; // Only 5 concurrent requests allowed
        opt.QueueLimit = 2; // Up to 2 additional requests in queue
        opt.QueueProcessingOrder = QueueProcessingOrder.OldestFirst;
    });

    // Token Bucket: Controls request bursts efficiently
    options.AddTokenBucketLimiter("token", opt =>
    {
        opt.TokenLimit = 50; // Max tokens available
        opt.QueueProcessingOrder = QueueProcessingOrder.OldestFirst;
        opt.QueueLimit = 5;
        opt.ReplenishmentPeriod = TimeSpan.FromSeconds(10); // Add tokens every 10 sec
        opt.TokensPerPeriod = 10;
        opt.AutoReplenishment = true;
    });
});

var app = builder.Build();

app.UseRateLimiter();

app.MapGet("/fixed", () => "Fixed Window Response").RequireRateLimiting("fixed");
app.MapGet("/sliding", () => "Sliding Window Response").RequireRateLimiting("sliding");
app.MapGet("/concurrent", async () =>
{
    await Task.Delay(3000); // Simulate long processing time
    return "Concurrent Limiting Response";
}).RequireRateLimiting("concurrent");
app.MapGet("/token", () => "Token Bucket Response").RequireRateLimiting("token");

app.Run();
```

## 📊 Rate Limiting Strategies Explained

| Strategy | Description | Best For |
|----------|------------|----------|
| **Fixed Window** | Limits requests in fixed time blocks (e.g., 100 requests per minute) | APIs needing strict rate limits |
| **Sliding Window** | Adjusts limits dynamically over a rolling window | Preventing bursts, smoother control |
| **Concurrent** | Limits active requests being processed simultaneously | Preventing server overload |
| **Token Bucket** | Allows bursts of requests but refills over time | APIs with occasional high traffic |




## 🔥 Benefits of Using Rate Limiting
- **Protects APIs from abuse** 🚨
- **Ensures fair usage among users** ⚖️
- **Prevents server overloads** 🚀
- **Helps in DDoS mitigation** 🛡️

