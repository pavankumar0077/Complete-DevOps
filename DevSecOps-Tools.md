# DevSecOps Tools

### TRIVY 
We have different security tools like Anchore, Trivy, Dependency-Check -- Helps in finding vulnerabilities in source code, docker images 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c4f88885-a982-4854-a089-e91019803c3c)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7b8f002e-2639-4a2a-abec-6495a9a7f070)

Targets (what Trivy can scan):
--
```
Container Image I
Filesystem
Git Repository (remote)|
Virtual Machine Image
Kubernetes
AWS
```

Scanners (what Trivy can find there):
--
```
OS packages and software dependencies in use (SBOM)
Known vulnerabilities (CVEs)
IaC issues and misconfigurations
Sensitive information and secrets
Software licenses
```

DOCKER IMAGE SCAN
--
``` trivy image <image-name> or <image-id> ```
```
idrbt@idrbt:~$ trivy image 01e044ee3d9f
2023-10-18T12:36:08.379+0530	INFO	Vulnerability scanning is enabled
2023-10-18T12:36:08.379+0530	INFO	Secret scanning is enabled
2023-10-18T12:36:08.379+0530	INFO	If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2023-10-18T12:36:08.379+0530	INFO	Please see also https://aquasecurity.github.io/trivy/v0.45/docs/scanner/secret/#recommendation for faster secret detection
2023-10-18T12:36:09.598+0530	INFO	JAR files found
2023-10-18T12:36:09.598+0530	INFO	Java DB Repository: ghcr.io/aquasecurity/trivy-java-db:1
2023-10-18T12:36:09.598+0530	INFO	Downloading the Java DB...
471.87 MiB / 471.87 MiB [------------------------------------------------------------------------------------------------------------------------------------] 100.00% 2.54 MiB p/s 3m6s
2023-10-18T12:39:17.310+0530	INFO	The Java DB is cached for 3 days. If you want to update the database more frequently, the '--reset' flag clears the DB cache.
2023-10-18T12:39:17.319+0530	INFO	Analyzing JAR files takes a while...
2023-10-18T12:39:17.480+0530	INFO	Detected OS: alpine
2023-10-18T12:39:17.480+0530	INFO	Detecting Alpine vulnerabilities...
2023-10-18T12:39:17.486+0530	INFO	Number of language-specific files: 1
2023-10-18T12:39:17.487+0530	INFO	Detecting jar vulnerabilities...
2023-10-18T12:39:17.534+0530	WARN	This OS version is no longer supported by the distribution: alpine 3.9.4
2023-10-18T12:39:17.534+0530	WARN	The vulnerability detection may be insufficient because security updates are not provided

01e044ee3d9f (alpine 3.9.4)

Total: 274 (UNKNOWN: 0, LOW: 140, MEDIUM: 98, HIGH: 32, CRITICAL: 4)

┌───────────────────┬──────────────────┬──────────┬────────┬───────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library      │  Vulnerability   │ Severity │ Status │ Installed Version │ Fixed Version │                            Title                             │
├───────────────────┼──────────────────┼──────────┼────────┼───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ freetype          │ CVE-2020-15999   │ MEDIUM   │ fixed  │ 2.9.1-r2          │ 2.9.1-r3      │ freetype: Heap-based buffer overflow due to integer          │
│                   │                  │          │        │                   │               │ truncation in Load_SBit_Png                                  │
│                   │                  │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2020-15999                   │
├───────────────────┼──────────────────┼──────────┤        ├───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ krb5-libs         │ CVE-2020-28196   │ HIGH     │        │ 1.15.5-r0         │ 1.15.5-r1     │ krb5: unbounded recursion via an ASN.1-encoded Kerberos      │
│                   │                  │          │        │                   │               │ message in lib/krb5/asn.1/asn1_encode.c may lead...          │
│                   │                  │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2020-28196                   │
├───────────────────┼──────────────────┼──────────┤        ├───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libbz2            │ CVE-2019-12900   │ CRITICAL │        │ 1.0.6-r6          │ 1.0.6-r7      │ bzip2: out-of-bounds write in function BZ2_decompress        │
│                   │                  │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2019-12900                   │

```
```
Total: 274 (UNKNOWN: 0, LOW: 140, MEDIUM: 98, HIGH: 32, CRITICAL: 4)
```

Trivy Basic Commands
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f2eb4f3a-8ded-40a9-b3c3-5279e457e5bc)

We can filter the serverity 
--
```
trivy image --severity HIGH,CRITICAL sonarqube
```
Kubernetes yaml file scan
--
```
trivy fs --security-checks vuln,config kubernetes-sample/
```
Here kubernetes-sample is folder where all the yaml files are available

Save report as a json file
--
```
trivy image -f json -o results.json sonarqube 
```



