Most services with “BFF” in their name aren’t backends for frontends at all. They’re reverse proxies wearing a better title, or shared backends that picked up a new label. And the gap between those and the real thing is exactly where teams lose the autonomy the pattern was supposed to give them.

A backend for frontend (BFF) is a server-side component built for a single interface and owned by the team that owns it. However, it isn’t worth it when only one interface talks to the backend, or when clients make similar requests. So the first step is being precise about what the pattern is.

This article walks through seven steps that take a BFF from decision to production. You’ll be able to tell a BFF that earns its keep from one that’s quietly becoming the shared backend it was meant to replace.

**Key takeaways:**

- A backend for frontend earns its complexity only when interfaces have divergent data needs and separate teams need independent deployment velocity.

- An API gateway serves cross-cutting concerns for every client, while a backend for the frontend is scoped to a single interface owned by that interface’s team.

- Choosing a backend for the frontend is an org-chart decision before a technical one, since separate frontend teams should own their BFFs.

- A service that only forwards bearer tokens without aggregating data or holding secrets is a reverse proxy, not a backend for frontend.

- Moving a BFF to the edge often slows it down, because most BFF work is bounded by compute-to-data latency rather than interface-to-compute latency.

## [Copy link to heading](#step-1:-understand-the-backend-for-frontend-\(bff\)-pattern)**Step 1: Understand the backend for frontend (BFF) pattern**

Most of the confusion teams run into starts right here. A BFF is one backend per user experience. You pair a server-side component with a single interface, and it aggregates data from downstream services and shapes the response for that one UI.

