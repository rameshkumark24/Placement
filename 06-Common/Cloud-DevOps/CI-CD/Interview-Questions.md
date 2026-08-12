CI/CD Interview Notes — RameshKumar Kannan

   ✅ CI/CD Interview Questions — Freshers (Q & A)

  1. State differences between a Docker image and a container.  
    Docker Image:   Immutable template (filesystem snapshot + instructions) created from a  Dockerfile . Used to create containers.
    Docker Container:   A running instance of an image that consumes CPU/memory; the live environment (instance) created from an image.
    Key points:   Images are immutable and logical; containers are runtime and real. Build images with  docker build  (Dockerfile); run containers from images.

  2. What is CI/CD pipeline?  
    CI/CD =  Continuous Integration  +  Continuous Delivery/Deployment .
    It automates building, testing, and delivering code changes to speed up reliable releases and reduce manual errors.

  3. Explain Docker.  
    Docker is a containerization platform that packages an application and its dependencies into containers so it runs consistently across environments.

  4. What does containerization mean?  
    Packaging application code + runtime + libraries + dependencies into an isolated container so it runs reliably across different environments.

  5. Describe the build stage.  
    Build stage compiles code, installs dependencies, runs static checks, and produces artifacts (binaries or Docker images). Validates that the project builds and is testable.

  6. What is the importance of DevOps?  
    DevOps improves release speed, automates repetitive tasks, enhances collaboration between dev & ops, reduces errors, and enables continuous improvement and faster feedback.

  7. Can you explain the Git branch?  
    A Git branch is an independent line of development used to work on a feature or fix without affecting the main codebase.

  8. What do you mean by Git Repository?  
    A Git repository stores project files and their full change history, enabling versioning and collaboration.

  9. Explain Git.  
    Git is a distributed version control system that tracks changes, supports branching & merging, and facilitates collaborative development.

  10. What is Version Control?  
    Version control tracks and manages changes to code/files using tools like Git, SVN, Mercurial, etc., enabling history, rollback, and collaboration.

  11. Does CI/CD require any programming knowledge?  
    Not strictly — GUI-based CI tools exist — but scripting/ programming helps build robust pipelines and custom automation steps.

  12. What are some popular CI/CD tools?  
    Jenkins, CircleCI, GitHub Actions, GitLab CI, Bamboo, TeamCity, Codefresh.

  13. State difference between CI/CD vs DevOps.  
    CI/CD:   Automation practices for integrating, testing, and delivering/deploying code.
    DevOps:   Culture + practices + tools that include CI/CD, monitoring, collaboration, and full lifecycle automation.

  14. What is a CI/CD Engineer?  
    A CI/CD engineer designs, implements, and maintains automation pipelines and tooling for build, test, and release processes.

  15. Explain the benefit of the CI/CD Pipeline.  
    Faster feedback, smaller & safer releases, fewer manual errors, easy rollback, continuous testing, improved quality, and reduced MTTR (mean time to resolution).

  16. Explain Continuous Integration, Continuous Delivery, and Continuous Deployment.  
    Continuous Integration (CI):   Merge code frequently and run automated builds/tests.
    Continuous Delivery:   Every change is automatically prepared for a release to staging/qa; production deploy is controlled/manual.
    Continuous Deployment:   Every change that passes tests is automatically deployed to production (no manual gate).

 CI/CD Interview Questions — Experienced (Selected)

  1. Describe Chef.  
    Chef is an Infrastructure-as-Code tool. Components:   Workstation   (author cookbooks/recipes),   Chef Server   (stores cookbooks),   Nodes   (machines configured by Chef).

  2. What do you mean by Rolling Strategy?  
    Rolling deployment gradually replaces old instances with new ones to update services without full downtime.

  3. Explain OpenShift Container Platform.  
    OpenShift (Red Hat) is an enterprise Kubernetes platform (PaaS) offering auto-scaling, self-healing, CI/CD integrations, and multi-language support.

  4. Can you tell me about the serverless model?  
    Serverless abstracts server management; developers deploy functions/services while the cloud provider manages infra and scaling. Billing is usage-based.

  5. What are some of the deployment strategies?  
    Blue-Green:   Two identical environments (switch traffic to new).
    Canary:   Release to a small percentage, then increase.
    Rolling:   Replace instances gradually.
    Recreate:   Stop old, start new (higher risk).

  6. How do DevOps tools work together? (Typical flow)  
    Dev → Git → CI (build/test) → config management (Ansible/Chef/Puppet) → automated tests (Selenium) → deploy → monitoring (Prometheus/Nagios) → feedback loop.

  7. What are the top testing tools in continuous testing?  
    Testsigma, Selenium, Tricentis Tosca, UFT, IBM RFT (the landscape varies).

  8. Why is Automated Testing essential for CI/CD?  
    Ensures code quality, reduces human error, enables frequent safe releases, and supports fast feedback across environments.

  9. How does testing fit into continuous integration? Is automated testing always a good idea?  
    Testing is core to CI for immediate feedback. Automated testing is essential, but choose test scope (unit/integration/e2e) to keep pipelines fast.

  10. Common CI/CD best practices  
    Use version control, automate build/tests, pin tool versions, manage secrets, use path filters, run CI on PRs, monitor pipelines, least-privilege permissions.

  11. In CI/CD, does security play an important role? How is it secured?  
    Yes. Use SAST/DAST, dependency scanning, secure secrets storage, least-privilege tokens, isolated runners, and audit logs.

  12. Difference between hosted and cloud-based CI/CD platform?  
    Hosted:   You install/manage CI server (maintenance required).
    Cloud-based:   Provider-hosted with scalability, SLAs, and less maintenance.

  13. Can a branch live for a long time?  
  Best practice: short-lived branches (hours–days) to reduce merge conflicts and keep CI benefits.

  14. Explain trunk-based development.  
  Developers merge small changes frequently into trunk/main; simplifies integration and supports continuous delivery.

   ✅ CI/CD MCQ Questions (copy-pasteable) — with Answers

