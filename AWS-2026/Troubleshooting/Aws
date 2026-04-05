# Dealing with Network Connectivity Issues in AWS VPC

## Overview

This guide explains how to troubleshoot common network connectivity issues inside an AWS Virtual Private Cloud (VPC). In AWS, a VPC is your isolated network environment in the cloud. When connectivity breaks, the problem is usually caused by one or more misconfigured networking components.

Typical examples include:

- An EC2 instance cannot run `yum update` because it has no internet access
- An instance in one subnet cannot communicate with an instance in another subnet
- A web server is not reachable from the internet
- A private instance cannot reach external services through a NAT gateway

The key idea is simple: **network issues in AWS are usually configuration issues**.

---

## Example VPC Architecture

A typical VPC setup may include:

- A VPC with CIDR block such as `10.0.0.0/16`
- A **public subnet**
- A **private subnet**
- EC2 instances running in these subnets
- An Elastic Load Balancer (ELB / ALB)
- An Internet Gateway (IGW) for public internet connectivity
- A NAT Gateway for private subnet outbound internet access
- Route tables
- Security groups
- Network ACLs (NACLs)

Each of these components can affect traffic flow.

---

## Common Network Connectivity Problems

Here are the most common issues you may run into:

### 1. No Internet Access from an EC2 Instance
Example:
- An EC2 instance cannot download updates or install packages

Possible causes:
- Missing public IP
- No route to an Internet Gateway
- No route to a NAT Gateway
- Security group or NACL blocking traffic

### 2. Instances Cannot Talk Across Subnets
Example:
- Instance A in subnet 1 cannot ping or connect to Instance B in subnet 2

Possible causes:
- Security group rules missing
- NACL blocking inbound or outbound traffic
- Incorrect route table associations

### 3. Web Server Not Reachable from the Internet
Example:
- You deployed a web server in a public subnet, but you cannot open it in a browser

Possible causes:
- No public IP or Elastic IP
- No Internet Gateway route
- Security group does not allow HTTP/HTTPS
- NACL blocks traffic
- Application is not listening on the expected port

---


## What to Check When Troubleshooting

When debugging connectivity issues, check the path end to end. Do not randomly guess. Follow the traffic.

---

## 1. Security Groups

Security groups are **stateful** firewalls attached to instances, load balancers, and other resources.

### What to check
- Is the correct inbound port allowed?
- Is outbound traffic allowed?
- Is the source correct?
- Is the destination correct?

### Example
If your EC2 instance should allow:
- HTTP on port `80`
- SSH on port `22`

Then your security group must explicitly allow both.

If HTTP is allowed but SSH is not, SSH will fail.

### Important point
Because security groups are **stateful**, return traffic is automatically allowed when the original traffic is permitted.

---

## 2. Network ACLs (NACLs)

Network ACLs are **stateless** filters applied at the subnet level.

### What to check
- Are inbound rules allowing the traffic?
- Are outbound rules allowing the response?
- Are rule numbers correct?
- Is there an explicit deny overriding your allow?

### Example
If you allow inbound ICMP, you also need the corresponding outbound ICMP rule.

Why?

Because NACLs are **stateless**. They do not automatically allow response traffic.

### Important point
This is where many beginners get confused:

- **Security Group** = stateful
- **NACL** = stateless

That means with NACLs, you must think in **both directions**.

---

## 3. Route Tables

Route tables determine where traffic goes.

### What to check
- Is the subnet associated with the correct route table?
- Does the route table have the correct destination and target?
- Is there a default route for internet-bound traffic?

### Common routes

#### Public subnet
Usually needs:
- `0.0.0.0/0 -> Internet Gateway`

#### Private subnet
Usually needs:
- `0.0.0.0/0 -> NAT Gateway`

### Blackhole Route
If a route target shows **Blackhole**, it means the target resource no longer exists.

Example:
- The route points to an Internet Gateway
- That Internet Gateway was detached or deleted
- The route remains, but it points nowhere

This causes connectivity failure.

---

## 4. Internet Gateway and NAT Gateway

These components control internet connectivity.

### Internet Gateway (IGW)
Used for:
- Inbound internet traffic to public resources
- Outbound internet traffic from public resources

### NAT Gateway
Used for:
- Outbound internet access from private subnet resources
- Preventing inbound internet access to private instances

