---
title: "Graduation Internship Report"
date: 2026-07-10
weight: 1
chapter: false
---

<div style="border: 3px double #000; padding: 25px; margin-bottom: 30px; text-align: center; font-family: 'Times New Roman', Times, serif;">
  <p style="font-size: 15px; font-weight: bold; margin: 0; text-transform: uppercase;">Ministry of Education and Training</p>
  <p style="font-size: 16px; font-weight: bold; margin: 5px 0 0 0; text-transform: uppercase;">Ho Chi Minh City University of Technology (HUTECH)</p>
  <p style="font-size: 14px; font-weight: bold; margin: 5px 0 0 0; text-transform: uppercase;">Institute of Information Technology</p>
  
  <div style="margin: 60px 0;">
    <p style="font-size: 26px; font-weight: bold; margin: 0; text-transform: uppercase; color: #002D62;">Graduation Internship Report</p>
    <p style="font-size: 18px; font-weight: bold; margin: 15px 0 0 0; text-transform: uppercase;">Project: Deploying Serverless Infrastructure for SmartDorm on AWS platform</p>
    <p style="font-size: 16px; font-style: italic; margin: 10px 0 0 0;">Internship Organization: Amazon Web Services (AWS) Vietnam</p>
  </div>
  
  <div style="text-align: left; margin: 80px auto 40px auto; width: 85%; font-size: 15px; line-height: 1.8;">
    <table style="width: 100%; border: none; border-collapse: collapse;">
      <tr style="border: none;">
        <td style="width: 45%; font-weight: bold; border: none; padding: 3px 0;">Department:</td>
        <td style="width: 55%; border: none; padding: 3px 0;">Information Technology</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Major:</td>
        <td style="border: none; padding: 3px 0;">Cyber Security</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Academic Advisor:</td>
        <td style="border: none; padding: 3px 0;">Dinh Huynh Tue Tue</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Student Name:</td>
        <td style="border: none; padding: 3px 0;">Pham Cao Chi Thanh</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Student ID (MSSV):</td>
        <td style="border: none; padding: 3px 0;">2280602954</td>
      </tr>
      <tr style="border: none;">
        <td style="font-weight: bold; border: none; padding: 3px 0;">Class:</td>
        <td style="border: none; padding: 3px 0;">22DTHA3</td>
      </tr>
    </table>
  </div>
  
  <p style="font-size: 14px; margin-top: 60px;">Ho Chi Minh City, Year 2026</p>
</div>

---

## Acknowledgements

I would like to express my deepest gratitude to my academic advisor, **Ms. Dinh Huynh Tue Tue**, who has closely followed, patiently guided, and given me invaluable directions to complete this graduation internship report.

Although I have put in my best efforts to complete this report, limitations in my knowledge and practical experience make shortcomings unavoidable. I highly look forward to receiving feedback, criticisms, and guidelines from her to improve this report.

*Thank you very much!*

---

## Internship Organization Overview (Amazon Web Services Vietnam)

### 1. History & Development
Amazon Web Services (AWS) is the world’s most comprehensive and broadly adopted cloud platform, offering over 200 fully featured services from data centers globally. In Vietnam, AWS has set up representative offices to help enterprises, public sector organizations, educational institutions, and developers accelerate digital transformation, optimize infrastructure costs, and build modern cloud-native systems.

### 2. Core Service Areas
*   **Cloud Computing:** Offers compute runtimes (EC2, Lambda), storage (S3, EBS, EFS), secure virtual networking (VPC, CloudFront), and relational/non-relational database management (RDS, DynamoDB).
*   **Artificial Intelligence & Machine Learning:** Delivers SageMaker and Bedrock services powering next-generation Generative AI models.
*   **Cloud Talent Education:** Collaborates via AWS Academy programs and internship initiatives like the **First Cloud Journey (FCJ)** community to cultivate high-quality cloud engineering talent in Vietnam.

### 3. Work Culture at AWS Vietnam
AWS Vietnam operates under Amazon’s global leadership principles. The workplace promotes high ownership, learning curiosity, fast experimentation, and customer obsession.

---

## SECTION 3. METHODS AND TOOLS IMPLEMENTED

