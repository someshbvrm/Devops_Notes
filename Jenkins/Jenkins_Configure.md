
---

## **Jenkins Job Configuration – Detailed Explanation**

### **1. General Section**

This section defines the **overall behavior and identity** of your Jenkins job.

* **Enabled** –
  If checked, the job is active and can be run. If unchecked, Jenkins will skip it (useful for pausing without deleting).

* **Discard old builds** –
  Automatically cleans up older builds to save disk space. You can specify:

  * **Max # of builds** (e.g., keep only last 10 builds)
  * **Max # of days** (e.g., delete builds older than 30 days)

* **GitHub project** –
  If enabled, links the job to a GitHub repository URL. The job page will show a clickable link to the repo.

* **This project is parameterized** –
  Lets you define parameters (variables) that you can pass into the build.

  * **Example**: A “Version” parameter lets you decide which version to build when you start the job.

* **Throttle builds** –
  Restricts how many jobs of this type can run at the same time. Useful when builds are heavy and you want to avoid overloading the server.

* **Execute concurrent builds if necessary** –
  Allows multiple instances of the same job to run in parallel (useful for parameterized jobs with different values).

---

### **2. Source Code Management (SCM)**

This is where you tell Jenkins **where your code is stored** and how to pull it.

* **None** –
  No source control used (useful for jobs that just run scripts).

* **Git** –
  Connects Jenkins to a Git repository.

  * **Repository URL** – HTTPS or SSH link to your repo.
  * **Credentials** – Login details to access private repos.
  * **Branches to build** – Which branch Jenkins should check out (e.g., `main`, `develop`).
  * **Repository browser** – Lets Jenkins link directly to code lines in your repo viewer (e.g., GitHub, Bitbucket).

---

### **3. Triggers**

Defines **how and when Jenkins should start this job automatically**.

* **Trigger builds remotely (e.g., from scripts)** –
  Generates a URL and token so you can start a build from outside Jenkins (scripts, APIs, monitoring tools).

* **Build after other projects are built** –
  Runs this job after another job finishes (good for multi-stage pipelines).

* **Build periodically** –
  Runs the job on a fixed schedule using **cron syntax** (e.g., nightly builds).

* **GitHub hook trigger for GITScm polling** –
  Runs the job automatically when GitHub sends a webhook (on code push).

* **Poll SCM** –
  Jenkins checks your repo at intervals (cron syntax). If changes are found, it triggers a build.

---

### **4. Environment**

Controls the **environment and conditions** under which the build runs.

* **Delete workspace before build starts** –
  Clears all files before building to avoid leftover data from previous builds.

* **Use secret text(s) or file(s)** –
  Injects sensitive values (like API keys, passwords) into the build without hardcoding them.

* **Add timestamps to the Console Output** –
  Adds date/time to each line in the build log (helpful for debugging).

* **Inspect build log for published build scans** –
  Detects special “build scan” URLs in logs (used by Gradle/Maven integrations).

* **Terminate a build if it’s stuck** –
  Stops the build automatically if it runs longer than a set time.

---

### **5. Build Steps**

This is the **heart of the job** — what Jenkins actually does when it runs.

* Common options:

  * **Execute shell** – Runs shell commands (Linux/Unix).
  * **Execute Windows batch command** – Runs Windows commands.
  * **With Ant** – Runs Apache Ant build scripts.
  * Plugins can add more (e.g., Maven, Gradle, Docker).

* Example for building Java:

  ```bash
  mvn clean package
  ```

---

### **6. Post-build Actions**

Defines **what happens after the main build is finished**.

* Examples:

  * **Archive the artifacts** – Saves build output files (e.g., WAR, JAR) so you can download them later.
  * **Publish JUnit test result report** – Shows test results in Jenkins.
  * **Send notifications** – Email, Slack, etc., to notify team members of success/failure.
  * **Trigger other projects** – Automatically start another job after this one.

---

✅ **In short**:

* **General** → Identity & behavior of job
* **SCM** → Where to get code
* **Triggers** → When to run
* **Environment** → Setup before build
* **Build Steps** → What to do
* **Post-build** → What happens after

---