### What to check
- Is the Internet Gateway attached to the VPC?
- Is the NAT Gateway deployed in a public subnet?
- Does the NAT Gateway have an Elastic IP?
- Is the private subnet route table pointing to the NAT Gateway?

### Important point
A private subnet instance will not reach the internet just because a NAT Gateway exists.  
It also needs:
- Correct route table
- Proper security rules
- Working NACL rules

---

## 5. Instance Configuration

Sometimes the VPC is fine, but the instance itself is the problem.

### What to check
- Does the instance have a public IPv4 address?
- If not, does it need an Elastic IP?
- Is the operating system firewall blocking traffic?
- Is the application running and listening on the correct port?
- Is the instance in the correct subnet?

### Example
If an EC2 instance is in a public subnet but has **no public IP**, it still is not directly reachable from the internet.

A public subnet alone is not enough.

You need:
- A route to the Internet Gateway
- A public IP or Elastic IP
- Security group rules that allow the traffic

---

## Practical Troubleshooting Flow

Use this checklist in order:

### If an instance cannot access the internet:
1. Confirm the instance subnet type
2. Check whether the instance has a public IP
3. Check route table:
   - Public subnet -> IGW
   - Private subnet -> NAT Gateway
4. Verify security group outbound rules
5. Verify NACL inbound and outbound rules
6. Check whether the IGW or NAT Gateway actually exists and is healthy
7. Confirm DNS resolution and application behavior inside the instance

### If a web server is not reachable from the internet:
1. Check if the instance has a public IP or Elastic IP
2. Confirm the subnet route table has `0.0.0.0/0 -> Internet Gateway`
3. Verify security group inbound rule for HTTP/HTTPS
4. Verify NACL allows both inbound and outbound traffic
5. Confirm the web server process is running
6. Confirm the web server is listening on the expected port
7. Test with:
   - `curl`
   - `telnet`
   - browser access
   - EC2 reachability checks

### If one instance cannot talk to another:
1. Check both security groups
2. Check both subnet NACLs
3. Check route table associations
4. Confirm the target instance is listening on the right port
5. Confirm OS-level firewalls are not blocking traffic

---

## Key Concepts to Remember

### Security Groups vs NACLs

| Feature | Security Groups | Network ACLs |
|---|---|---|
| Scope | Attached to resource | Attached to subnet |
| Stateful | Yes | No |
| Return traffic automatically allowed | Yes | No |
| Rules can deny | No, only allow | Yes, allow and deny |

This difference matters a lot during troubleshooting.

---

## Typical Misconfigurations

Here are the most common mistakes:

- Security group allows HTTP but not SSH
- NACL allows inbound traffic but blocks outbound response traffic
- Public subnet route table is missing route to Internet Gateway
- Private subnet route table is missing route to NAT Gateway
- Route points to a deleted gateway and shows **Blackhole**
- EC2 instance in public subnet has no public IP
- NAT Gateway exists but is placed incorrectly or not referenced by the route table
- Application is not listening on the expected port

---

## Final Summary

When troubleshooting AWS network issues, think in layers:

1. **Instance configuration**
2. **Security groups**
3. **Network ACLs**
4. **Route tables**
5. **Internet Gateway / NAT Gateway**

Most problems happen because one of these is misconfigured.

The biggest beginner mistakes are:
- forgetting that **NACLs are stateless**
- assuming a **public subnet automatically makes an instance public**
- forgetting to verify **routes**
- ignoring whether the app is actually listening on the required port

The best troubleshooting habit is to trace the full path of traffic and check every component one by one.

---

## Suggested Commands for Troubleshooting

You can use these commands from inside an EC2 instance:

```bash
ping 8.8.8.8
curl http://example.com
curl ifconfig.me
nslookup amazon.com
ip addr
ip route
ss -tulnp
```

Useful checks in AWS:
- EC2 security groups
- Subnet NACLs
- Route tables
- Internet Gateway attachment
- NAT Gateway status
- Instance public IP assignment
- Load balancer listeners and target groups

---

## One-Line Takeaway

**In AWS networking, connectivity usually fails because traffic is blocked, misrouted, or the resource is not exposed the way you think it is.**


---

## AWS Reachability Analyzer

AWS **Reachability Analyzer** is a **static configuration analysis tool** that helps you test whether one AWS network resource can reach another resource inside your VPC environment. It analyzes the configured network path between a **source** and a **destination** and tells you either:

