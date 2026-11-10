# Software Requirements Specification — TARVIKA Research and Technology

**Document version:** 1.0  
**Baseline reviewed:** 8 October 2026  
**System:** TARVIKA public website, registration and admissions services, research collaboration portal, and internal review consoles  
**Purpose:** Describe the observable scope and requirements of the site represented by this repository. This is a baseline specification, not a promise that every program, service, or backend integration is currently available in production.

## 1. Introduction

### 1.1 Purpose

This document records the site’s audience, pages, functional behavior, program information, visual design, data flows, integrations, and operational constraints. It is intended for the site owner, designers, developers, content maintainers, and reviewers.

### 1.2 Product scope

TARVIKA Research and Technology presents an India-based research, education, and technology-focused institution in Udaipur, Rajasthan. The site:

- Describes the institution, its approach, learning programs, research collaboration, internships, career support, partnerships, and resources.
- Lets prospective students create an account, register interest, apply for admission, and view admission/payment status.
- Lets researchers submit proposals, create or use an account, review their submissions, and exchange follow-up messages.
- Collects employment, research-associate, internship, mentorship, and project-collaboration interest.
- Gives authorized administrators separate admission and research review consoles.
- Publishes policy, FAQ, editorial, and contact information.

The public program descriptions are learning-area overviews. They explicitly do not establish a fixed syllabus, qualification, degree, certification, placement, job, or particular learning outcome. Program-specific format, level, prerequisites, schedule, duration, fees, and availability are confirmed separately by TARVIKA.

### 1.3 Intended users

| User | Goals and access |
|---|---|
| Public visitor / prospective learner | Discover TARVIKA, compare learning areas, review policies and FAQs, contact the team, and begin registration. |
| Student account holder | Sign in, view their registration record, submit an admission application and required documents, and see application status and fee balance. |
| Researcher / proposal owner | Submit a proposal, view only their own submissions, read review status and feedback, and send follow-up messages. |
| Candidate / collaborator | Submit interest for employment, research, internship, mentorship, or project collaboration. |
| Employer | Review job-listing guidance and submit a role by email for review; publication is not automatic. |
| Authorized administrator | Review admission applications and private files, manage decisions and fee components, and review proposals and communicate with researchers. |
| Content maintainer | Maintain the website’s static information pages, program descriptions, FAQs, and editorial/resource content in the site source. |

### 1.4 Definitions and conventions

- **Worker:** Cloudflare Worker API deployed at `tarvika.gsthelper0.workers.dev`.
- **Firebase:** Firebase Authentication and Realtime Database services used by account and record workflows.
- **R2:** Cloudflare R2 object storage configured for admission documents.
- **SHALL:** Required behavior in the documented current product scope.
- **MAY / where enabled:** Behavior supported by source code but dependent on deployment configuration, available content, or service setup.

## 2. Overall description

### 2.1 Product perspective and architecture

The site is a static, multi-page web frontend built from HTML, CSS, and browser JavaScript. Shared navigation, page layout, course layouts, and portal layouts are implemented in local stylesheets and scripts. Dynamic submission and account operations call the Worker API; the Worker integrates with Firebase and R2.

| Layer | Observed implementation |
|---|---|
| Public interface | HTML pages, CSS, JavaScript, logo and program/hero image assets. |
| Shared web behavior | `pages.js` provides the responsive navigation interaction and current copyright year. Other scripts handle registration, admission, research, candidate, FAQ/editorial, and admin interactions. |
| API | Cloudflare Worker in `worker.js`; Worker configuration is in `wrangler.toml`. |
| Authentication | Firebase Email/Password for student and researcher accounts; Firebase-backed admin verification. A legacy enquiry path uses Firebase Anonymous Auth. |
| Persistent records | Firebase Realtime Database, accessed by the Worker with authenticated requests. |
| Private admission documents | R2 bucket binding `TARVIKA_R2`; files are uploaded and retrieved through admission-specific Worker routes. |
| External frontend dependency | Google Fonts: DM Sans, Manrope, and Noto Serif Devanagari. |

