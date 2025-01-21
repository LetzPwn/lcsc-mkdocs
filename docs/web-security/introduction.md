# Introduction

## What to expect

In the web security category, the challenges are about exploiting vulnerabilities inside web applications.
Principally, we distinguish between 2 goals a web challenge can have:

- **Server-Side**: Attack and compromise the server, that the web application is running on.  Here, the flag is usually hidden inside a file on the server. 
- **Client-Side**: Attack an admin user (an admin bot in our setup) through the web application and exfiltrate sensitive information. Here, the flag is usually hidden inside a cookie of the admin-bot.

A non-exhaustive list of some common vulnerabilities/techniques one might encounter in web challenges:

- SQL Injection \[SQLi\] (Server-side)
- Local File Inclusion (Server-side)
- Race conditions (Server-side)
- Server-Side Template Injection (Server-Side)
- Cross-Site Scripting \[XSS\] (Client-side)
- Cross-Site Request Forgery \[CORF\] (Client-side)
- Cross-Origin Ressource Sharing \[CORS\] (Client-side)
- Client-Side Template Injection \[CSTI\] (Client-side)

## Skills required (or Motivation to learn)
- Using a proxy (like Burpsuite) to intercept, view and modify HTTP Packages your browser sends and receives.
- Being able to read and understand HTML & JavaScript
- Knowing and recognize vulnerabilities
- Using `docker-compose` to build and run the challenges locally.

## If you have questions...

...feel free to ask people on Discord in the LCSC Server or me directly (#mathisfoxius)