1.   CI/CD full form –  

     A. Comprises Integration/Continuous Delivery
     B. Constant Integration/Constant Delivery
     C. Construct Delivery/Continuous Integration
     D. Continuous Integration/Continuous Delivery   ✅

2.   Continuously updating and improving products is more streamlined and efficient with DevOps. True or False.  

       True   ✅

3.   In the software delivery pipeline, what is the process for executing automated tests?  

     A. Continuous Integration
     B. Continuous Testing   ✅
     C. Continuous Pipeline
     D. Continuous Delivery

4.   Using the ____ method, any changes made to the code can be tested immediately.  

     A. Continuous Delivery
     B. Continuous Pipeline
     C. Continuous Integration   ✅
     D. Continuous Testing

5.   What are the classifications of Chef?  

     A. Chef Server
     B. Chef Node
     C. Chef Workstation
     D. All of the above   ✅

6.   Which is not the popular CI/CD tool?  

     A. Jenkins
     B. CircleCI
     C. Maven   ✅ (Maven is a build tool)
     D. Bamboo

7.   Which of the following are the top testing tools used in continuous testing:  

     A. Testsigma
     B. Selenium
     C. Tricentis Tosca
     D. All of the above   ✅

8.   Which of the following are used to create containers?  

     A. Docker   ✅
     B. Jenkins
     C. Jira
     D. Chef

9.   Which of the following is not a feature of continuous delivery?  

     A. Automate Everything
     B. Continuous Improvement
     C. Information Gathering   ✅ (not a core feature)
     D. Bug fixes and experiments

10.   ______ involves the use of a central repository where teammates can commit changes to files and sets of files.  

      A. Version control   ✅
      B. Central Repository
      C. Risk Filters
      D. All of the above