The Worker’s generic `/api/files` interface is explicitly disabled in the current implementation. This is distinct from the admission-specific document routes.

### 2.2 Page and content inventory

The repository contains the following 37 HTML pages:

| Area | Pages |
|---|---|
| Main site and institutional information | `index.html` (home), `about.html`, `why-tarvika.html`, `contact.html`, `programs.html`, `internship.html` |
| Program details (7) | `digitalmarketing.html`, `aiml.html`, `agritech.html`, `spacetechnology.html`, `cybersecurity.html`, `webdevops.html`, `digitalforensics.html` |
| Student lifecycle | `registration.html`, `admission.html`, `admin.html` (restricted admissions review) |
| Research collaboration | `research-collaboration.html`, `research-collaboration-registration.html`, `research-portal.html`, `research-admin.html` |
| Career and opportunity content | `career-support.html`, `careers.html` (job board), `employee-registration.html`, `employer-submit.html` |
| Editorial, FAQ, and resources | `announcements.html`, `blog.html`, `news.html`, `resources.html`, `faq.html`, `investment.html`, `partnerships.html`, `mentorship.html` |
| Policies and safety | `terms-and-conditions.html`, `refund-policy.html`, `marketing-policy.html`, `academic-policy.html`, `student-safety.html` |

The shared header links to Home, About, Why TARVIKA, Programs and its seven program pages, Internships, and Contact. The footer provides Explore, Programs, Connect, and Terms & Policies groups, plus institutional branding, social links, and a dynamically updated year. Facebook is shown without an active profile link in the observed markup.

The home page introduces TARVIKA, highlights its purpose and approach, promotes learning areas and pathways, and directs visitors to program, contact, registration, research, and other site content. Informational content pages are navigable as a static site; editorial and resource pages provide category/search filtering where the relevant controls and records are present.

## 3. Functional requirements

### 3.1 Navigation and public pages

- **FR-001:** The site SHALL provide the page inventory and navigation paths listed in §2.2.
- **FR-002:** On narrow screens, the shared menu control SHALL open and close the primary navigation, expose its state through `aria-expanded`, update its accessible label, and close after a navigation link is selected.
- **FR-003:** The program catalog SHALL link each of the seven learning areas to a detail page and provide registration-interest and return-navigation calls to action.
- **FR-004:** Public content SHALL clearly communicate that program details can change and direct visitors to TARVIKA to confirm the current offering.
- **FR-005:** Contact information SHALL include the institution’s published Udaipur address, `info@tarvikart.com`, and the published +91 contact number, together with email and telephone links.
- **FR-006:** The FAQ SHALL provide keyword search, category filters (About TARVIKA; Programs & Disciplines; Admissions & Structure; Research & Mentorship), a no-match state, and a Hindi FAQ disclosure/toggle.
- **FR-007:** Blog/resource content SHALL filter by category and search visible card text where the corresponding filter controls are present. Results counts and empty states SHALL reflect the current filter and query.
- **FR-008:** The footer SHALL expose policy links, program links, public contact information, available social profiles, and the current calendar year.

### 3.2 Learning program requirements and details

The catalog currently describes these seven learning areas. The topics and example projects below are published examples, not commitments to a fixed cohort syllabus. Detail pages also describe a possible learning pathway, intended learners, preparation, possible development, boundaries, and outcomes that are not guaranteed.

