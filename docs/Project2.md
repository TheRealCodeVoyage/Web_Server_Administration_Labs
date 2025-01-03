### **Project 2: High-Availability Portfolio with OAuth and GitHub Contributions**

#### **Overview:**

In this project, students will extend their portfolio from Project 1 by implementing OAuth for secure login, adding a GitHub contributions section, and deploying a high-availability setup using HAProxy and multiple Nginx instances. Each Nginx instance will act as a reverse proxy, protecting the backend (Flask or Express.js). 🔐📈

---

#### **Part 1: OAuth Integration and Portfolio Extension (30%)**

**Step 1: OAuth Integration**

- Use Google Cloud’s OAuth 2.0 to enable secure login.
- Ensure only authenticated users can:
  - Edit the "Projects" section of the portfolio. 🔑👤

**Step 2: Adding a GitHub Contributions Section**

- Embed the GitHub contributions widget from [github-contributions-widget](https://github.com/imananoosheh/github-contributions-widget).
- Use the provided CDN for efficient loading.
- Match the widget design with the portfolio. 🛠️📅

**Deliverables:**

- Updated portfolio with:
  - OAuth-secured login and editing functionality.
  - GitHub contributions calendar embedded.
- Documentation of OAuth implementation and widget integration. 📖✔️

---

#### **Part 2: High-Availability Setup with Load Balancing (30%)**

**Step 1: Preparing Nginx Instances**

- Create at least two cloud instances running Nginx to host the portfolio.
- Configure each Nginx instance as a reverse proxy to protect the backend (Flask or Express.js).

**Step 2: Configuring HAProxy**

- Install and configure HAProxy on a separate instance.
- Load balance traffic across the two Nginx instances. ⚙️🖧

**Deliverables:**

- Functional load balancing setup.
- HAProxy configuration file with explanations.
- Testing results showing traffic distribution. 📈🧪

---

#### **Part 3: Scalability, Performance, and Documentation (40%)**

**Step 1: Scalability and Performance Testing**

- Test the portfolio under simulated traffic.
- Document the performance benefits of the HAProxy setup. 🚀📊

**Step 2: Security Enhancements**

- Ensure secure handling of OAuth tokens and role-based access control.

**Step 3: Comprehensive Documentation**

- Provide a detailed report covering:
  - HAProxy and Nginx configurations.
  - OAuth implementation.
  - GitHub widget integration.
  - Scalability and performance results.

**Deliverables:**

- Updated portfolio accessible via load-balanced domain.
- Documentation with troubleshooting steps and testing results. 📚💡

---

### **Project 2 Grading Criteria: 🥇🔍**

- OAuth Integration and Portfolio Extension: 30%
- High-Availability Setup with Load Balancing: 30%
- Scalability, Performance, and Documentation: 40% 