Rõ rồi 👍. Mình sẽ viết cho bạn file **`SECURITY.md`** với văn phong sinh viên, từ vựng/ngữ pháp B2, tránh AI. Nội dung sẽ ghi lại các bước **hardening Jenkins** mà bạn đã làm trong Task 2.

---

# SECURITY.md

## Jenkins Security Hardening Notes

This document explains the main security steps I applied to the Jenkins environment for Assignment 2. The goal was to reduce risks, prevent unauthorized access, and make sure only the right users and credentials are used during the pipeline runs.

### 1. Disable Anonymous Access

By default, Jenkins allows anonymous read access, which is insecure. I disabled this option in **Manage Jenkins → Security**. After this change, all users must log in before they can view jobs or run builds.

### 2. Create a Non-Admin User (ci-user)

I created a dedicated non-admin user called **ci-user**.

* This account can run builds but cannot change system settings.
* The admin account is kept only for configuration and maintenance.
* This follows the **principle of least privilege**.

### 3. Configure Matrix-Based Security

I enabled **Matrix-based security** and assigned only the required permissions to `ci-user`:

* Overall → Read
* Job → Read, Build
* Job → Workspace, Cancel (Optional)

This prevents non-admin users from editing job configurations or accessing **Manage Jenkins**.

### 4. Secure Use of Credentials

I stored sensitive values in Jenkins Credentials:

* **dockerhub** (username and password for Docker Hub)
* **snyk_token** (secret text for Snyk CLI)

The Jenkinsfile uses `withCredentials(...)` to inject them securely into the build. These values are masked in logs, so users like `ci-user` can run jobs but cannot see the actual secrets.

### 5. Enable Build Retention and Timestamps

To avoid uncontrolled log growth, I enabled the **Discard old builds** option, keeping only the last 20 builds. The Jenkinsfile also uses `options { timestamps() }` so that all logs show accurate times. This improves auditing and troubleshooting.

### 6. Audit Trail 

As an extra step, I installed the **Audit Trail plugin** to log changes and user actions. This creates a record of who performed which action and when, which is useful for accountability.