| Program | Published subject areas and examples |
|---|---|
| **Digital Marketing** | Audience/problem research; goals, positioning, and channel selection; editorial planning and storytelling; search and social fundamentals; analytics and experimentation; privacy, accessibility, and trust. Example work: audience discovery study, content experiment plan, campaign measurement brief. |
| **Artificial Intelligence & Machine Learning** | AI/ML concepts; data preparation and exploration; suitable introductory learning methods for classification, prediction, clustering, or language tasks; baselines, metrics, and error analysis; workflow integration and human review; ethics, safety, privacy, bias, and limitations. Example work: model comparison, responsible language-tool evaluation, human-in-the-loop prototype. |
| **Agri Tech** | Agriculture and food-system context; sensors and connected devices; collection quality and provenance; data-informed decisions; accessible tools and workflow fit; sustainability and evaluation. Example work: field-data quality study, water-use information prototype, farmer-centred service map. |
| **Space Technology** | Mission fundamentals; orbits and satellite functions; remote sensing and Earth data; geospatial workflows; signals and ground segments; responsible and sustainable space. Example work: Earth-observation comparison, mission concept study, satellite-data application brief. |
| **Cyber Security** | Security principles and risk; operating systems and networks; accounts and access; secure configurations and data; monitoring and incident readiness; policy, privacy, and responsible disclosure. Example work: secure-configuration review in a practice environment, small-organization threat model, simulated incident-response tabletop. Practice is limited to owned/authorized systems, test data, and isolated labs. |
| **Web Development & DevOps** | User needs and testable requirements; semantic HTML, CSS, responsive interfaces and interaction; APIs and data flows; version control and review; testing and CI/CD concepts; reliability, monitoring, maintenance, and security. Example work: accessible information site, small API with tests, release-workflow prototype. |
| **Digital Forensics** | Forensic process and scope; evidence integrity; chain of custody; filesystems and metadata; structured analysis; neutral findings and limitations. Example work: training-image timeline, integrity/custody log, neutral technical report. Exercises require controlled training evidence or explicit written authorization. |

**Program-wide rules and disclosures**

- Program pages SHALL state that content, tools, depth, resources, projects, and sequencing vary with the offering and available resources.
- The site SHALL not imply guaranteed employment, internship placement, income, client work, campaign results, field access, hardware/launch access, production systems, certification, university degree, or other stated outcomes.
- Program-specific prerequisites, fees, schedules, duration, delivery mode, and availability are not represented as fixed values in the observed catalog. Users are asked to confirm them with TARVIKA.
- Ethical and safety boundaries SHALL be shown where relevant, including permission and scope for security and forensics, consent and data limits for field/research activities, and privacy and responsible-use constraints.
- The catalog explicitly states that TARVIKA does not award university degrees.

### 3.3 Student registration and account

- **FR-009:** A visitor SHALL be able to create a student account using email and password or switch to sign-in for an existing account.
- **FR-010:** Registration SHALL collect student name, email, WhatsApp number, mobile number, address, city, qualification and qualification status, degree/stream where relevant, school/college, institution city, program of interest, and required consent.
- **FR-011:** Qualification choices SHALL include 10th, 12th, undergraduate, and postgraduate. Degree/stream choices SHALL change by qualification: 10th uses “Not applicable”; 12th includes Science, Commerce, Arts/Humanities, Vocational, and Other; UG includes BA, BSc, BCom, BCA, BBA, BTech/BE, BPharm, BDes, LLB, and Other; PG includes MA, MSc, MCom, MCA, MBA, MTech/ME, MPharm, LLM, and Other.
- **FR-012:** Password confirmation SHALL be checked before account creation. The browser SHALL use HTML form validity and display service or validation errors instead of indicating success when a request fails.
- **FR-013:** On successful registration, the system SHALL save the record against the signed-in Firebase user, return a `TRV-<UTC year>-<12 uppercase hex characters>` student reference, display it, and offer copy and text-file download actions. The reference SHALL be identified as a communication reference, not a login credential.
- **FR-014:** A student SHALL be able to sign in, load their own registration, view the registration fields, and sign out. Sign-in SHALL not disclose another account’s record.
- **FR-015:** Passwords SHALL be handled by Firebase Authentication rather than stored in the registration record.

### 3.4 Admissions application

