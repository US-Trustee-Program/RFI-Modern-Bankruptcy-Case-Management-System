- [1.0 Background and Purpose](#10-background-and-purpose)
  - [1.1 Background](#11-background)
    - [1.1.1 United States Trustee Program Overview](#111-united-states-trustee-program-overview)
    - [1.1.2 Data Exchange for Trustees (DXTR) Overview](#112-data-exchange-for-trustees-dxtr-overview)
    - [1.1.3 Automated Case Management System (ACMS) Overview](#113-automated-case-management-system-acms-overview)
  - [1.2 Purpose and Problem Space](#12-purpose-and-problem-space)
- [2.0 Scope](#20-scope)
  - [2.1 Product Vision](#21-product-vision)
  - [2.2 Open source](#22-open-source)
  - [2.3 Anticipated Period of Performance](#23-anticipated-period-of-performance)
- [3.0 Objectives](#30-objectives)
  - [3.1 Backlog](#31-backlog)
    - [3.1.1 Preliminary user types](#311-preliminary-user-types)
    - [3.1.2 Preliminary user stories](#312-preliminary-user-stories)
    - [3.1.3 Potential Future work](#313-potential-future-work)
  - [3.2 List of Deliverables with Quality Assurance Surveillance Plan (QASP)](#32-list-of-deliverables-with-quality-assurance-surveillance-plan-qasp)
- [4.0 Contract Place of Performance and Contract Type](#40-contract-place-of-performance-and-contract-type)
- [5.0 Operating Constraints (Non-functional Requirements)](#50-operating-constraints-non-functional-requirements)
  - [5.1 Non-functional Requirements](#51-non-functional-requirements)
  - [5.2 Personnel Skills and Knowledge](#52-personnel-skills-and-knowledge)
  - [5.3 Special Clauses](#53-special-clauses)
- [6.0 Instructions and Evaluation](#60-instructions-and-evaluation)
  - [6.1 Submission Instructions](#61-submission-instructions)
  - [6.2 Instructions for Proposals](#62-instructions-for-proposals)
    - [6.2.1 Technical Submissions](#621-technical-submissions)
    - [6.2.2 Price Submissions](#622-price-submissions)
    - [6.2.3 Interviews](#623-interviews)
    - [6.2.4 Basis of Award and Evaluation Factors](#624-basis-of-award-and-evaluation-factors)

# 1.0 Background and Purpose

## 1.1 Background

### 1.1.1 United States Trustee Program Overview

The United States Trustee Program (USTP) is a component of the Department of Justice (DOJ) that acts in the public's interest to promote efficiency as well as protect and preserve the integrity of the bankruptcy system for all stakeholders – debtors, creditors, and the public. The USTP works to secure the just, speedy, and economical resolution of bankruptcy cases; monitors the conduct of parties; takes action to ensure compliance with applicable laws and procedures; identifies and investigates bankruptcy fraud and abuse; and oversees administrative functions in bankruptcy cases.

The USTP consists of an Executive Office in Washington, DC, and 21 regions with 90 field office locations nationwide.

The USTP holds responsibility for administering bankruptcy proceedings defined under the Bankruptcy Code. For example, proceedings under chapter 7 in which the assets of the debtor are liquidated; reorganization proceedings under chapter 11 for rehabilitation of the business debtor; reorganization proceedings under chapter 12 for the rehabilitation of the family farmer; and adjustment of debts of an individual with regular income under chapter 13, pursuant to which an individual can discharge debts by arranging for payments over a period, usually not exceeding 36 months.

### 1.1.2 Data Exchange for Trustees (DXTR) Overview

Under an agreement with the Administrative Office of the U.S. Courts bankruptcy courts, the USTP developed a process to download relevant docket entries and filings from the courts systems for entry into the Automated Case Management System (ACMS)—described below. This information then provides that data to other Bankruptcy Management Applications (BMA). This process is called DXTR ("Data Exchange for Trustees"). Each night, an Active State Perl script program retrieves case information using the USTP's Download Archive Delete (DAD) Service (see [Appendix A](02_Appendix_A.md)) from the U.S. Courts' Case Management/Electronic Court Filing (CM/ECF) web sites. This information is in XML format containing court docket information as well as PDFs of filed documents (~50,000 PDFs/day).

The XML files are loaded to a SQL Server database and processed. The output files are transferred via SFTP to the ACMS system, and USTP field staff is responsible for editing (where needed) and posting these transactions to ACMS.

DXTR also extracts embedded data and decodes barcode images from selected PDF files and makes this data available to the Credit Counseling/Debtor Education (CCDE) application. It also extracts data from PDFs or barcodes as XML for Trustee Uniform Final Report (TUFR) related data (Monthly Operating Reports, Post Confirmation Reports, etc.). All of these PDF files are copied to a cloud file share so they are viewable in other BMA including CaseViewer, TUFR, etc. (see [Appendix A](02_Appendix_A.md)).

#### Technical Details

- Associated Software: Active State Perl, cURL, and CutePDF, and Adobe Experience Manager (AEM)
- Database: MS SQL Server

### 1.1.3 Automated Case Management System (ACMS) Overview

ACMS has been in use since 1986 and currently houses over 33 million cases with over one million new cases per year and 150 active users. Today, ACMS is still USTP's source of truth for bankruptcy case data because this is where users can make changes to case information. ACMS helps USTP staff efficiently review the administration of bankruptcy cases for chapters 7, 11, 12, 13, and 15 and assists with USTP civil enforcement actions. ACMS allows USTP staff to identify a particular debtor's case, review the status of a case, manage civil enforcement deadlines, and monitor for possible fraud and abuse

Data is imported from DXTR or manually entered by authorized USTP system users. ACMS data includes important case-related information, such as the debtor's estate detail and the history of the hearings, reports, pleadings, appointments, fees for each case, case numbers, debtor names, debtor social security numbers, company Employee Identification Numbers (EIN), debtor alias names, debtor addresses, and debtor attorney information. ACMS also maintains case status information such as court orders, opinions, hearings, reports, pleadings, appointments, and fees along with associated dispositions. Case status reports prepared by trustees also contain information that may be entered into ACMS.

The system is built with IBM RPG 400 and is comprised of 22 databases (21 regional, 1 consolidated centralized) resident in a virtual environment. Updates to this system are regularly done to either add additional features or fix bugs.

#### Technical Details

ACMS is a legacy system written in RPG 400, an IBM proprietary language. Twenty-one regional versions of the ACMS application and database are in Infinite. Infinite is an emulated IBM mainframe environment. ACMS regional users log in and authenticate through the central server to gain access solely to their respective regional ACMS data.

Additionally, all regional ACMS databases are replicated to a centralized database, from which data is accessed by other applications. Information stored in ACMS drives other bankruptcy management applications.

- Associated Software: RPG 400, 5250 Emulator, InfiniteCloud
- Database: MS SQL Server

## 1.2 Purpose and Problem Space

USTP wants to build a modern case management system. To start, USTP envisions building a new data repository, as the foundational piece to feed user enhancements.

The program uses a non-web-based legacy case management system and multiple stovepipe web-based apps. These systems are difficult to improve; needs more timely data; have a poor, inconsistent user experience; and have data that is not easily accessible.

# 2.0 Scope

## 2.1 Product Vision

The case management system will allow users to access timely, pertinent case information to facilitate the just and efficient oversight of bankruptcy case administration.

## 2.2 Open source

USTP intends that the software delivered under this task order will be committed to the public domain. The Contractor will have to obtain USTP permission before delivering software under this task order that incorporates any software that is not free and open source. The Contractor must post all developed code to a version-control repository designated by the USTP.

## 2.3 Anticipated Period of Performance

The period of performance shall be one year, with USTP options to extend for up to two additional years. Services shall be provided, and expenses may be reimbursed for travel and other ODCs, during an initial period that begins with the date of the order and ends one year after the date of the order. USTP may, by written notice issued at least 15 days prior to expiration of the initial term, extend the period of performance for one additional year. If USTP exercises that option to extend the initial period of performance, USTP may also, by written notice issued at least 15 days prior to the expiration of that extended period of performance, again extend the period of performance for one additional year.

# 3.0 Objectives 

## 3.1 Backlog

The preliminary user stories below are the starting point for the development of software to be provided under this task order. These preliminary user stories are provided only for illustrative purposes, and do not comprise the full scope or detail of the USTP project.

USTP expects that the contractor will work closely with USTP to perform regular user research, usability testing, and to develop and prioritize a full gamut of user stories as the project progresses. Individual user stories will be refined, retracted, sub-divided, and reprioritized by the team throughout the development process. Items in the backlog must be completed by the software development team in order of priority.

USTP has designated an employee who will be empowered to serve as the Product Owner for this project. That individual has been certified by the Scrum Alliance as a Certified Scrum Product Owner (CSPO). USTP's Product Owner will set direction, make prioritization choices, build a product roadmap, consider and address the business needs of the USTP, and support the other members of the development team. The Contractor may be asked to assist USTP with product management.

To enable an overhaul of the case management system, the first focus of work will likely be on the data exchange (DXTR) with the Administrative Office of the U.S. Courts, and then reimagining what happens with data after this. The overhaul will focus on user-centered design and servicing the data it contains. We are unclear about what the best long-term data storage / servicing solution is – whether it's redesigning DXTR, using the strangler-pattern/encasement method of ACMS, or phasing out of our current, legacy system. Currently the data exchange system (DXTR) is risk prone – there is only one person capable of managing the system, the current process leads to delays in getting access to case data, and if it goes down, USTP would be unable to fulfill its mission.

USTP's current internal management and staffing levels are not adequate to allow for effective coordination with more than one development team, at least during the initial stages of the development process. If the development process goes smoothly and it appears USTP has sufficient capability, USTP may consider adding additional Contractor development team(s).

### 3.1.1 Preliminary user types

- **Auditor** – Reviews and analyzes all financial and accounting matters in bankruptcy cases.
- **Trial attorney** – Represent the USTP in bankruptcy cases.
- **Paralegals** – Assist attorneys and auditors with bankruptcy research, communication, and case filings.
- **Quarterly fee coordinator** – Oversees collection of fees in chapter 11 cases.
- **Legal assistant** – Assist USTP staff with clerical tasks.
- **Assistant U.S. Trustee** – manages the USTP staff and cases within a field location.
- **Data scientists** – review case data to provide statistics and reporting to senior management and oversight bodies.

### 3.1.2 Preliminary user stories

#### Auditor

- As an auditor, I need a list of cases that recently had a monthly operating report filed, so that I can review reports for timeliness and accuracy.
- As an auditor, I need to know when a trustee's final report is due, so that I can review the report for timeliness and accuracy or follow-up with the trustee, as needed.
- As an auditor, I need a data file of all chapter 7 cases that were opened in the last two weeks, so that I can compare them to bank statements.
- As an auditor, I would like to log any significant activity in a case, so that it can be reported to Congress accurately.
- As an auditor, I need to see what cases were filed yesterday, so that I know if any immediate action needs, such as appointing a trustee, to be taken on newly filed cases.
- As an auditor, I need to see what cases were updated yesterday, so that I know if any immediate action needs to be taken, such as appointing a trustee, on newly filed cases.
- As an auditor, I want the ability to view cases that are assigned to me, so that I don't have to search for cases or information.
- As an auditor, I need to review credit counseling and debtor education certificate numbers, so that I can check the validity of the certificates.

#### Trial attorney

- As a trial attorney, I need a calendar of all upcoming hearings for the week, so that I can be sure to attend the ones that I need to attend.
- As a trial attorney, I would like to log any significant activity in a case, so that it can be reported to Congress accurately.
- As a trial attorney, I need to look up the status of quarterly fees in a case, so that I can prepare a motion if they are delinquent.
- As a trial attorney, I need to see the debtor's schedules that were filed, so that I can review the completeness of them.
- As a trial attorney, I need to see what cases were filed yesterday, so that I know if any immediate action needs, such as appointing a trustee, to be taken on newly filed cases.
- As a trial attorney, I need to see what cases were updated yesterday, so that I know if any immediate action needs to be taken, such as appointing a trustee, on newly filed cases.
- As a trial attorney, I want the ability to view cases that are assigned to me, so that I don't have to search for cases or information.
- As a trial attorney, I need the ability to request case documents from the bankruptcy courts, so that I have all the pertinent information to prepare for responses and do not have to incur PACER charges to taxpayers.

#### Paralegal

- As a paralegal, I need to review cases that did not pass means tests, so that I can review the case more closely.
- As a paralegal, I need to log when a trustee's final report arrives in the office and when it gets approved, so that I can be sure they are processed within 30 days.
- As a paralegal, I need to get a notification within 20 days after a final report is filed and is not approved, so that I know I need to follow-up with the trustee and perform corrective actions, as needed.
- As a paralegal, I need to log a significant event on behalf of an attorney, so that it can be reported to Congress in a timely manner.
- As a paralegal, I want the ability to view cases that are assigned to me, so that I don't have to search for cases or information.
- As a paralegal, I need to review credit counseling and debtor education certificate numbers, so that I can check the validity of the certificates.

#### Quarterly fee coordinator

- As a quarterly fee coordinator, I need the ability to generate a disbursement report, so that I can review to ensure correctness of data before transmitting the file.
- As a quarterly fee coordinator, I need to receive a notification when the ARM user initiates the statement process, so that I am aware that the fee adjustments will not be made for the remainder of the day.
- As a quarterly fee coordinator, I need a list of the cases that have reporting or fee delinquencies of greater than 6 months, so that I can follow up on those cases and take the appropriate action.

#### Legal assistant

- As a legal assistant, I need to review the DXTR data for accuracy before it goes to the live ACMS system, so that events are tracked accurately.
- As a legal assistant, I need a listing of "repeat filers," how many times, and where, so that I can check for legitimacy and report findings to an attorney.

#### Oversight parties

- As a Credit Counseling and Debtor Education (CCDE) financial analyst, I need to review credit counseling and debtor education certificate numbers, so that I can match agencies with filed certificates.
- As a data scientist in the Office of Planning and Evaluation (OPE), I need to review broad types of case data (e.g., filings, bankruptcy types, motions filed, cases by office, etc.), so that I can provide statistics and reporting to senior management, provide an annual report to Congress, respond to FOIA requests, and post data publicly.

### 3.1.3 Potential Future work

- Fee collections
- View and search
- Document/content management
- Notes, messaging, and notifications
- Calendaring
- Private trustee oversight
- Reporting, analytics, auditing, and monitoring
- Time keeping system

## 3.2 List of Deliverables with Quality Assurance Surveillance Plan (QASP)

The government uses the Quality Assurance Surveillance Plan (QASP, pronounced "kwasp") to monitor the quality of the contractor's performance and deliverables throughout the contract.

Because there are many acceptable approaches to this project's objectives, this QASP provides high-level performance standards and describes their purpose.

The Contracting Officer's Representative (COR) will assess the contractor's performance against the standards of the QASP, though they may choose to delegate the assessment to another government personnel if the other staff member has relevant subject matter expertise. It's expected that assessment intervals will be frequent (i.e., every sprint).

The QASP is a living element of the contract and reflects the working standards and processes of the team. Updates to the QASP are expected and may be made by modification to the task order by mutual agreement of the government and the contractor.

Contractor deliverables may be measured against each element of the QASP at the interval listed for each element (e.g., every sprint). The contractor is expected to pass each element of the QASP when assessed to avoid unacceptable or deficient performance.

The QASP is used to inspect and accept all deliverables within each interval (typically two weeks).

| **Deliverable** | **Performance Standard(s)** | **Acceptable Quality Level** | **Method of Assessment** |
| --- | --- | --- | --- |
| Tested Code | Code delivered under the order must have substantial test code coverage.USTP version controlled code repository that comprises the product that will remain in the government domain. | Minimum of 90% test coverage of all code. All areas of code are meaningfully tested. | Combination of manual review and automated testing |
| Properly Styled Code | [GSA 18F Front-End Guide](https://frontend.18f.gov/#js-style) | 0 linting errors and 0 warnings | Combination of manual review and automated testing |
| Accessible Application | Web Content Accessibility Guidelines 2.1 AA standards | 0 errors reported using an automated scanner and 0 errors reported in manual testing | [https://github.com/pa11y/pa11y](https://github.com/pa11y/pa11y) |
| Deployed Code | Code must successfully build and deploy into the staging environment. | Successful build with a single command | Combination of manual review and automated testing |
| Documented Application | All dependencies are listed, and the licenses are documented. Major functionality in the software/source code is documented. Individual methods are documented inline in a format that permit the use of tools such as JSDoc. System diagram is provided. | Combination of manual review and automated testing, if available | Manual review |
| Secure Application | OWASP Application Security Verification Standard 3.0 | Code submitted must be free of medium- and high-level static and dynamic security vulnerabilities | Clean tests from a static testing SaaS (such as Snyk or npm audit) and from OWASP ZAP, along with documentation explaining any false positives |
| User research | Usability testing and other user research methods must be conducted at regular intervals throughout the development process (not just at the beginning or end). | Research plans and artifacts from usability testing and/or other research methods with end users are available at the end of every applicable sprint, in accordance with the contractor's research plan. | USTP will manually evaluate the artifacts based on a research plan provided by the contractor at the end of the second sprint and every applicable sprint thereafter. |

# 4.0 Contract Place of Performance and Contract Type

The Contractor may work offsite to perform the required software development services. Any work done, whether onsite or offsite, must be covered by the rate agreed upon between the government and contractor. The contractor will generally be expected to be readily available during core working hours from 11:00am to 4:00pm Eastern time Monday through Friday. They are not expected to work federal holidays.

Occasional travel to government facilities may be required. Actual travel costs to government facilities will be reimbursed in accordance with federal travel regulation. All travel must be approved by the COR prior to booking.

The government is considering the contract type for this award to be a time and material (T&M) contract. USTP will not consider quotes that include fees for licenses or subscriptions. All rates shall be fully burdened, and T&M rates are fixed at the time of award.

The offeror must specify whether the fixed hourly rate for each labor category applies to labor performed by

1. the offeror,
2. subcontractors; and/or
3. divisions, subsidiaries, or affiliates or the offeror under a common control.

# 5.0 Operating Constraints (Non-functional Requirements)

## 5.1 Non-functional Requirements

- USTP, not the Contractor, will be responsible for the hosting of the deployed System and obtaining any necessary Authority to Operate (ATO). USTP will also determine what security controls are required and whether they have been satisfied. USTP expects to provide those security controls to the Contractor as either acceptance criteria or separate user stories. The Contractor is expected to use best practices for security in delivering code.
- Use of a USTP-provided version control system.
- Use of the [U.S. Web Design System](https://designsystem.digital.gov/).
- USTP uses Azure Government Cloud, and expects development to be performed in a commercial Azure environment, only using those services (see [Appendix B](03_Appendix_B.md)) that are available within Azure Government Cloud. USTP will be responsible for creating, maintaining, and managing an internet-connected staging environment, and an internet-connected production environment, on a FedRAMP-authorized cloud storage solution. The Contractor will be responsible for creating, maintaining, and managing its own internet-connected development environment, which will be the only environment that the Contractor will deploy to. The Contractor's development environment must mirror USTP's staging and production environments, except that the Contractor's development environment need not be FedRAMP-authorized.
- The System must incorporate an intuitive web-based interface that is accessible from both internal and external platforms, including desktops, laptops, tablets, and mobile devices.
- The System architecture must incorporate Application Programming Interfaces (APIs) to intermediate major components.
- The System must have no single point of failure. The System will use elastic, dynamically allocated computing resources that accommodate changing demand in real time.

The software architecture must be extensible to allow for future development. The code base must incorporate analytics, monitoring, continuous integration, and measurement tools. Application design and development must use plain language to the extent practical.

## 5.2 Personnel Skills and Knowledge

The contractor must designate who will fill these two key personnel roles: Project Manager and Technical Lead. The roles may be filled by the same person or by different people and as a result, there should be between one to two people identified as key personnel. The person(s) fulfilling these roles must be cleared and will be provided access to USTP systems.

Key Personnel substitutions must be approved by USTP in writing, and will only be approved by USTP request, sudden illness, change of employment, or termination of employment for cause. Contractor requests for a substitution of Key Personnel must include a detailed explanation of the justifying circumstances, and a complete résumé for the proposed substitute or addition, including skills, experience, education, training, and security level. The UTSP's failure to approve a proposed substitution will not constitute grounds for non-performance by the Contractor, or form a valid basis for any claim for money or any equitable adjustment.

The **Project Manager** will be a direct liaison to the government product team. They will be responsible for ensuring a healthy and effective team, including both government and contractor personnel. This person will ensure that all contractor personnel are aligned around a set of shared team goals and have clear, actionable tasks that support those goals. This person will be the focal point for all contractor activities. This person should have a background as a product manager and familiar with iterative development methodologies. It is not expected that this person will manage the work of the development team if the development team is using a self-organizing software development methodology.

The **Technical Lead** must have a full understanding of the technical approach to be used by the contractor's team and will be responsible for ensuring that the contractor's team follows that approach. This person will ensure that the technical approach is robust, scalable, and maintainable. They will also manage technical debt. This person should have a background as a DevOps engineer, software engineer, or equivalent.

The contractor must provide a cross-functional team that is experienced in working in an iterative manner. They must be comfortable delivering value iteratively and be able to pivot quickly based on an evolving understanding of user needs. The contractor should deliver working software early in the post-award period and iteratively improve the software through ongoing development sprints.

The relevant skills for this project include:

- Iterative development practices
- User experience and product design
- User and usability research (e.g., stakeholder and user interviews, usability testing)
- Continuous integration and continuous deployment (CI/CD)
- Azure Gov cloud platform
- Automated testing (unit/integration/end-to-end)
- Wireframing, prototyping, and user task-flow development
- Software engineering (Cloud practitioner)
- GitOps/DevOps engineering

- Identity / authentication engineering
- Content design

- Web application security
- Cloud security
- Data security

## 5.3 Special Clauses

_Data Rights and Ownership of Deliverables_ – USTP intends that all software and documentation delivered by the Contractor will be owned by the USTP and committed to the public domain. This software and documentation includes, but is not limited to, data, documents, graphics, code, plans, reports, schedules, schemas, metadata, architecture designs, and the like; all new open source software created by the Contractor and forks or branches of current open source software where the Contractor has made a modification; and all new tooling, scripting configuration management, infrastructure as code, or any other final changes or edits to successfully deploy or operate the software.

To the extent that the Contractor seeks to incorporate any software that was not first produced in the performance of this task order in the software delivered under this task order, USTP encourages the Contractor to incorporate either software that is in the public domain, or free and open source software that qualifies under the Open Source Definition promulgated by the Open Source Initiative. In any event, the Contractor must promptly disclose to USTP in writing, and list in the documentation, any software incorporated in the delivered software that is subject to a license.

If software delivered by the Contractor incorporates software that is subject to an open source license that provides implementation guidance, then the Contractor must ensure compliance with that guidance. If software delivered by the Contractor incorporates software that is subject to an open source license that does not provide implementation guidance, then the Contractor must attach or include the terms of the license within the work itself, such as in code comments at the beginning of a file, or in a license file within a software repository.

# 6.0 Instructions and Evaluation

## 6.1 Submission Instructions

The offeror must submit their information in this Google Form. You are limited to X, Y, and Z. 

## 6.2 Instructions for Proposals

### 6.2.1 Technical Submissions

Technical submissions must consist of a technical proposal of no more than four (4) pages, a staffing plan of no more than three (3) pages plus resumes and signed letters by key personnel of intent to participate, and references to one or more source code samples, preferably open source. Technical submissions may also include user research plans and design artifacts of no more than 10 pages combined. Technical proposals and staffing plans must be submitted using 12-point type.

The technical proposal must set forth the Offeror's proposed approach to providing the services required, including the base software (if any) and programming language(s) the Offeror proposes to use. The technical proposal must also make clear that the Offeror understands the details of the project requirements. The technical proposal must also identify potential obstacles to efficient development and include plans to overcome those potential obstacles. The technical proposal must also include a description of the Offeror's plans, if any, to provide services through a joint venture, teaming partner, or subcontractors.

The staffing plan must set forth the Offeror's proposed approach to staffing the requirements of this project, including the titles of each of the labor categories proposed and proposed level of effort for each member of the Offeror's development team. The staffing plan must also identify the proposed Project Manager and proposed Technical Lead by name, and include a resume for each. Those resumes must include a brief description of the experience and capability for each individual, but cannot exceed one (1) page in length each. Offerors proposing Key Personnel who are not currently employed by the Offeror or a teaming partner must include a signed letter of intent from the individual proposed as Key Personnel that he/she intends to participate in this project for at least one (1) year. The staffing plan must also set forth the extent to which the proposed team for this project was involved in the development of the source code referred to in the next paragraph.

The staffing plan must set forth and explain the extent to which individuals on the proposed team, as a whole, will provide cumulative experience in these following areas:

- Iterative development practices
- User experience and product design
- User and usability research (e.g., stakeholder and user interviews, usability testing)
- Continuous integration and continuous deployment (CI/CD)
- Azure cloud platform
- Automated testing (unit/integration/end-to-end)
- Wireframing, prototyping, and user task-flow development
- Software engineering (Cloud practitioner)
- GitOps/DevOps engineering
- Identity / authentication engineering
- Content design
- Web application security
- Cloud security
- Data security

The references to one or more source code samples must be either links to Git repositories (either credentialed or public) or to equivalent version-controlled repositories that provide USTP with the full revision history for all files. If an Offeror submits a link to a private Git repository hosted with GitHub, USTP will provide the Offeror with one or more GitHub user identities by email, and the Offeror will be expected to promptly provide the identified user(s) with access to the private Git repository.

The source code samples should be for projects that are similar in size, scope, and complexity to the project contemplated here. The source code must have been developed by either (i) the Offeror itself, (ii) a teaming partner that is proposed in response to this RFQ, or (iii) an individual that is being proposed as Key Personnel for this project. USTP would prefer that the source code samples have been for recent projects involving teams of approximately 6-9 Full-Time Equivalent (FTE) personnel.

If the references to source code samples provided do not include associated references to user research plans and design artifacts demonstrating how ongoing user research was incorporated into the project, then the Offeror must submit a user research plan and design artifacts relating to at least one (1) of the source code samples provided.

### 6.2.2 Price Submissions

Price submissions must set forth a single dollar amount that represents the Offeror's estimate of the total cost to USTP for the development services and travel expenses required for [period of performance]. [Instructions on providing price proposal, typically an excel workbook]. USTP expects that the labor categories and staffing levels set forth by the Offeror in the Excel workbook will be consistent with the Offeror's staffing plan.

The Contractor will be compensated at loaded hourly rates. USTP intends to evaluate proposals and award based on initial proposals, and therefore the Offeror's initial proposal should contain the Offeror's best terms.

### 6.2.3 Interviews

Interviews associated with this competition does not and will not constitute discussions or negotiations as defined in FAR Part 15. USTP will not determine a competitive range, conduct discussions, nor solicit or allow revised quotes.

The Offerors with the most highly rated written submissions will each be invited to participate in an interview as part of the evaluation process. Each interview will be conducted remotely via video connection and/or teleconference. USTP will communicate with certain Offerors to schedule the dates and times of interviews.

Each interview will include an unstructured question and answer session, during which Offerors will be asked questions about the technical aspects of their proposal and their approach to software development. USTP expects these interviews will assist USTP to assess the technical abilities of the proposed development team and to better understand the proposed technical approach described in the Offeror's written submission. Both of the Offeror's proposed Key Personnel must participate in the interview.

The Introductions phase of each interview will last approximately 5 minutes, during which the Offeror and USTP interview team members will introduce themselves.

The Open Technical Session of each interview will last approximately 45 minutes, during which the Offeror interview team will respond to USTP's questions related to the technical aspects of the Offeror's proposal. Offerors will NOT be able to use or present any slides, graphs, charts, or other written presentation materials, including handouts. There will be no follow-up session for further questions after this part of the interview.

The Closing Remarks phase of each interview will last approximately 5 minutes, during which the Offeror may make a short presentation summarizing the Offeror's responses to USTP's questions.

Interviews will not constitute discussions. Statements made during an interview will not become part of the agreement.

### 6.2.4 Basis of Award and Evaluation Factors

Each submission received by USTP will be evaluated for technical acceptability. Submissions that are determined to not be technically acceptable after the Offeror has been given the opportunity for a clarification will not be evaluated further.

Quotes must be realistic with respect to technical approach, staffing approach, and total price. Quotes that indicate a lack of understanding of the project requirements may not be considered for award. Quotes may indicate a lack of understanding of the project requirements if the staffing plan does not use a realistic mix of labor categories and hours, or if any proposed hourly labor rates are unrealistically high or low.

USTP will evaluate quotes that are technically acceptable on a competitive best value basis using a trade-off between technical and price factors. Technically acceptable submissions will be evaluated based on four (4) evaluation factors. These factors are: (1) technical approach, (2) staffing approach, (3) similar experience, and (4) price. The three (3) technical, non-price evaluation factors, when combined, are significantly more important than price. USTP may make an award to an Offeror that demonstrates an advantage with respect to technical, non-price factors, even if such an award would result in a higher total price to USTP. The importance of price in the evaluation will increase with the degree of equality between Offerors with respect to the non-price factors, or when the Offeror's price is so significantly high as to diminish the value to USTP of the Offeror's advantage in the non-price factors.

#### Technical Approach

In evaluating an Offeror's technical approach, USTP will consider (a) the quality of the Offeror's plans to provide the open source, iterative development services required, including user research and design, (b) the extent of the Offeror's understanding of the details of the project requirements, and (c) the extent to which the Offeror has identified potential obstacles to efficient development, and has proposed realistic approaches to overcome those potential obstacles.

#### Staffing Approach

In evaluating an Offeror's staffing approach, USTP will consider (a) the skills and experience of the Key Personnel and other individuals that the Offeror plans to use to provide the required services, (b) the mix of labor categories that will comprise the Offeror's proposed development team, and (c) the Offeror's proposed number of hours of services to be provided by each member of the Offeror's proposed development team.

#### Similar Experience

In evaluating an Offeror's similar experience, USTP will consider the extent to which the Offeror has recently provided software development services for projects that are similar in size, scope, and complexity to the project described in this RFQ, and the quality of those services. In evaluating the quality of those services, USTP will consider, among other things, the revision history for all files in the source code samples provided. USTP will also consider the user research and design-related artifacts that were associated with the source code samples provided or submitted separately. In considering an Offeror's similar experience, USTP may also consider information from any other source, including Offeror's prior customers and public websites.

#### Price

In evaluating an Offeror's price, USTP will consider the total of the Offeror's estimated costs for the development services, and travel expenses proposed.