### 3.1. Relational Database Design using PostgreSQL on AWS RDS
To build a solid relational database for SmartDorm, PostgreSQL was deployed on Amazon RDS. The database was normalized to eliminate data redundancy and ensure referential integrity.
The Rooms table manages physical attributes of each room (base price, capacity, occupied status). The Tenants table serves as the profile registry for student identification parameters, hosting profile pictures and National ID scans. Lease agreements are captured in the Contracts table, functioning as a relational join between Rooms and Tenants with strict lease duration constraints.
To automate utility calculations, the UtilityUsages table keeps a history of monthly utility readings. Based on this, the Invoices system extracts consumption figures, computes totals based on unit pricing, adds static service fees, and generates invoices dynamically.

### 3.2. C# ASP.NET Core Web API Backend Development
The backend application was built using C# ASP.NET Core Web API (.NET 10) matching Clean Architecture principles. .NET 10 provides excellent CPU scaling and asynchronous task management (async/await), which minimizes Lambda memory consumption during serverless executions.
Entity Framework Core acts as the Object-Relational Mapper (ORM), mapping C# classes to PostgreSQL tables without writing raw SQL scripts. This speeds up coding and prevents SQL injection vulnerabilities. All lease validations, invoice mathematical formulas, and status updates are encapsulated in the Service layer to ensure high reusability.

### 3.3. Next.js Frontend Client & URL Masking Security
The client interface is developed using Next.js and TailwindCSS to deliver responsive screen layouts across mobile and desktop. Next.js connects to the C# Web API using client-side async fetch calls secured by JSON Web Tokens (JWT). The token is stored in sessionStorage and appended to Authorization headers.
To enhance cybersecurity (the student's major), client-side URL Masking was designed. Normally, exposing directory routes allows attackers to guess paths. By encrypting admin routes with hashed signatures like `/?sig=9b81d8d4&id=user`, Next.js validates these hashes before page renders, block routing for unauthorized requests.

### 3.4. Cloud Infrastructure Automation using Terraform
To achieve professional and repeatable deployments, Terraform was used for Infrastructure as Code (IaC). Terraform configuration scripts define the VPC networks, Subnets, Internet Gateways, and Route Tables.
Terraform also restricts RDS PostgreSQL access via Security Groups, allowing connections on port 5432 only within the VPC network. AWS Lambda is configured to use the custom AL2 runtime on Linux x64 for auto-scaling. Terraform provides a centralized, automated approach to cloud management, reducing human configuration errors.

---

## SECTION 4. OUTCOMES AND CONTRIBUTIONS

### 4.1. Consolidated Theoretical Concepts
The AWS internship allowed the student to apply academic principles taught at HUTECH to real-world cloud architectures:
- Gained a deep understanding of cloud networking (subnets, route propagation, and NAT routing).
- Mastered security fundamentals, configuring security groups and IAM roles under the Least Privilege principal.
- Gained solid knowledge on modern serverless deployment models (AWS Lambda) and micro-VM scaling behavior.

### 4.2. Practical Engineering Competencies Mastered
Through the mentorship of AWS solutions architects and the FCJ community, the student mastered critical tools:
- Accomplished configuration commands via AWS CLI for remote infrastructure management.
- Designed complex Terraform templates to provision networks, RDS database engines, and Lambda entrypoints.
- Bypassed Lambda's read-only disk layout constraints using S3 SDK APIs inside the C# controller.
- Resolved CORS headers issues between Vercel static frontends and AWS API Gateways.

### 4.3. Soft Skills & Professional Experience
The student matured significantly as an engineer:
- Imbibed Amazon's leadership principles of high Ownership and Learn and Be Curious.
- Collaborated in Agile sprint teams, presenting technical solutions and adopting constructive feedback.
- Developed strong troubleshooting skills under pressure. The student is proud and grateful for AWS Vietnam's sharing culture.

### 4.4. Contributions to Organization & Community
The student contributed the following deliverables:
- Built the SmartDorm system running on AWS at nearly $0/month.
- Authored a technical lab guide (Workshop) to be used as reference material for the FCJ community.
- Published a technical blog post detailing AWS Lambda disk solutions and cost-optimized RDS setups, aiding future HUTECH students.

---

## Detailed Report Contents (FCJ Requirements)

Click the links below to view the detailed work logs and technical labs of the graduation internship:

1.  **[1. Weekly Worklog](1-Worklog/)**
2.  **[2. Internship Project Proposal](2-Proposal/)**
3.  **[3. Translated Technical Blogs](3-BlogsTranslated/)**
4.  **[4. Events Participated](4-EventParticipated/)**
5.  **[5. Technical Lab Workshop](5-Workshop/)**
6.  **[6. Self-Evaluation Matrix](6-Self-evaluation/)**
7.  **[7. Program Feedback & Reflection](7-Feedback/)**