- **FR-016:** Admission application access SHALL require a student account sign-in and an existing student registration.
- **FR-017:** After sign-in, the page SHALL display the student’s registration details, prefill applicable application fields, and show prior applications including their status and fee totals/balance when available.
- **FR-018:** The application SHALL collect date of birth, gender, nationality, program, qualification/status, school or college and institution city, permanent address, parent/guardian name and relationship, guardian phone/email/occupation, and emergency contact name/phone. Gender choices are Female, Male, Non-binary, Prefer not to say, and Self-describe; guardian relationship choices are Father, Mother, Guardian, and Other. Nationality defaults to Indian but can be edited. Registration-owned details are displayed as account data and are not editable as registration values.
- **FR-019:** Student photo is required. Guardian photo, latest marksheet, identity proof, and address proof are optional. The UI SHALL accept JPEG, PNG, WebP, and PDF as appropriate to the document category, restrict photo fields to images, and enforce an individual file size below 2 MB.
- **FR-020:** The applicant SHALL confirm the accuracy and authorized use of the information and acknowledge the Terms & Conditions before submission.
- **FR-021:** Documents SHALL be uploaded through the admission file API and stored privately. The server SHALL independently check declared size, allowed media type, file signature, owner, and category. The applicant SHALL receive visible upload/submission progress and explicit failure feedback.
- **FR-022:** A submitted admission application SHALL return a `TRV-ADM-<UTC year>-<8 uppercase hex characters>` reference. The student SHALL be able to view the resulting/past admission application status and fees after signing in. Current status options include submitted, under review, documents requested, approved, declined, and enrolled.

### 3.5 Research submission and researcher portal

- **FR-023:** Research proposal submission SHALL support both creating a researcher account and using an existing account.
- **FR-024:** The research form SHALL collect researcher name and email; password/confirmation as required by account mode; optional phone, organization, city, team, and mentor/supervisor details; research title; field; topic/keywords; research stage; collaboration type; proposal summary; fit with TARVIKA; optional supporting link; and required consent.
- **FR-025:** Supported research fields SHALL include AI/ML, Data Science, Software/Web Technologies, Cybersecurity/Digital Forensics, Agriculture/Agri Tech, Space Technology, Cloud/DevOps/Automation, Healthcare Technology, Education Technology, Climate/Sustainability, and Other. Stages SHALL include Idea, Literature review, Proposal, In progress, Prototype/pilot, and Completed. Collaboration types SHALL include discussion, academic/university, student/researcher project, mentorship/expert review, industry/community, and workshop/knowledge exchange.
- **FR-026:** Research summary SHALL be 40–6,000 characters with a live count. The optional TARVIKA-fit field SHALL support up to 2,000 characters; mentor, field, topic, title, and supporting-link fields SHALL honor their stated input limits.
- **FR-027:** Successful submission SHALL create a `TRR-<year>-<10 uppercase hex characters>`-style reference, display it with copy/download options, and explicitly say that receipt is not acceptance or partnership.
- **FR-028:** A researcher SHALL be able to sign in and access only submissions belonging to that account. The portal SHALL list submissions by recency, show proposal details, status, admin feedback, and message history, and allow a follow-up message of 2–3,000 characters.
- **FR-029:** Research statuses SHALL include submitted, under review, changes requested, approved, and declined. Status, feedback, and conversation SHALL be visible to the proposal owner after authenticated retrieval.

### 3.6 Candidate interest and employment

- **FR-030:** The candidate application SHALL collect full name, email, phone/WhatsApp, city, optional organization, application type, role/area, education, experience, skills, availability, optional portfolio/publication URL, optional CV/resume URL, motivation, and required consent.
- **FR-031:** Application types SHALL cover full-time employee, research associate, internship applicant, mentor/subject expert, and project collaborator. The form SHALL show a live motivation count and require at least 40 characters, up to 3,000.
- **FR-032:** Resume submission SHALL be a URL, not a file upload. Successful submission SHALL return and display an `EMP-<UTC year>-<10 uppercase hex characters>` application reference; submission SHALL be described as interest for review and not a job offer or guarantee.
- **FR-033:** The job-board pages SHALL explain what employers must provide, transparent compensation/work terms (including clear labeling and detail for unpaid roles), review/verification, confirmation of listing terms, publication, and closing/withdrawal steps. Employers currently submit listings by email; site content SHALL not imply automatic posting.

