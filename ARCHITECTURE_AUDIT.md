# Mindof Boost - Architecture Audit & Planning

## ⚠️ IMPORTANT CLARIFICATION
**This is a CLINICIAN TOOLKIT/RESOURCE PORTAL - NOT a patient data management system.**
- No patient data is stored or processed
- Tools are for reference, calculation, and workflow support
- Resources are templates, guidelines, and reference materials
- Users are clinicians/staff members only

## Brand Colors (from mindof.uk)
- Primary Blue: `rgb(17, 109, 255)` / `#116DFF`
- Text: `rgb(0, 0, 0)` / `#000000`
- Background: `rgb(255, 255, 255)` / `#FFFFFF`
- Secondary Gray: `rgb(95, 99, 96)` / `#5F6360`

---

## AUDIT 1: Security & Compliance Architecture

### Critical Questions:

1. **Data Protection & Compliance:**
   - Which compliance standards must we meet? (GDPR, HIPAA, UK Data Protection Act, CQC requirements?) this is a portal just for clinicians so GDPR only.
   - What level of data encryption is required? (at rest, in transit) no patient data used this si a helper portal for staff.
   - Do we need audit trails for all clinical data access/modifications? nope
   - What are the data retention policies for patient records? none used.

2. **Authentication & Authorization:**
   - Should we use Supabase Auth or a separate auth provider? use supabase for simplicity.
   - Do we need 2FA/MFA for admin users? nah no
   - Should regular users have different permission levels (e.g., read-only clinicians vs. full-access)? admin should decide the level of permissions of users.
   - How should session management work? (timeout policies, concurrent sessions) use best industry practices.

3. **PHI (Protected Health Information) Handling:**
   - Should patient data be pseudonymized/anonymized in certain views? none used.
   - What are the requirements for data export/deletion (patient rights)? none used
   - Do we need role-based data access (e.g., clinicians only see their patients)? no access to patients.

4. **API Security:**
   - Should we implement rate limiting? not sure what this is - use best industry practices
   - Do we need API keys for external integrations (medication databases, etc.)? hmm hardcode it.
   - What CORS policies are needed? none

---

## AUDIT 2: Data Architecture & Database Design

### Critical Questions:

1. **Database Schema (Toolkit/Resource Data):**
   - Should we use Supabase Row Level Security (RLS) for user permissions? (e.g., users can only see certain resources based on admin settings) Yes
   - What data do we store?  user accounts,   policies/documents,  user preferences 
   - How should we structure the colleague directory? (name, specialty, contact info, notes, tags/categories?) name, tags, phone number, email
   - Should users be able to add personal notes/bookmarks to resources? any user can add/remove/edit collegue 

2. **Content Management:**
   - How should we store policies/procedures? (markdown, HTML, PDF uploads, or all?) link to sharepoint, date last updated, name of policy
   - Do policies need versioning/history? (track changes over time) no its just a list of links to sharepoint
   - Should there be categories/tags for organizing resources? (safeguarding, clinical guidelines, IT setup, etc.) no need in my tool.
   - Can admins upload files directly or do they need to be formatted in a specific way? any user can add / remove / edit entry

3. **File Storage:**
   - Where should we store documents (PDFs, images, videos for policies, training materials)? (Supabase Storage)
   - What are the file size limits? (for training videos, large PDFs) make it small and sweet. this can be youtube link or link to sharepooint. no need to store iin my tool anything beyond tables with data.
   - Should users be able to download/export resources? (PDF generation for policies, printable forms) no nah. 

4. **External Data Integration:**
   - Which medication databases will we integrate? (NHS BNF, BNF for Children - for reference/calculation tools) - just medicines for children prescribed by psychiatrists in the uk.
   - Do we need real-time drug interaction checking APIs? (for the prescribing calculator tool) no.
   - Should medication data be cached locally or fetched on-demand? locally.
   - Do we need offline capabilities for any tools? (calculators, forms) no.

5. **User Data & Preferences:**
   - What user preferences should we store? (dashboard layout, favorite tools, notification settings) none.
   - Should users have personal workspaces? (saved calculations, bookmarked resources, personal notes) no
   - How do we track CPD activities? (date, type, hours, certificate uploads, reminders) no
   - Should there be user activity logs? (which tools/resources accessed, when) no

---

## AUDIT 3: Feature Architecture & User Flows

### Critical Questions:

