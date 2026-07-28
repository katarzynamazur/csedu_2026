# 🔓 CVE-2023-48022 - Ray Jobs API Unauthenticated Remote Code Execution

> **⚠️ ShadowRay - Ray Dashboard / Jobs API Missing Authentication**

## 📖 Overview

**CVE-2023-48022** affects **Ray**, the open-source distributed computing framework used widely for scaling Python and ML workloads. Ray's dashboard and Jobs API accept job submissions from any network client with **no authentication or authorization** whatsoever. Anyone who can reach the dashboard port can submit arbitrary shell commands that execute directly on the Ray head node.

Anyscale (the maintainers of Ray) have stated this behaviour is **intentional** - Ray is designed to run only inside a trusted, isolated network, and the dashboard/Jobs API were never meant to be internet-facing. In practice, however, **thousands of exposed Ray dashboards** have been discovered and actively exploited in the wild since 2023, in a campaign researchers dubbed **"ShadowRay"**.

Because this is a **design/configuration issue rather than a code defect**, there is **no patched version** to upgrade to. The only real mitigations are network isolation, firewalling, and never exposing the Ray dashboard port (default `8265`) to untrusted networks.

This exercise simulates a realistic penetration test against a misconfigured Ray cluster: you'll confirm the missing authentication, submit a job to achieve remote code execution, retrieve the job's output via the Jobs API, and demonstrate the kind of sensitive data (cloud credentials, environment variables) an attacker could exfiltrate once inside. 🐍☁️

---

## 🐳 Setup

Run the vulnerable Ray cluster using Docker Compose:

```bash
docker compose -f docker-compose-cve-2023-48022.yml up
```

This spins up two services:

- **`ray-head-vulnerable`** - the vulnerable Ray head node. Its dashboard and Jobs API are exposed at **http://localhost:8265**, and the Ray internal port `6379` is also published. 🚀
- **`ray-docs`** - a local copy of the Ray API documentation (for the vulnerable version `2.9.0`), available at **http://localhost:8088**. Use this as your reference for the Jobs API - it documents the exact endpoints, request formats, and response fields you'll need for the tasks below (e.g. how to construct a job submission payload, and how to query job status/logs). 📘

---

## 📋 Tasks

### (a) 🛠️ Environment Setup

Launch the cluster using the Docker Compose command above and confirm the dashboard is reachable at **http://localhost:8265**.

Browse the local API reference at **http://localhost:8088** and find the endpoint that reports the Ray version. Use it to confirm the service is reachable - notice what (if anything) it asks you to authenticate with.

🤔 Notice that no API key, token, or login prompt was required to reach this endpoint.

---

### (b) 🎯 Confirm Unauthenticated Job Submission (RCE)

Consult **http://localhost:8088** for the Jobs API's job-submission endpoint - check the required HTTP method, path, headers, and JSON body fields (in particular `entrypoint` and `runtime_env`).

Using that documentation, craft and send your own request with **no credentials of any kind**, using an `entrypoint` command that proves code execution on the head node (for example, printing a unique marker string alongside the output of `id` and `hostname`).

The response will include a `job_id` - save it, you'll need it for the next step:

```bash
JOB_ID=<paste job_id from the response>
```

🤔 Did the command execute? What does the returned `id` and `hostname` output tell you about the privilege level the job runs under? Did the docs mention any authentication requirement at all?

---

### (c) 🧾 Retrieve Job Output via the Logs API

Look up the endpoint for fetching a job's logs in the docs at **http://localhost:8088**, using `$JOB_ID` from the previous step.

- 🤔 Confirm your marker string, user identity, and hostname appear in the output.
- This proves a fully unauthenticated round trip: **submit → execute → retrieve results**, with no login step anywhere in the chain.

---

### (d) 💥 Exfiltrate Sensitive Environment Data

Escalate the attack - many Ray clusters run on cloud infrastructure with credentials injected as environment variables on the head node. Using the same job-submission endpoint from part (b), submit a new job whose `entrypoint` enumerates environment variables (for example, filtering for cloud provider prefixes such as `AWS_`, `GCP_`, or `AZURE_`).

Then fetch the logs for this new job (using its returned `job_id`) the same way as in part (c).

Suggested techniques to explore further:

- 🔑 **Credential Harvesting** - grep for `AWS_`, `GCP_`, `AZURE_`, or other cloud provider env vars
- 📂 **Filesystem Enumeration** - run `ls -la /`, `cat /etc/passwd`, or explore mounted volumes
- 🌐 **Lateral Movement Recon** - check `ip a`, `cat /etc/hosts`, or scan for other reachable internal services

> ⚠️ **Goal:** Confirm that a single unauthenticated HTTP request can lead to full remote code execution and credential exposure on the Ray head node - with no exploit chain, no auth bypass tricks, and no code vulnerability required. The API simply does exactly what it was asked to do.

---

### (e) 🧹 Cleanup

After completing the exercise, stop and remove the containers:

```bash
docker compose -f docker-compose-cve-2023-48022.yml down -v
```

---

## 🔗 References

- [🛡️ CVE-2023-48022 - NVD Entry](https://nvd.nist.gov/vuln/detail/CVE-2023-48022)
- [🔍 ShadowRay: First Known Attack Campaign Targeting AI Workloads Actively Exploited In The Wild - Oligo Security](https://www.oligo.security/blog/shadowray-attack-ai-workloads-actively-exploited-in-the-wild)
- [📘 Ray Documentation - Security](https://docs.ray.io/en/latest/ray-security/index.html)