### 3.7 Administration and review

- **FR-034:** Administrator access SHALL be authorized server-side; hiding or protecting the admin page URL alone is not security. Admin routes SHALL require a valid Firebase identity and configured admin authorization.
- **FR-035:** The admissions console SHALL list and sort applications, show applicant and status details, refresh the queue, preview authorized private documents, and allow an administrator to save a decision and note for the student.
- **FR-036:** Admission decisions SHALL include under review, documents requested, approved, declined, and enrolled. A fee editor SHALL support tuition, registration, materials/lab, other charge, discount, scholarship, and paid amount, all in INR.
- **FR-037:** The fee total SHALL be calculated as tuition + registration + materials/lab + other charge − discount − scholarship; balance SHALL be max(0, total − amount paid). Admin-entered values SHALL be non-negative and no more than ₹10,000,000 each in the UI. No program fee is assumed or prefilled as an official price.
- **FR-038:** The research admin console SHALL list proposals by recency, show proposal details and conversations, permit status changes to under review/changes requested/approved/declined, and let the reviewer send a required 2–3,000-character message.
- **FR-039:** Admin interfaces SHALL show explicit errors and preserve a retry path when loading, saving, or file preview fails.

### 3.8 Contact, editorial, and policy content

- **FR-040:** The contact page SHALL direct users to email for program, fee, schedule, eligibility, mentorship, research, employment, and institutional-collaboration inquiries and link to the candidate interest form.
- **FR-041:** The site SHALL provide announcements, blog/field notes, news, resources, mentorship, investment, partnerships, career support, and FAQ information pages.
- **FR-042:** Policies SHALL include Terms & Conditions, Refund Policy, Marketing Policy, Academic Policy, and Student Safety, accessible from the footer and related workflows where applicable.
- **FR-043:** Policy and application consent copy SHALL describe relevant use of submitted information, payments/refunds, responsible conduct, educational outcomes, and other terms as published. Policy-specific transaction terms may vary and users are directed to review them before payment.

## 4. Data and API requirements

### 4.1 Principal record groups

| Record group | Principal fields / data |
|---|---|
| Student registration | Firebase UID ownership; generated student ID; name, email, phone/WhatsApp, address/city, qualification/status/degree, institution/city, selected program, consent, record status, creation timestamp. |
| Admission application | Student owner and application reference; registration-linked data; DOB, gender, nationality; program/education details; permanent address; guardian and emergency-contact information; status, review note, fee breakdown, uploaded-file metadata, submission/review timestamps. |
| Research submission | Researcher UID ownership and reference; contact/team/mentor information; title, field, keywords, stage, requested collaboration, summary, fit, supporting link, consent, status, admin feedback, timestamps, and messages. |
| Candidate application | Reference; candidate contact and location; organization; application/role interest; education, experience, skills, availability, portfolio/resume links, motivation, consent, timestamps. |
| Enquiry | Name, email, message, and optional phone, subject, and program where submitted. |
| Admission document | Owner/application association, category, filename and content metadata, object key, and file content stored in private R2. |

### 4.2 API surface

The Worker currently defines these routes (authentication and validation are enforced according to route purpose):