- the traffic path is reachable, with **hop-by-hop details**, or
- the traffic is **not reachable**, and which component is blocking it. citeturn256610search0turn256610search2turn256610search10

### The most important thing to understand

Reachability Analyzer does **not** send real packets through the network.  
It performs a **static analysis of your AWS network configuration**. That means it builds a model from your configured resources and evaluates whether a path should work based on those settings. citeturn256610search4turn256610search10

This matters because:

- It is great for finding **misconfiguration**
- It is **not** the same as a live packet capture
- It will not prove that your application itself is healthy
- It will not replace checking whether your process is listening on the right port

### What Reachability Analyzer is good for

Use it when you want to answer questions like:

- Can this EC2 instance reach that EC2 instance on a given path?
- Can a network interface reach an internet gateway?
- Can traffic pass from one subnet to another?
- Is a security group, NACL, route table, gateway, or other component blocking the path?
- Why is my resource unreachable even though the configuration looks correct at first glance?

### How it works at a high level

You choose:

- a **source**
- a **destination**

Reachability Analyzer then evaluates the configured network path between them. If a path exists, it shows the virtual path in detail. If not, it identifies the blocking component or an explanation code to help you diagnose the issue. citeturn256610search2turn256610search6turn256610search10

### What kind of resources it can analyze

AWS documents Reachability Analyzer as being able to analyze reachability between AWS resources such as:

- EC2 instances
- network interfaces
- internet gateways
- transit gateways  
and other supported networking components in a VPC path. citeturn256610search10turn256610search14

### What it helps you identify

Reachability Analyzer is extremely useful when troubleshooting these kinds of problems:

- Missing or incorrect **security group** rules
- Missing or incorrect **NACL** rules
- Wrong **route table** entries
- Incorrect or missing **gateway** targets
- Broken paths caused by general VPC configuration mistakes
- Hidden blockers that are easy to miss when checking resources one by one

### Example: EC2 in one subnet cannot reach EC2 in another subnet

Suppose:

- EC2-A is in Subnet A
- EC2-B is in Subnet B
- You expect application traffic on port 443

You can run Reachability Analyzer from the source to the destination and AWS will tell you whether that virtual path is reachable according to the configured networking components.

If it fails, the output may point you toward:

- a denied security group rule
- a missing NACL return rule
- a bad route
- another blocking network component

This is much faster than manually checking everything in random order.

### Why it is powerful

The real value is that it gives you a **path-based explanation** instead of just making you inspect each component manually.

Instead of asking:

- Is the security group correct?
- Is the route table correct?
- Is the NACL correct?

You ask a better question:

- **Can traffic actually travel from source to destination?**

That mindset is how experienced engineers troubleshoot networking.

### Limitations you should remember

Reachability Analyzer is excellent, but it is not magic.

It does **not** mean:

- your application is running
- your web server is listening
- your OS firewall is open
- your DNS name resolves the way you expect
- your service is healthy from an application perspective

It only means the **AWS network configuration path** is reachable or blocked based on the modeled configuration. citeturn256610search4turn256610search10

### Brutal truth

A lot of people misuse Reachability Analyzer by treating it like a full connectivity test. It is not.  
It is a **configuration reachability tool**, not an end-to-end application health tool.

If Reachability Analyzer says the path is reachable but your app still fails, the problem is probably one of these:

- app is not listening on the correct port
- process crashed
- wrong hostname or DNS
- TLS or certificate problem
- application-level authorization or protocol mismatch
- OS firewall or service-level issue

### When to use Reachability Analyzer

Use it when:

- you suspect a VPC networking misconfiguration
- you want a fast path analysis between two AWS resources
- you want to know exactly where the path is blocked
- you are debugging security groups, NACLs, routes, gateways, or path components

Do not rely on it alone when:

- debugging HTTP 500s
- debugging application bugs
- debugging process crashes
- debugging database authentication or TLS issues

---

## AWS Network Access Analyzer

AWS **Network Access Analyzer** is used to identify **unintended network access** in your AWS environment. Instead of testing one source-to-destination path like Reachability Analyzer, it lets you define your **network access requirements** and then checks your environment for paths that violate or match those requirements. citeturn256610search3turn256610search7turn256610search19

### The core idea

Reachability Analyzer asks:

- **Can this specific source reach this specific destination?**

Network Access Analyzer asks:

- **Do any network paths exist in my environment that match this access pattern?**
- **Do any of those paths violate what I intended?**

That is a much broader and more architectural question.