When [authentication](https://vercel.com/kb/guide/application-authentication-on-vercel) is handled on the server, the same component can also handle token exchange, so credentials never reach the browser. [Sam Newman’s framing](https://samnewman.io/patterns/architectural/bff/) is the most useful framing for teams. A BFF is the opposite of a general-purpose API backend that tries to serve every frontend at once, and that contrast is the whole point.

A shared backend forces every client onto a single negotiated contract, and the negotiation tax climbs with each additional consumer you add.

### [Copy link to heading](#how-a-bff-differs-from-an-api-gateway)**How a BFF differs from an API gateway**

An API gateway handles cross-cutting concerns for every client, such as TLS termination, JWT validation, rate limiting, and routing. A BFF is scoped to one interface and owned by that interface’s team.

The two patterns compose cleanly so that a BFF can sit behind a gateway, and they are not interchangeable. The moment a single shared backend serves web, mobile, and third-party consumers at once, every change needs cross-consumer coordination.

A BFF hands each frontend team a server-side layer it can change without asking anyone’s permission. Once you can name the one interface a backend serves, you can ask the real question, which is whether your clients are different enough to need one at all.

## [Copy link to heading](#step-2:-assess-your-client-diversity)**Step 2: Assess your client diversity**

The single best predictor for whether a BFF will pay off is how different your clients actually are.

A mobile app on a metered connection wants a lean, denormalized payload. A desktop dashboard wants the rich, related data the mobile client would choke on. When those needs genuinely diverge, a dedicated backend per frontend stops being overhead and becomes what lets each client ship its own shape.

### [Copy link to heading](#decision-signals-worth-tracking)**Decision signals worth tracking**

Before approving a BFF, look for these signals:

| Signal | BFF justified? |
| --- | --- |
| 3+ client types with different data shapes | Yes |
| Single frontend, CRUD-heavy app | No |
| Interfaces are making identical requests | No |
| Heavy microservices aggregation per user action | Yes |
| Browser client in a regulated industry needing token isolation | Yes |

For a web-only product, the math is tighter. A BFF earns the extra hop only when server-side aggregation is heavy enough to justify it. Below that line, you’re paying latency for a layer that shapes nothing. Whether you cross that line usually has less to do with your clients than with your org chart, which is the next thing to look at.

## [Copy link to heading](#step-3:-align-bff-boundaries-with-team-structure)**Step 3: Align BFF boundaries with team structure**

Most BFF boundary disputes eventually turn out to be an org chart problem wearing a technical costume. The BFF is an architectural pattern with organizational weight, and [Conway’s Law](https://martinfowler.com/bliki/ConwaysLaw.html) is the reason.

If two product teams each own a separate frontend experience, they should each own a separate BFF. Forcing them to share one recreates the exact coordination bottleneck the pattern was supposed to remove.

A useful rule is one backend, one experience, one owning team. This is because it keeps the orchestration logic next to the interface it serves and makes deployment ownership clear rather than contested.

### [Copy link to heading](#when-team-structure-shifts)**When team structure shifts**

The hard part is that org charts move faster than architecture. Leave a BFF with a team that no longer ships its frontend, and the thing rots inside a quarter, a pattern that happens often in practice.

[Decathlon](https://www.infoq.com/news/2024/03/decathlon-backend-for-frontend) made BFF a company-wide recommendation precisely so that frontend teams would own their own orchestration and aggregation logic. The ongoing cost nobody budgets for is that your boundaries have to track your teams, and that realignment is real work. Once ownership is settled, the next fight is over what you’re allowed to put inside the layer.

## [Copy link to heading](#step-4:-scope-what-belongs-in-the-bff-layer)**Step 4: Scope what belongs in the BFF layer**

Scope creep is the most common BFF failure mode, and it’s quiet. A BFF starts as a thin aggregation layer that everyone agrees on, then someone drops in a business rule because it’s convenient, and within months, it’s a general-purpose backend with a misleading name.

The discipline that keeps it healthy is narrow. Aggregation across downstream calls and response shaping for one frontend belong there, and protocol translation can live there too, as long as it stays interface-specific. Keep interface-specific logic in the BFF, abstract cross-cutting features, and leave domain logic in downstream services.

### [Copy link to heading](#the-security-dimension)**The security dimension**

This is where the distinction matters: too many services labeled “BFF” that did nothing of the sort. A real BFF earns its security value by acting as a confidential OAuth client and running the full token exchange server-side, so tokens never touch the browser.

A so-called BFF that just forwards bearer tokens is a [reverse proxy](https://vercel.com/kb/guide/vercel-reverse-proxy-rewrites-external), adding a network hop while leaving token exposure in the browser exactly where it was. If your service doesn’t aggregate and doesn’t hold secrets, you built a proxy and called it a BFF. Keeping scope tight is also what stops your BFF count from quietly multiplying, which is the next thing to plan for.

## [Copy link to heading](#step-5:-anticipate-and-govern-bff-proliferation)**Step 5: Anticipate and govern BFF proliferation**

By the time a team is running its third BFF, the next six months become predictable. Some duplication across BFFs is not a defect; it’s the pattern working as designed.

Experienced engineering teams treat it as an explicit tradeoff: duplication in exchange for a more tailored experience per client. The mistake is panicking at the first repeated line and extracting a shared runtime service, because that quietly reintroduces the coupling the BFF removed.

### [Copy link to heading](#the-four-phase-lifecycle)**The four-phase lifecycle**

This progression has repeated across enough of the teams adopting BFFs that it’s worth planning for at adoption time, not discovering in production:

1.  Shared API coordination limits team autonomy

2.  BFF adoption gives teams independent delivery control

3.  Multiple BFFs introduce duplication and raise the need for consistent auth and observability

4.  Follow-on architecture emerges, like [GraphQL Federation](https://graphql.org/learn/federation/), at a larger scale

Plan for phase three on day one. When shared logic is genuinely unavoidable, reach for versioned shared libraries before shared services, because a library doesn’t put two teams back on the same deploy schedule. Given that, the remaining question is purely operational: where each BFF should actually run.

## [Copy link to heading](#step-6:-choose-the-right-runtime-model)**Step 6: Choose the right runtime model**

Runtime placement is where most teams make their costliest mistakes, often because they optimize the wrong latency metric. You’re trading off cold-start overhead, interface-to-compute latency, standard library access, and operational burden against each other, and no single runtime wins all four.

Edge runtimes cut interface-to-compute latency and cold starts. Node.js serverless runtimes give you the full standard library and broader SDK compatibility.

### [Copy link to heading](#the-data-proximity-constraint)**The data-proximity constraint**

This is the part that catches people, and it’s the most common runtime mistake. Teams move a BFF to the edge, expecting a speedup, and get the opposite. Edge compute improves interface-to-compute latency, but most BFF work is bounded by compute-to-data latency.

An edge function that calls a centralized database can end up slower end-to-end than a serverless function sitting right next to that database. Stateless work, such as JWT verification and feature-flag routing, belongs at the edge. Data aggregation belongs close to the data.

Get that split right, and the deployment story gets a lot simpler, which is where the platform underneath you starts to matter.

## [Copy link to heading](#step-7:-deploy-bff-infrastructure-on-a-unified-platform)**Step 7: Deploy BFF infrastructure on a unified platform**

This is where you look for the right deployment platform, because the primitives you want for a BFF are the ones you’d otherwise have to bolt together by hand.

On a unified platform like Vercel:

- [Next.js](https://vercel.com/frameworks/nextjs) gives you server-side building blocks that map straight onto BFF responsibilities without a separate service to run

- [Route Handlers](https://nextjs.org/docs/app/getting-started/route-handlers) become your aggregation endpoints

- Server Components fetch from upstream sources during render, which collapses the usual HTTP round-trip between BFF and UI into one pass

- [Routing Middleware](https://vercel.com/docs/routing-middleware) runs before the cache on every request, which is exactly where auth checks, header enrichment, and client-type detection belong

Put together, these primitives let one Next.js project carry the full BFF workload, the aggregation, the auth, and the response shaping, without standing up and operating a separate service next to your frontend. What they don’t solve on their own is reaching data that isn’t exposed to the public internet, which is the next constraint to handle.

### [Copy link to heading](#private-backend-connectivity)**Private backend connectivity**

The constraint that blocks more BFF deployments than any other is reaching a backend that isn’t publicly accessible. On Vercel, these primitives run as [Fluid compute](https://vercel.com/docs/fluid-compute) functions. When your data sits behind a firewall, [Secure Compute](https://vercel.com/docs/networking/secure-compute) provides dedicated static IPs and private connectivity to backend infrastructure such as databases, internal APIs, and services running in a peered VPC.

Keep [ISR for background revalidation](https://vercel.com/docs/incremental-static-regeneration) of cached pages, and route BFF response caching through CDN response caching, and cache reusable data or computed values with Next.js data-level caching or durable remote caching where appropriate. With placement and connectivity handled, the only question left is whether the pattern was worth adopting in the first place.

## [Copy link to heading](#ship-your-bff-with-the-right-infrastructure)**Ship your BFF with the right infrastructure**

If you look across every successful BFF, the ones that paid off all shared three conditions. They had genuinely divergent interface needs, an independent team deployment, and real server-side orchestration to do.

The ones that became dead weight were adopted early for a single frontend, or let business logic leak into the aggregation layer, or grew to a count nobody was governing. The pattern itself is sound. What determines the outcome is whether your runtime and platform make the BFF a layer your frontend team owns end-to-end, or just another thing somebody has to babysit.

That’s the gap Vercel’s primitives are built to close, and here’s how:

- **Fluid compute:** Data aggregation functions with active CPU pricing that handle concurrent invocations on shared instances

- **Routing Middleware:** Request-level auth checks, client-type detection, and header enrichment that run before the cache on every request

- **Secure Compute:** Private VPC peering to Kubernetes backends, internal APIs, and databases with dedicated static IP addresses

- **ISR:** On-demand revalidation for cached page content, with BFF response caching handled through Data Cache or the CDN, depending on what you’re caching

- **Preview deployments:** Every pull request generates a production-grade URL, so BFF changes get reviewed in context before they ship

[Start a new Vercel project](https://vercel.com/new) and ship on your first `git push`, or browse [vercel.com/templates](https://vercel.com/templates) to begin from a foundation you can grow into.

## [Copy link to heading](#frequently-asked-questions-about-backend-for-frontend)**Frequently asked questions about backend for frontend**

### [Copy link to heading](#when-should-you-not-use-the-bff-pattern)**When should you not use the BFF pattern?**

Skip the BFF when a single frontend consumes your backend, when every interface makes identical requests, or when the app is CRUD-heavy with little aggregation. The [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends) identifies the first two as cases where the pattern does not apply, and in each, the extra hop buys you nothing.

### [Copy link to heading](#does-a-bff-add-latency-to-api-calls)**Does a BFF add latency to API calls?**

Yes. A BFF adds a network hop, and Newman notes that these environments are already latency-sensitive due to high network-call counts. [Netflix’s Android team](https://netflixtechblog.com/seamlessly-swapping-the-api-backend-of-the-netflix-android-app-3d4317155187) documented that for a small fraction of requests, increased latency was simply part of the trade-off.

### [Copy link to heading](#can-graphql-federation-replace-a-bff)**Can GraphQL Federation replace a BFF?**

Sometimes, but rarely on day one. GraphQL Federation addresses some of the scaling and duplication patterns that arise as BFF architectures grow, and the [official GraphQL documentation](https://graphql.org/learn/federation) covers best practices. Several teams reached for it in response to BFF sprawl, not as a starting point.

### [Copy link to heading](#who-should-own-the-bff-on-an-engineering-team)**Who should own the BFF on an engineering team?**

The frontend team that owns the interface should own its BFF. Newman’s canonical definition ties the BFF to one user experience maintained by the team that builds the UI, and [Decathlon](https://www.infoq.com/news/2024/03/decathlon-backend-for-frontend/) described running it exactly that way across its engineering teams in 2024.