| Method and path | Purpose |
|---|---|
| `GET /` | API health/status response. |
| `POST /api/auth/register`, `POST /api/auth/login` | Firebase Email/Password account registration and sign-in; passwords are 8–128 characters and email is validated. |
| `POST /api/registrations`, `GET /api/registrations/me` | Create/read the signed-in student’s registration. |
| `POST /api/enquiries` | Save a public enquiry using the configured legacy enquiry flow. |
| `POST /api/employee-applications` | Submit candidate interest. |
| `POST /api/research-submissions` | Create a research proposal associated with an authenticated researcher. |
| `GET /api/research-submissions/mine` | Read the signed-in researcher’s submissions. |
| `POST /api/research-submissions/{reference}/messages` | Add an owner/reviewer conversation message. |
| `GET /api/admin/research-submissions`, `POST /api/admin/research-submissions/review` | Admin research queue and review actions. |
| `GET /api/admissions/me`, `POST /api/admissions` | Read the signed-in student’s admission data and submit an application. |
| `POST /api/admissions/files`, `GET /api/admissions/files/{key}` | Upload and authorized retrieval of admission documents. |
| `GET /api/admin/admissions`, `POST /api/admin/admissions/review` | Admin admissions queue, status, note, and fee updates. |
| `/api/files` and `/api/files/{key}` | Not enabled; currently returns a service-unavailable response. |

API responses are JSON. The Worker applies an origin allowlist, CORS handling, `Cache-Control: no-store`, and `X-Content-Type-Options: nosniff` to its JSON responses. The implemented allowlist includes the TARVIKA production origins and local development origins `localhost:5500` and `127.0.0.1:5500`.

### 4.3 Environment and deployment configuration

`wrangler.toml` identifies the Worker as `tarvika`, uses `worker.js` as its entry point, sets compatibility date `2025-03-01`, and binds the R2 bucket named `tarvika` as `TARVIKA_R2`. Deployment requires the Firebase configuration values documented in that file to be set as Worker secrets, plus an `ADMIN_UIDS` allowlist variable. Firebase Email/Password must be enabled for student/researcher accounts; the legacy enquiry endpoint’s anonymous-auth provider is required only if that route remains in use. Firebase Realtime Database rules and R2 access policy must enforce owner/admin restrictions.

## 5. External interface requirements

- **Web browsers:** Pages SHALL use standards-based HTML/CSS/JavaScript and support keyboard-operable links, buttons, forms, menus, accordions, filters, and account flows in current desktop and mobile browsers.
- **Google Fonts:** The frontend requests DM Sans, Manrope, and Noto Serif Devanagari from Google Fonts. Local/system sans-serif fallbacks are specified.
- **Firebase Authentication:** Email/password account registration and sign-in return Firebase identity tokens; the Worker verifies user identity before protected reads/writes.
- **Firebase Realtime Database:** Registration, admission, research, review, and enquiry records are persisted through configured database URLs and ID tokens.
- **Cloudflare R2:** Admission uploads and administrator previews are handled through the Worker, not a public bucket URL.
- **Email/telephone/social services:** `mailto:`, `tel:`, WhatsApp, LinkedIn, and Instagram links open external handlers/sites. Job-listing submissions are directed to email.

## 6. User interface and visual requirements

### 6.1 Core brand palette

Values are the CSS custom properties found in the shared design systems:

| Token | Color | Use |
|---|---|---|
| `--forest` | `#17251e` | Primary dark green; header, hero, dark sections and primary dark buttons. |
| `--forest-soft` | `#22362b` | Secondary forest-green tone. |
| `--leaf` | `#496648` | Supporting green; accents, links, focus outlines, labels and positive states. |
| `--saffron` | `#d99835` | Main saffron accent; primary calls to action, rules and highlights. |
| `--saffron-pale` | `#f0d4a3` | Pale gold; text/accent on dark backgrounds. |
| `--ink` | `#202720` | Main text on light backgrounds. |
| `--muted` | `#626b62` | Supporting text and labels. |
| `--paper` | `#f6f4eb` | Warm page/section background. |
| `--white` | `#fffefa` | Card and panel background (warm white). |
| `--line` | `rgba(32, 39, 32, .14)` | Low-contrast borders and dividers. |

### 6.2 Supporting and state colors

