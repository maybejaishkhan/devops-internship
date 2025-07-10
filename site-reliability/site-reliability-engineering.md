# Site Reliability Engineering (SRE)

1. **SLA – Service Level Agreement** is what you **promise** to your customers or users. It's a **formal agreement** (often legal or business-level) which describes **how reliable or fast** your service should be.
   * Example: “We guarantee 99.9% uptime every month. If we fail, you get a refund.”
2. **SLO – Service Level Objective** is what you **aim for** internally to stay reliable. It's an **internal target** to keep your system healthy which helps you avoid SLA violations.
   * Example: “Our internal goal is 99.95% uptime — better than the SLA.”
3. **SLI – Service Level Indicator** is what you **actually measure** to check system health. A **specific metric** used to calculate how you're doing. Used to track uptime, latency, error rate, etc.
   * Example: “The percentage of successful API requests in the last 30 days.”
