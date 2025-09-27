# 1503-exercise.md

# Hands-On Exercise: DNS Detective Work

**Course:** DevOps and Cloud with AI   
**Module:** Foundations of Distributed Systems - Scaling Strategies: CDNs, Caches, and Load Balancers

## Introduction

This exercise takes you behind the scenes of the internet's address system. We'll use command-line tools to explore how major organizations configure their DNS infrastructure, discover hidden services, and understand how DNS enables the distributed systems you interact with daily. By the end of this exercise, you'll see DNS records the same way a DevOps engineer does - as a roadmap to understanding how systems are architected and connected.

## Prerequisites

* **Linux/Mac Users:** The `dig` command should be available by default. Open your terminal and type `dig --version` to confirm.
* **Windows Users:** You have several options:
  * **Recommended:** Use [Google Cloud Shell](https://shell.cloud.google.com/) - it's free and runs in your browser with all tools pre-installed
  * Install Windows Subsystem for Linux (WSL) and use the Linux terminal
  * Install dig through tools like Git  or Cygwin
  * Alternative: Use `nslookup` (built into Windows) - we'll show equivalent commands

## Part 1: Basic DNS Record Exploration

Let's start by exploring the fundamental DNS record types we discussed in the module.

### A Records - The Basic Name-to-IP Mapping

1. **Query a simple A record:**

   ```
   dig google.com A +short
   ```
   
   **What you'll see:** Something like `142.250.191.78`
   
   **What this means:** This is Google's IPv4 address. Every time you visit google.com, your browser connects to this IP address (or one like it).

2. **Compare with a university:**

   ```
   dig nus.edu.sg A +short
   ```
   
   **What you'll see:** An IP address like `137.132.21.30`
   
   **Why this matters:** Different organizations, different IP addresses, but the same DNS mechanism powers both.

### CNAME Records - Following the Aliases

3. **Discover aliases:**

   ```
   dig www.github.com CNAME +short
   ```
   
   **What you'll see:** Something like `github.com.`
   
   **What this reveals:** The `www.github.com` is just an alias pointing to `github.com`. GitHub only needs to manage one A record for `github.com` instead of maintaining separate IP addresses for both names.

4. **Find a more complex CNAME chain:**

   ```
   dig www.microsoft.com CNAME
   ```
   
   **What you might see:** A chain of CNAME records before finally resolving to an A record. This shows how large organizations structure their DNS for flexibility.

### MX Records - Discovering Mail Infrastructure

5. **Uncover NUS's email system:**
  
   ```
   dig nus.edu.sg MX +short
   ```
   
   **What you'll see:** Something like:
   
   ```
   10 nus.in.tmes.trendmicro.com.
   ```
   
   **Wow, look!** Now we know that Trend Micro operates the mail servers for NUS. The number `10` is the priority - lower numbers mean higher priority for mail delivery.

6. **Compare with a tech company:**
   
   ```
   dig spotify.com MX +short
   ```
   
   **What you might see:** Multiple MX records pointing to different mail providers, showing redundancy and load distribution.

### TXT Records - The Secret Metadata

7. **Discover OpenAI's verification records:**
   
   ```
   dig openai.com TXT +short
   ```
   
   **What you'll see:** Something like:
   
   ```
   "jamf-site-verification=2ysWIpJrXkO40kW1SNc8Qg"
   "v=spf1 include:_spf.google.com include:spf.protection.outlook.com include:8050860.spf04.hubspotemail.net include:mktomail.com -all"
   ```
   
   **What this reveals:** 
   - The first record proves OpenAI owns this domain to Jamf (device management)
   - The SPF record shows they use Google, Microsoft, HubSpot, and Marketo for sending emails
   - This is like a public configuration file showing which services they trust

## Part 2: DNS Detective Work on Real Organizations

Now let's use our DNS skills to understand how major organizations structure their infrastructure.

### University Infrastructure Analysis

8. **Explore your local university's setup:**

   ```
   # Try your local university or use these examples
   dig stanford.edu MX +short
   dig mit.edu TXT +short
   dig cambridge.ac.uk A +short
   ```
   
   **What you'll discover:** Universities often use different providers for different services - one company for email, another for web hosting, another for security.

### Corporate Infrastructure Patterns

9. **Analyze a major corporation:**
 
   ```
   dig tesla.com A +short
   dig tesla.com MX +short
   dig tesla.com TXT +short
   ```
   
   **What patterns emerge:** Look for:
   - Multiple A records (indicating load balancing)
   - MX records pointing to cloud providers
   - TXT records showing security and verification services

### CDN Detection

10. **Identify CDN usage:**
    
    ```
    dig cnn.com A +short
    dig media.cnn.com A +short
    ```
    
    **Compare the results:** You'll likely see different IP addresses, and the media subdomain might resolve to CDN infrastructure (though we won't name specific CDNs). This confirms what we discussed about content delivery networks.

## Part 3: Service Discovery in Action

### Subdomain Exploration

11. **Discover hidden services:**
 
    ```
    # Try these common service subdomains
    dig api.github.com A +short
    dig mail.google.com A +short
    dig blog.cloudflare.com A +short
    ```
    
    **What you're seeing:** How organizations separate different services using subdomains, each potentially hosted on different infrastructure.

### Geographic Distribution

12. **Explore geographic routing (if available):**

    ```
    dig amazon.com A +short
    # Run this command multiple times or from different locations
    ```
    
    **Note:** Some major sites return different IP addresses based on your location, demonstrating the geographic routing we discussed.

## Part 4: Windows Users - Alternative Commands

If you're using Windows `nslookup` instead of `dig`:

```
# A record lookup
nslookup google.com

# MX record lookup
nslookup -type=MX nus.edu.sg

# TXT record lookup
nslookup -type=TXT openai.com
```

**Note:** The output format is different, but you'll see the same underlying DNS information.

## Summary

Through this hands-on exploration, you've seen how DNS serves as much more than simple name resolution:

- **A records** showed you the basic building blocks of internet addressing
- **CNAME records** revealed how organizations create flexible, maintainable naming schemes  
- **MX records** uncovered the hidden email infrastructure powering organizations
- **TXT records** exposed the verification and security configurations that enable trusted communication

You've essentially performed reconnaissance on internet infrastructure, understanding how major organizations structure their systems for reliability, security, and performance. This same DNS knowledge helps DevOps engineers troubleshoot connectivity issues, plan infrastructure changes, and understand the dependencies between distributed systems.

The next time you visit a website or send an email, remember that your simple action triggers this entire DNS ecosystem working behind the scenes to connect you to the right servers across the globe.

*Pro Tip: Save interesting DNS configurations you discover - they often reveal best practices for organizing distributed systems infrastructure.*