The pages use additional colors for hover, surfaces, text, input states, and feedback. These are implementation colors, not additional primary brand tokens: saffron hover `#e9aa4c`; warm panel `#eeece1`; pale neutral `#f0f2e9` / `#f0f1e9`; success surfaces `#eef2e8` / `#edf2e8`; pale card `#f4f3eb`; dark muted text such as `#4c564d`, `#505b51`, and `#515a51`; placeholders `#858b83`; required-field accent `#9a542f`; and error/status text `#9d2f28`. Translucent white/forest overlays are used on dark sections and image heroes.

### 6.3 Typography, layout, and responsive behavior

- Body and control text use **DM Sans**; headings/display use **Manrope**; Sanskrit/Devanagari motto text uses **Noto Serif Devanagari**.
- Shared content is generally capped at 1,200 px with 48 px desktop side gutters; form and policy pages use narrower centered measures.
- The visual language uses dark forest-green header/hero bands, warm paper surfaces, white cards, saffron CTAs, fine dividers, restrained rectangular controls, and responsive card/grid layouts.
- The homepage and course detail heroes use layered dark overlays with image backgrounds where configured. Program pages use image/hero assets and structured learning-area, project, pathway, and fit sections.
- Layouts adapt at approximately 720–760 px, with single-column forms and cards, stacked portal layouts, and responsive navigation. The FAQ language panel has a 600 px adaptation.
- Interactive states SHALL remain understandable: focus styles, current/selected state, status/error messages, hidden/visible panels, and disabled/loading button states are present in the relevant flows.

### 6.4 Media assets

The `image/` directory currently contains `logo.png`, `logo new.png`, `herossection.png`, `digital marketing.png`, `ai.png`, `agritech.png`, `space.png`, `cyber.png`, `full stack.png`, and `digital.png`. The site references the logo and relevant hero/program images in page templates.

## 7. Non-functional requirements

### 7.1 Security and privacy

- **NFR-SEC-001:** Protected records and document content SHALL be accessed only after server-side identity/authorization checks. Client-side state and the existence of an admin URL SHALL not be considered authorization.
- **NFR-SEC-002:** Student and researcher account records SHALL be scoped to the authenticated Firebase UID; admin reads/reviews SHALL require the configured admin allowlist/marker.
- **NFR-SEC-003:** Passwords SHALL not be stored in site records. Authentication secrets and Firebase project configuration SHALL be managed as deployment secrets, not committed source values.
- **NFR-SEC-004:** Admission document access SHALL remain private and be limited to the record owner or authorized administrators by Worker, Firebase rules, and R2 configuration.
- **NFR-SEC-005:** Public forms SHALL validate data, enforce configured length/type/size bounds, require consent where specified, and return explicit error responses.
- **NFR-SEC-006:** Proposal and admission flows SHALL warn users not to submit confidential or unauthorized material. Cybersecurity and digital-forensics learning SHALL be bounded by explicit authorization and safe training environments.
- **NFR-SEC-007:** The system SHALL use HTTPS for configured database service URLs and HTTPS external links/services where implemented.

### 7.2 Accessibility and usability

- **NFR-UX-001:** Layouts and forms SHALL remain usable on narrow screens and desktop sizes without requiring horizontal page scrolling for normal content.
- **NFR-UX-002:** Controls SHALL have labels or accessible names, navigation and menu state attributes, meaningful image alternatives, semantic headings, keyboard interaction, and status announcements where present.
- **NFR-UX-003:** Form validation, upload progress, submission success, and failures SHALL be communicated in-page. A failed operation SHALL not be represented as successful.
- **NFR-UX-004:** Responsive layout and reduced-motion preferences SHALL be respected where explicitly implemented (including FAQ language-toggle motion).
- **NFR-UX-005:** Users SHALL be able to review program caveats and relevant policies before making commitments or submitting consent.

### 7.3 Reliability, maintainability, and compatibility