1. **Electronic Prescribing Tools (Reference/Calculator Tools):**
   - Should the dosage calculator save calculation history? (for user's reference) no
   - Do we need printable calculation results? (PDF export of calculations) yes pdf generator with automatic download
   - Should drug interaction alerts be displayed inline or in a separate modal? no need to do this.
   - Can users customize/formulary preferences? (save preferred medications, dosages) no, each drug in the database will have its own range of strenghts, brands, release profiles etc.
   - Should calculators work offline? (PWA capabilities) no

2. **Growth & Development Charts (Template/Reference Tool):**
   - Which charting libraries should we use? (Chart.js, Recharts, D3.js?) choose the best you think. idk
   - Should this be an interactive template where clinicians can input sample data to see how charts work? yes age gender and height / weight
   - Do we need to show WHO/UK growth chart percentiles as reference? uk
   - Should charts be exportable/printable? (for training/reference) no
   - Is this just a visual reference or should users be able to practice plotting data? not sure.






---

## AUDIT 4: Technical Architecture & Infrastructure

### Critical Questions:

1. **Next.js Architecture:**
   - Should we use App Router or Pages Router? not sure - use your gut. 
   - Do we need SSR, SSG, or ISR for any pages? not sure what it is but use best practices. Pause here and next time we pickup from here.
   - Should we use Server Components or Client Components primarily? 
   - What's the API route strategy? (Next.js API routes vs. Supabase Edge Functions?)

2. **State Management:**
   - Do we need global state management? (Zustand, Redux, Context API?)
   - How do we handle form state? (React Hook Form, Formik?)
   - Should we use React Query/TanStack Query for server state?

3. **Styling with Bulma:**
   - Should we customize Bulma with SASS variables or use CSS modules?
   - Do we need a design system/component library on top of Bulma?
   - How do we handle dark mode (if needed)?

4. **Performance:**
   - What are the expected user loads? (concurrent users, data volume)
   - Do we need caching strategies? (Redis, Vercel Edge Cache?)
   - Should we implement pagination/infinite scroll for large lists?

5. **Deployment:**
   - Where will we deploy? (Vercel, self-hosted, other?)
   - Do we need staging/production environments?
   - What's the CI/CD strategy?

6. **Monitoring & Logging:**
   - Do we need error tracking? (Sentry, LogRocket?)
   - What analytics do we need? (which tools are used most, popular resources, user engagement?)
   - Should we log user activity? (for understanding tool usage, not clinical actions)

7. **Backup & Disaster Recovery:**
   - What's the backup strategy for Supabase?
   - Do we need point-in-time recovery?
   - What's the RTO/RPO requirements?

8. **Integration Points:**
   - Do we need to integrate with existing MindOf systems? (booking system, other tools?)
   - Should we support API access for third-party integrations? (or keep it self-contained)
   - Do we need webhook support for external notifications? (or just internal notifications)
   - Should medication databases be integrated via API or static data files?

---

## Additional Considerations

### User Experience:
- Should the UI be mobile-responsive or desktop-first? (clinicians likely use tablets/laptops)
- Do we need offline capabilities for any features? (calculators, forms - PWA?)
- What's the accessibility requirement? (WCAG 2.1 AA?)
- How should the dashboard be organized? (quick access to frequently used tools, recent resources, shortcuts)
- Should there be a search function across all tools/resources?
- How should navigation work? (sidebar menu, top nav, or both?)

### Admin Features:
- What admin capabilities are needed? (user management, content management for policies/resources, template management, colleague directory management, analytics dashboard?)
- Should admins be able to customize templates? (clinical notes, referral forms, assessment tools)
- Should admins be able to control which tools/resources are visible to which users? (permission-based access)
- Do we need admin activity logs? (who added/edited content, when)
- Can admins upload bulk content? (multiple policies, training materials at once)

### Multi-tenancy:
- Is this single-tenant (one MindOf clinic) or multi-tenant (multiple clinics/organizations)?
- If multi-tenant, how do we handle data isolation? (separate content libraries, user groups, or shared resources?)

---

## Additional Toolkit-Specific Questions:

1. **Dashboard & Navigation:**
   - What should the main dashboard show? (quick access tools, recent resources, notifications, shortcuts)
   - Should users be able to customize their dashboard? (pin favorite tools, rearrange widgets)
   - How should the 17 features be organized? (by category, all in sidebar, grouped by workflow?)

2. **Building & Environmental Guide:**
   - Is this a static document library or interactive guide?
   - Should it include images/diagrams? (room layouts, equipment photos)
   - Can admins update this content easily?

3. **Onboarding Toolkit:**
   - Should this be a step-by-step wizard for new staff?
   - Can admins customize the onboarding checklist?
   - Should completion be tracked? (mark items as done, track progress)

4. **Patient & Family Resources:**
   - Are these printable leaflets/templates that clinicians can customize and print?
   - Should there be categories? (by condition, by age group)
   - Can users bookmark favorites?

5. **Time Management Aids:**
   - Is this a personal task manager for clinicians? (not connected to actual appointments)
   - Should it integrate with external calendars? (Google Calendar, Outlook)
   - What analytics should be shown? (time spent on tasks, workload distribution)

6. **Risk & Incident Reporting:**
   - Are these forms that clinicians fill out and submit? (to admin/internal system)
   - Should submitted reports be stored in the system? (for admin review)
   - Do we need notification system for new reports? (alert admins)

---

## Next Steps

**Please answer the questions above so we can:**
1. Finalize the database schema (focused on toolkit resources, not patient data)
2. Create detailed feature specifications
3. Develop a comprehensive development plan
4. Begin implementation

