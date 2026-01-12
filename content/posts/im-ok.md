+++
title = "Build a serverless Dead Man's Switch for $0 so my friends stop worrying"
date = '2026-01-14T12:00:00+09:00'
publishdate = '2026-01-13T00:00:00+09:00'
draft = false
+++

Recently, a friend of mine panicked because I went offline for a few days without replying to messages. They actually thought I was in danger. I realized I needed a passive way to reassure people that "I'm OK" without having to send active "I'm here" texts every single day. 

So, I built **I'm OK**, a serverless app to solve this problem, and to finally try out the Google Cloud stack.

### The Concept
The idea is simple: the app sends me a daily email. 
* **If I click the link:** The timer resets. Nothing happens.
* **If I *don't* click within my set timeframe (e.g., 24 hours):** The system assumes something is wrong and automatically emails my trusted contact alerting them that I haven't checked in.

### The Tech Stack
I wanted this to be maintenance-free and cost $0 to run. Here is the stack I chose:

* **Frontend:** Pure HTML/JS hosted on **GitHub Pages** (Free hosting).
* **Backend:** **Go (Gin)** running on **Google Cloud Run**. **Database:** **Google Cloud Firestore** 
* **Scheduler:** **Google Cloud Scheduler** to trigger the heartbeat check every hour.
* **Email:** Standard SMTP (Personal email for MVP).

### The Architecture
Here is how the pieces fit together. The Cloud Scheduler wakes up the backend hourly to check if anyone has missed their deadline, while the Frontend on GitHub Pages talks to Cloud Run to create new monitors.


### How it works under the hood
1.  **Setup:** The user visits the static site on GitHub Pages. The frontend calls the Go API on Cloud Run to create a document in Firestore with the user's email and timeout settings.
2.  **The Heartbeat:** Cloud Scheduler hits a `/heartbeat` endpoint on my Cloud Run instance every hour.
3.  **The Check:** The Go backend wakes up, queries Firestore for any users who haven't checked in recently, and calculates if they are past their warning or danger threshold.
4.  **The Alert:** If a user is late, the backend sends an email via SMTP.

### Try it out
The project is fully open source. You can host it yourself or check out the code here:
* **Repo:** [https://github.com/l-lumin/im-ok-backend](https://github.com/l-lumin/im-ok-backend)
* **Live Demo:** [https://l-lumin.github.io/im-ok/](https://l-lumin.github.io/im-ok/) 
    * *(Use the secret code `ruok` to create a monitor)*