- **NFR-REL-001:** API and external-service failures SHALL produce a readable error and an available retry/sign-in path where the workflow supports retry.
- **NFR-REL-002:** Dynamic year display SHALL use the current browser year rather than a hard-coded year after page load.
- **NFR-MNT-001:** Shared styles, navigation, footer, and scripts SHALL be reused consistently across applicable pages.
- **NFR-COMP-001:** The system SHALL support modern browsers with JavaScript enabled for account, filtering, and application workflows. Static informational content remains HTML-rendered.
- **NFR-OPS-001:** Production deployment SHALL configure the correct CORS origins, Firebase secrets/providers, admin allowlist, Firebase security rules, R2 bucket binding, and Worker routes before enabling associated workflows.

No production response-time, uptime, retention-period, or accessibility-conformance target is stated in the observed source. Those values require owner decisions before they can be treated as binding service-level requirements.

## 8. Business rules and constraints

1. The site’s program information is indicative; actual format, prerequisite, fees, schedule, duration, modules, availability, and conditions may change.
2. Enrollment and admission are not guaranteed by registration or enquiry. Research submission is not acceptance or a promise of partnership. Candidate submission is not a job, internship, mentorship, compensation, or selection guarantee.
3. TARVIKA does not award university degrees; a course does not guarantee certification, employment, salary, internship placement, business success, or a particular academic result.
4. Payment terms and refund/cancellation conditions are governed by the applicable program/service notice and policy. No fee is pre-assumed by the admission admin.
5. Users must submit accurate information and have authority to submit any documents or research material. Users must not submit confidential or unauthorized material through public forms.
6. External websites/services are outside TARVIKA’s direct control and have their own availability, security, and privacy terms.

## 9. Known implementation notes for release review

These are source-level observations to validate; this specification does not silently alter the existing behavior:

- Several program/catalog calls to action target `registration.htm`, while the repository contains `registration.html`. Confirm hosting rewrites or correct the links so registration navigation resolves in production.
- Public descriptions intentionally leave program-specific fees, dates, duration, delivery, prerequisites, and availability to separate confirmation; they should not be presented as fixed catalog facts without owner approval.
- The Worker’s generic `/api/files` route is disabled. Only the admission-specific document routes are represented as an implemented upload/preview flow.
- Job listing submission is an email-mediated review process, not an employer self-service publishing form.
- Verify production Firebase database rules and R2 object authorization independently of the UI. The source/configuration comments explicitly require owner/admin restrictions and an admin UID allowlist.
- The job-board and social-profile destinations/content should be checked by the site owner before production publication.

## 10. Acceptance checklist

- All 37 listed pages resolve on the deployed site, and global header/footer navigation reaches the intended pages.
- Each catalog program page exposes its indicative topics, example projects/pathway, audience/expectations, ethical boundaries, and non-guarantees.
- The mobile menu, FAQ search/category/Hindi panel, and editorial/resource filters produce the expected visible and accessible states.
- Student registration creates or signs into the correct Firebase account, validates qualification-dependent fields, returns a student ID, and loads only that student’s record.
- Admission login prefills the registration, requires the student photo and consent, enforces file type/size rules, saves the application, and displays the resulting status/fee values.
- Research account creation/sign-in, proposal validation and reference output, owner-only portal access, messaging, and admin review/status changes work end-to-end.
- Candidate submission validates required inputs, returns a reference, and preserves explicit non-offer/non-guarantee copy. Employer job listing instructions correctly route to email.
- Admin login, authorization, queues, fee arithmetic, notes/status changes, and authorized document preview are verified with Firebase and R2 rules configured.
- All interactive service failures are visible; no failed network/API request produces a success state.
- Production CORS origins, Worker secrets, Firebase providers/rules, `ADMIN_UIDS`, R2 binding, external links, and the `registration.htm`/`registration.html` discrepancy are verified before release.

## 11. Source baseline

This specification was prepared from the repository’s HTML pages, shared and specialized CSS, browser JavaScript, `worker.js`, `wrangler.toml`, and image asset inventory. It reflects source-visible behavior; deployment configuration, live records, provider rules, unpublished program operations, and business decisions must be confirmed with TARVIKA before treating them as production guarantees.