### The most important concept: Network Access Scope

Network Access Analyzer works using something called a **Network Access Scope**.

A Network Access Scope defines the traffic patterns you care about, such as:

- sources
- destinations
- path components
- traffic direction
- traffic type

Each scope contains:

- one or more **match conditions**
- zero or more **exclusion conditions** citeturn256610search19turn256610search13

In simple words, you are telling AWS:

- “Show me paths that match this kind of access”
- and optionally
- “Ignore paths that fit these exclusions”

### Why this matters

This is not just a troubleshooting feature.  
It is also a **governance**, **security review**, and **architecture validation** feature.

It helps answer questions like:

- Are any of my resources unintentionally reachable from an internet gateway?
- Do I have paths from public entry points to resources that should stay private?
- Are there unexpected ways traffic can reach sensitive network interfaces?
- Does my current network design violate my security intent?

### Amazon-created scopes

AWS provides Amazon-created or template-based scopes to help you get started faster. For example, AWS documentation describes scopes such as one that identifies inbound paths from internet gateways to network interfaces. citeturn256610search1turn256610search9

That is useful because many teams think a workload is private when in reality some route, subnet exposure, or gateway path still makes it reachable.

### How it works

Like Reachability Analyzer, Network Access Analyzer performs a **static analysis** of your AWS network configuration.  
It uses automated reasoning to analyze paths that packets could take through your AWS network and produces findings for paths that match a defined scope. It does **not** transmit packets through the network. citeturn256610search7turn256610search5

That means:

- it is good at finding **design-level exposure**
- it is good at surfacing **unintended access paths**
- it is not a live traffic capture tool

### Example: checking for unintended internet exposure

Suppose your architecture rule is:

- private application instances must **not** be reachable from the internet

You can define a Network Access Scope that looks for inbound paths from internet gateways to network interfaces.

If the analysis returns findings, that means some resource or path matches your exposure pattern and you need to inspect why.

That could reveal:

- subnet placement mistakes
- route table mistakes
- public exposure you did not intend
- misconfigured gateways
- overly open security rules

### Reachability Analyzer vs Network Access Analyzer

This is where most learners get confused, so here is the clean distinction:

| Tool | Main Question | Best Use |
|---|---|---|
| Reachability Analyzer | Can source A reach destination B? | Troubleshooting a specific connectivity path |
| Network Access Analyzer | Do any paths exist that match or violate this access requirement? | Auditing and validating network exposure at scale |

### When Reachability Analyzer is the better tool

Use Reachability Analyzer when:

- you already know the source and destination
- one specific connection is failing
- you want hop-by-hop detail for a single path
- you want to find the exact blocking component quickly

### When Network Access Analyzer is the better tool

Use Network Access Analyzer when:

- you want to review your network for unintended access
- you want to validate security architecture rules
- you want to search for classes of exposure
- you are doing design review, compliance review, or security assessment

### Brutal truth

A lot of engineers skip this kind of analysis and then act surprised when a supposedly private environment is not actually private.

That usually happens because they think in isolated components:

- “the instance is private”
- “the subnet is private”
- “the ALB is internal”

But they do not think in **full path analysis**.

Network Access Analyzer forces you to think in terms of:

- possible paths
- exposure patterns
- actual allowed access

That is a far more mature networking mindset.

### Limitations to remember

AWS documentation notes that Network Access Analyzer evaluates network paths **within the account and Region from which you run the analysis**. citeturn256610search1

Also, because it is static analysis:

- it does not test live packets
- it does not prove an app is healthy
- it does not replace application testing
- it does not replace service-level monitoring

### Practical advice for interviews and real work

If someone asks you how to troubleshoot one broken connection, mention **Reachability Analyzer**.

If someone asks you how to detect whether your environment has unintended exposure or whether your network design matches security intent, mention **Network Access Analyzer**.

That difference is what separates shallow AWS knowledge from real understanding.

---

## Added Troubleshooting Tools You Should Remember

When dealing with AWS network issues, remember these two advanced tools:

### Reachability Analyzer
Best for:
- one broken path
- source-to-destination analysis
- finding the blocking component

### Network Access Analyzer
Best for:
- auditing exposure
- validating intended access patterns
- identifying unintended reachable paths at scale

### Best mental model

Use this rule:

- **Specific broken connection** -> Reachability Analyzer
- **Broad access or exposure review** -> Network Access Analyzer
