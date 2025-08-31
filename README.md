<img width="1000" height="667" alt="image" src="https://github.com/user-attachments/assets/62911e0b-ae8e-4ccf-97fb-a3e3816ad2bc" />

My primary role is to support multiple Application Developer Teams, primarily consisting of Java developers. 

Most of our applications are Springboot.

These developers reach out to me and my team for any infrastructure-related support.

For each containerized application, we have three different environments - Dev, QA, and Prod.

Each application has a CI/CD Pipeline built for each of the three environments. We use GitLab CI and ArgoCD for CD (Continuous Delivery).

If any of the pipeline fails, especially during a production release, the developers can reach out to us to get assistance with troubleshooting the issues.

If I talk about what kind of troubleshooting I do. So, this is how the applications' DevOps CI/CD Pipeline looks:

DevOps CI/CD Pipeline Stages largely troubleshoot issues around AWS, Kubernetes (AWS EKS), Terraform, Dockerfiles, Container Image, etc.

Let's discuss these stages in detail:

Stage 1: Platform checks (AWS EKS Cluster): Let's assume that the code is being deployed to dev, then my AWS EKS Cluster in the dev account should be up and running, and the worker nodes should be in Ready state.

Stage 2: Validate (Dockerfile, Kubernetes syntax checks, policies, Snyk SAST [Static Application Security Testing]): This stage is to test whether the Dockerfile present in the repo and any Kubernetes-based YAML manifest, including helm charts, has the correct syntax. It

can also test the Kubernetes policies. We may mention that we are using a tool like Kyverno, which can restrict stuff like "Restrict creation of pods with elevated permissions or Does the pod satisfy resource limits". We have also integrated the Snyk tool within our pipeline to perform SAST on our code to identify code vulnerabilities.

Stage 3: Build Artifact (maven): A build artifact is the final packaged version of our application, ready to be deployed. It could be a .jar, .war file, .zip, .tar.gz, or even a container image. In this stage, once the code has passed the code quality check and unit tests, we initiate the build process. We use Maven to compile the source code, resolve dependencies, and generate a build artifact. This artifact becomes the core output of the build phase and is what we deploy to our dev, test, and production environments.

Stage 4: Package (build Dockerfile, Helm chart, add meta to image, push to AWS ECR)

Building a Dockerfile means to containerize the application

Adding meta to an image means adding tags to your container image: use variables like $AWS_ECR_URI:$commit_id

Build a Helm chart to prepare it for Kubernetes deployment

Push the image and the chart to AWS ECR

Stage 5: Scan container image with Snyk

Check if any of the software packages inside the image have known security issues (CVE IDs)

Check if the image has any outdated libraries or risky dependencies

Report anything suspicious, just like how antivirus software shows a list of threats

Stage 6: Promote to dev cluster

Create a config.yaml file with Terraform and Helm chart versions:

Push the config to another GitLab repo

ArgoCD detects a new Helm Chart version and starts to deploy it to the dev cluster

Run post-deployment tests if deployed successfully

Check on ArgoCD UI the status of pods, Deployments, StatefulSets, ConfigMaps, etc

Once all the stages pass and the deployment succeeds in dev, we will get a new Helm Chart with a version number. The same Helm Chart will be deployed to qa, and post-deployment, the relevant tests will be executed.

The test jobs are executed in Jenkins currently.

Then, if all tests succeed, then this same Helm Chart will be deployed to prod as well. 

Projects that I work on:

Project 1: Observability (Metrics & Logs): 

Data Source for metrics- Prometheus Mimir

Visualization - Grafana

I am responsible for creating Dashboards and Alerts for developer applications, for example: 

Dashboards:

Container-Level - CPU and Memory Utilization, container restart count, pod crashloopbackoff count, etc

Application-Level - JVM Threads count, API request counts, heap memory related, 4xx and 5xx error counts, http_server_requests count, etc

Alerts: 

Pod count for an application in dev, qa, or prod goes below 2

Pod restart count is over 5 in the last 5 minutes

Pod was seen in crashloopbackoff error in the last 5 minutes

4xx and 5xx error count goes over 5 in the last 5 minutes

Container Memory utilization goes over 95%

Project 2: Vulnerability Management:

Snyk for SAST (Static Application Security Testing): This is to identify vulnerabilities on the code level. Mostly it is to scan the pom.xml file in the repository, but other files are also scanned.

BrightSec for DAST (Dynamic Application Security Testing): This tool scans web URLs associated with various applications.

Project 3: Tool Migrations: This is being done to follow some specific security compliances and is part of cost optimization

Continuous Delivery (CD) Platform: Spinnaker to ArgoCD:-

We recently moved from Spinnaker to ArgoCD for our CD jobs. 

How did we do it? - The Platform team built the infrastructure and provided the SRE team with a set of steps to make infrastructure-level changes in the application repositories hosted on GitLab. When done correctly, the application pipelines would move from Spinnaker to ArgoCD.

Moving Infrastructure and Application metrics from DataDog to Prometheus (data source - Mimir)and Grafana (for visualization):-

Again, due to a few security compliance standards and part of cost optimization, we migrated all our metrics from DataDog to the Prometheus/Grafana toolset.

How did we do it? - Again, the platform team built the infrastructure and provided the SRE team with a set of steps to make infrastructure-level changes in the application repositories hosted on GitLab. When done correctly, the metrics (both default and any custom ones) would show up on Grafana (when the data source is chosen as Prometheus Mimir).

Moving away from Sumo Logic to Grafana Loki for log aggregation:-

The reasons are the same (security compliance and cost optimization), and we moved our logs platform from Sumo Logic to Grafana Loki.

Jenkins to GitLab CI for QA Automation jobs:-

This project is currently in progress at the time of posting this article. We are trying to move away from Jenkins (seriously, it's almost a legacy tool now) to GitLab CI to build our automation jobs.

Project 4: AWS Cost Optimization (A FinOps Project):-

This is a future project that I will be working on to save on AWS bills relevant to the following resources:

AWS EKS Clusters

AWS S3 Buckets

Orphaned EBS Volumes and EC2 Instances

Old EBS snapshots

And, a few other AWS resources

YouTube Video URL for the above: https://youtu.be/qsT8oPMhxmo
