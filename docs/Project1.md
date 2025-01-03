### **Project 1: Research and Deployment of a Professional Portfolio Web Server**

#### **Overview:**

In this project, students will research the Caddy web server, deploy it locally and on the cloud, and build your professional portfolio website. The portfolio will showcase your professional resume and at least two completed projects. 🌐✨

---

#### **Part 1: Research and Presentation (25%)**

**Task:**

- Research Caddy’s key features, use cases, and comparisons with Apache and Nginx.
- Deliver a 10-15 minute video presentation. 🎥📝

**Requirements:**

1. **Introduction to Caddy:**

   - What is Caddy, and why was it created?
   - Key features (automatic HTTPS, simplicity, performance, etc.).

2. **Comparison:**

   - Detailed comparison of Caddy, Apache, and Nginx:
     - Ease of installation and configuration.
     - Performance benchmarks.
     - Features: security, scalability, extensibility.
     - Use cases.

3. **Challenges and Limitations:**

   - Any challenges or limitations with Caddy compared to others.

**Deliverables:**

- Video presentation (10-15 minutes).
- Slides (clear and professional).
- Report summarizing findings with references and citations. 📑📊

---

#### **Part 2: Local Deployment and Portfolio Setup (35%)**

**Step 1: Installing Caddy Locally**

- Install Caddy on your local machine (Windows, Linux, or Mac).
- Document the process with detailed steps and screenshots. 🖥️📸

**Step 2: Building the Portfolio Website**

- **Frontend:**

  - Create a visually appealing website with the following sections:
    - **Projects:** Showcase at least two completed projects with detailed descriptions and visuals.
    - **Resume:** A professional resume section.
  - Use HTML, CSS, and JavaScript for the frontend.

- **Backend:**

  - Develop a backend using Flask or Express.js to handle login functionality and session management.
    - Session Management Guide in Flask : [Reference: geeksforgeeks.org](https://www.geeksforgeeks.org/how-to-use-flask-session-in-python-flask/)

**Step 3: Configuring Caddy to Serve Locally**

- Write a Caddyfile to serve the portfolio website locally.
- Include explanations for the configuration (e.g., reverse proxy, static file serving).

**Deliverables:**

- Screenshots of the working website (portfolio with resume and projects).
- The Caddyfile with annotations.
- Screenshots or a short video showing the website running locally. 🌟📷

---

#### **Part 3: Cloud Deployment and Domain Setup (40%)**

**Step 1: Setting Up a Cloud Instance**

- Choose AWS EC2 or DigitalOcean:
  - Launch an Ubuntu instance.
  - Configure security groups/firewalls for HTTP/HTTPS traffic. ☁️🔐

**Step 2: Installing Caddy on the Cloud Instance**

- Install and configure Caddy on the cloud instance.
- Ensure it is publicly accessible.

**Step 3: Domain Name and HTTPS Setup**

- Register a domain (e.g., via Freenom or a purchased domain).
- Point the domain to your cloud instance using DNS records.
- Enable HTTPS in the Caddyfile for automatic SSL certificate setup. 🌍🔒

**Step 4: Deploying the Portfolio Website**

- Deploy the portfolio website from Part 2 to the cloud instance.
- Test functionality and troubleshoot issues.

**Deliverables:**

- Functional portfolio accessible via the domain with HTTPS.
- Screenshots showing:
  - Domain and HTTPS configuration.
  - The portfolio website live on the domain.
- Documentation of troubleshooting steps. ✅📋

---

### **Project 1 Grading Criteria: 🏆📊**

- Research and Presentation: 25%
- Local Deployment and Portfolio Setup: 35%
- Cloud Deployment and Domain Setup: 40% 