# ADO Feature: Case Binder UI - Collapsible Grouped Sections (Proposal C)

**Feature ID:** TBD  
**Priority:** High  
**Target Release:** Q2 2026  
**Created:** March 3, 2026  
**Status:** Proposed  

---

## 📋 Executive Summary

Implement a new Case Binder document organization interface using Proposal C: **Grouped Sections — Collapsible**. This feature provides an enhanced document viewing experience where documents are organized by beneficiary with individual expand/collapse controls, plus bulk operations for managing visibility across all sections.

---

## 🎯 Business Value

### Problem Statement
Current case binder document organization lacks efficient navigation for complex immigration cases involving multiple beneficiaries (e.g., H-1B principal + multiple H-4 dependents + EAD applications). Users experience:
- Information overload when viewing all documents simultaneously
- Difficulty focusing on specific beneficiary documentation
- Reduced productivity when reviewing large document sets
- Poor scalability for cases with 20+ documents

### Solution Benefits
1. **Improved User Experience**: Collapsible sections reduce visual clutter while maintaining organization
2. **Enhanced Productivity**: Users can focus on one beneficiary at a time
3. **Scalability**: Better performance and usability for cases with numerous documents
4. **Flexibility**: Bulk controls enable quick navigation patterns
5. **Clarity**: Maintains clear document-to-beneficiary relationship even when collapsed

### Success Metrics
- **30% reduction** in time spent locating specific documents
- **50% reduction** in scroll distance for document navigation
- **90%+ user satisfaction** rating for document organization
- **Zero regression** in document accessibility

---

## 👥 User Stories

### Epic: Case Binder Document Organization

#### User Story 1: View Grouped Documents by Beneficiary
**As a** paralegal reviewing an H-1B family case  
**I want to** see documents grouped by beneficiary with clear visual separation  
**So that** I can quickly understand which documents belong to which person  

**Acceptance Criteria:**
- Documents are grouped into distinct sections by beneficiary
- Each section displays beneficiary name, visa type, and relationship
- Universal case documents appear in a dedicated section at the top
- Visual indicators (avatars, colors) distinguish different beneficiaries
- Document count is visible for each beneficiary section

---

#### User Story 2: Collapse/Expand Individual Beneficiary Sections
**As a** case manager reviewing multiple beneficiaries  
**I want to** collapse and expand individual beneficiary sections  
**So that** I can focus on one person's documents at a time  

**Acceptance Criteria:**
- Clicking a beneficiary header toggles collapse/expand state
- Collapsed sections show beneficiary info and document count
- Expanded sections show full document list
- Smooth animation transitions between states (300-400ms)
- Visual indicator (arrow/chevron) shows current state
- State persists during the current session

---

#### User Story 3: Bulk Expand/Collapse Operations
**As a** immigration attorney reviewing a full case  
**I want to** expand or collapse all sections with one click  
**So that** I can quickly get an overview or detailed view  

**Acceptance Criteria:**
- "Collapse all" link collapses all beneficiary sections
- "Expand all" link expands all beneficiary sections
- Bulk operations complete with smooth animations
- Actions are clearly labeled and accessible
- No performance degradation with 10+ sections

---

#### User Story 4: Document Selection and Preview
**As a** legal assistant reviewing case documents  
**I want to** click on a document to view its contents  
**So that** I can verify information without losing context  

**Acceptance Criteria:**
- Clicking a document highlights it
- Document preview loads in preview pane
- Selected state is visually distinct
- Previous selection is cleared when new document selected
- Preview pane shows document metadata and content

---

#### User Story 5: Responsive Document Count Display
**As a** case reviewer  
**I want to** see how many documents each beneficiary has  
**So that** I can quickly assess completeness  

**Acceptance Criteria:**
- Document count displays for each beneficiary (e.g., "13 docs")
- Count is visible in both collapsed and expanded states
- Count updates dynamically if documents are added/removed
- Universal documents section shows its own count

---

## 🏗️ Technical Requirements

### Frontend Architecture

#### Component Structure
```
CaseBinder/
├── CaseHeader.tsx           # Case info and action buttons
├── DocumentsSection.tsx     # Main container
│   ├── UniversalDocuments.tsx
│   ├── BeneficiarySection.tsx
│   │   ├── BeneficiaryHeader.tsx
│   │   ├── BeneficiaryContent.tsx
│   │   └── DocumentList.tsx
│   │       └── DocumentItem.tsx
│   └── BulkControls.tsx
└── DocumentPreviewPane.tsx
```

#### State Management
```typescript
interface CaseBinderState {
  caseInfo: CaseInfo;
  beneficiarySections: BeneficiarySection[];
  expandedSections: Set<string>;  // beneficiary IDs
  selectedDocument: string | null;
  universalDocuments: Document[];
}

interface BeneficiarySection {
  id: string;
  name: string;
  visaType: string;
  relationship: string;
  documents: Document[];
  isCollapsed: boolean;
}

interface Document {
  id: string;
  name: string;
  type: string;
  beneficiaryId: string;
  tags: string[];
  uploadedDate: Date;
  metadata: DocumentMetadata;
}
```

---

### API Requirements

#### GET /api/cases/{caseId}/binder
**Response:**
```json
{
  "caseInfo": {
    "id": "CAS-140889",
    "name": "HCAP (Case1)",
    "status": "Open",
    "totalDocuments": 34
  },
  "universalDocuments": [
    {
      "id": "doc-001",
      "name": "NH NIV_NIV_HCap Checklist",
      "type": "checklist",
      "uploadedDate": "2026-02-15T10:30:00Z"
    }
  ],
  "beneficiarySections": [
    {
      "id": "ben-001",
      "name": "John Bean",
      "visaType": "H-1B",
      "relationship": "Principal",
      "avatarColor": "blue",
      "documents": [
        {
          "id": "doc-101",
          "name": "Form I-907",
          "type": "form",
          "tags": ["H-1B"],
          "uploadedDate": "2026-02-20T14:22:00Z"
        }
      ]
    }
  ]
}
```

---

### UI/UX Specifications

#### Visual Design
- **Header Colors**: Gradient background (#667eea to #764ba2)
- **Section Background**: #f8f9fa (collapsed), white (expanded)
- **Avatar Colors**: Blue, Pink, Purple, Green (assigned per beneficiary)
- **Primary Action Color**: #2196F3
- **Border Radius**: 6px for sections, 4px for buttons/tags
- **Spacing**: 20px between sections, 16px padding in headers

#### Animation Specifications
- **Collapse/Expand Duration**: 400ms
- **Easing Function**: ease-in-out
- **Hover Transitions**: 200ms
- **Max Height Transition**: Used for smooth collapse animation

#### Accessibility Requirements
- WCAG 2.1 Level AA compliance
- Keyboard navigation support (Tab, Enter, Space, Arrow keys)
- Screen reader announcements for expand/collapse states
- Focus indicators on all interactive elements
- Proper ARIA attributes (aria-expanded, aria-controls, role)
- Minimum 4.5:1 contrast ratio for all text

---

## 🔧 Implementation Details

### Phase 1: Core Structure (Sprint 1)
**Duration:** 2 weeks  
**Story Points:** 13

**Tasks:**
1. Create React component structure
2. Implement static layout and styling
3. Set up state management (Redux/Context)
4. Implement case data API integration
5. Create mock data for testing
6. Unit tests for components

**Deliverables:**
- Working static layout with all sections visible
- API integration complete
- Unit test coverage >80%

---

### Phase 2: Collapse/Expand Functionality (Sprint 2)
**Duration:** 2 weeks  
**Story Points:** 8

**Tasks:**
1. Implement individual section collapse/expand
2. Add animation transitions
3. Create toggle icon component
4. Implement state persistence
5. Add keyboard navigation
6. Integration tests

**Deliverables:**
- Fully functional collapse/expand for individual sections
- Smooth animations
- Keyboard accessible
- Integration tests passing

---

### Phase 3: Bulk Controls & Polish (Sprint 3)
**Duration:** 1.5 weeks  
**Story Points:** 5

**Tasks:**
1. Implement "Collapse all" / "Expand all" controls
2. Add document selection highlighting
3. Optimize animation performance
4. Add loading states and error handling
5. Accessibility audit and fixes
6. Cross-browser testing

**Deliverables:**
- Bulk controls fully functional
- Performance optimized (60fps animations)
- Accessibility compliance verified
- Browser compatibility confirmed (Chrome, Firefox, Edge, Safari)

---

### Phase 4: Document Preview Integration (Sprint 4)
**Duration:** 2 weeks  
**Story Points:** 8

**Tasks:**
1. Create document preview pane component
2. Implement document selection logic
3. Add document metadata display
4. Integrate with document viewer service
5. Add lazy loading for document content
6. E2E tests

**Deliverables:**
- Working document preview pane
- Document selection and highlighting
- E2E tests covering main user flows
- Performance benchmarks met

---

## 🧪 Testing Strategy

### Unit Tests
- Component rendering tests
- State management logic
- Animation utilities
- Helper functions
- Target: >80% code coverage

### Integration Tests
- API integration tests
- Component interaction tests
- State update flows
- Navigation flows

### E2E Tests
**Critical User Flows:**
1. Load case binder → Verify all sections display
2. Click beneficiary header → Verify section collapses
3. Click collapsed header → Verify section expands
4. Click "Collapse all" → Verify all sections collapse
5. Click "Expand all" → Verify all sections expand
6. Click document → Verify selection and preview
7. Navigate with keyboard → Verify accessibility

### Performance Tests
- Animation frame rate (target: 60fps)
- Load time for 50+ documents (target: <2s)
- Memory usage monitoring
- Scroll performance with all sections expanded

### Browser Compatibility Tests
- Chrome (latest 2 versions)
- Firefox (latest 2 versions)
- Edge (latest 2 versions)
- Safari (latest 2 versions)

---

## 📊 Data Model

### Database Schema Updates

```sql
-- Beneficiary table (if not exists)
CREATE TABLE beneficiary (
    id VARCHAR(50) PRIMARY KEY,
    case_id VARCHAR(50) NOT NULL,
    full_name VARCHAR(200) NOT NULL,
    visa_type VARCHAR(50),
    relationship VARCHAR(50),
    avatar_color VARCHAR(20),
    sort_order INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (case_id) REFERENCES cases(id)
);

-- Document table updates
ALTER TABLE documents 
ADD COLUMN beneficiary_id VARCHAR(50),
ADD COLUMN is_universal BOOLEAN DEFAULT FALSE,
ADD COLUMN sort_order INT,
ADD FOREIGN KEY (beneficiary_id) REFERENCES beneficiary(id);

-- Index for performance
CREATE INDEX idx_documents_beneficiary ON documents(beneficiary_id);
CREATE INDEX idx_documents_case_universal ON documents(case_id, is_universal);
```

---

## 🔐 Security Considerations

1. **Authorization**: Verify user has access to case before showing documents
2. **Data Filtering**: Server-side filtering of document lists by user permissions
3. **XSS Prevention**: Sanitize all document names and metadata
4. **CSRF Protection**: Use tokens for state-changing operations
5. **Audit Logging**: Log document access and section interactions
6. **Sensitive Data**: No PII in client-side storage beyond current session

---

## ⚡ Performance Considerations

### Optimization Strategies
1. **Lazy Loading**: Load document content only when selected
2. **Virtualization**: Use virtual scrolling for 100+ documents
3. **Memoization**: Memoize expensive computations (React.memo, useMemo)
4. **Debouncing**: Debounce rapid collapse/expand operations
5. **Code Splitting**: Lazy load preview pane component
6. **Caching**: Cache beneficiary sections in session storage

### Performance Targets
- **Initial Load**: <2 seconds (50 documents)
- **Collapse/Expand**: <400ms animation duration
- **Document Selection**: <100ms response time
- **Memory Usage**: <50MB additional for case binder
- **Animation FPS**: 60fps consistent

---

## 🌐 Browser & Device Support

### Supported Browsers
- Chrome 90+
- Firefox 88+
- Edge 90+
- Safari 14+

### Responsive Design
- **Desktop**: Primary target (1920x1080, 1366x768)
- **Tablet**: Secondary support (iPad, 768px-1024px)
- **Mobile**: Consider for future phase

---

## 📱 Accessibility Requirements

### WCAG 2.1 Level AA Compliance
- ✅ Keyboard navigation (Tab, Enter, Space, Arrows)
- ✅ Screen reader support (NVDA, JAWS, VoiceOver)
- ✅ Focus management (visible focus indicators)
- ✅ ARIA attributes (aria-expanded, aria-controls, aria-label)
- ✅ Color contrast (4.5:1 minimum)
- ✅ Text resize (up to 200% without loss of functionality)
- ✅ Skip links for main content areas

### Keyboard Shortcuts
- `Tab` / `Shift+Tab`: Navigate between sections and documents
- `Enter` / `Space`: Toggle collapse/expand
- `Ctrl+E`: Expand all sections
- `Ctrl+Shift+E`: Collapse all sections
- `↑` / `↓`: Navigate documents within section

---

## 🚀 Deployment Strategy

### Rollout Plan
1. **Internal Testing** (1 week): QA team and power users
2. **Beta Release** (2 weeks): 10% of users, opt-in feature flag
3. **Gradual Rollout** (1 week): 25% → 50% → 100%
4. **Monitor & Adjust**: Track metrics, gather feedback

### Feature Flags
- `caseBinder.proposalC.enabled`: Master toggle
- `caseBinder.proposalC.bulkControls`: Bulk operations toggle
- `caseBinder.proposalC.animations`: Animation toggle (accessibility)

### Rollback Plan
- Feature flag can disable instantly
- Database schema changes are additive (no breaking changes)
- Previous UI version remains available via flag

---

## 📈 Success Metrics & KPIs

### Usage Metrics
- % of users using collapse/expand functionality
- Average number of sections collapsed during session
- Frequency of "Collapse all" / "Expand all" usage
- Time spent in case binder (before vs. after)

### Performance Metrics
- Page load time (P50, P95, P99)
- Animation frame rate
- API response time
- Error rate

### Business Metrics
- Document location time (before vs. after)
- User satisfaction score (CSAT)
- Support tickets related to document organization
- Case review completion time

### Target Goals
- **30% reduction** in document location time
- **CSAT score**: >4.5 / 5.0
- **Error rate**: <0.1%
- **Adoption rate**: >80% within 30 days

---

## 📋 Dependencies

### Internal Dependencies
- Document Storage Service API
- User Authentication Service
- Case Management Database
- Document Viewer Component

### External Dependencies
- React 18+
- TypeScript 4.5+
- CSS-in-JS library (styled-components or emotion)
- Animation library (Framer Motion recommended)

### Infrastructure
- API Gateway updates for new endpoints
- Database migration scripts
- CDN configuration for static assets

---

## ⚠️ Risks & Mitigation

### Technical Risks
| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Animation performance issues | High | Medium | Performance testing early, virtualization fallback |
| Browser compatibility | Medium | Low | Comprehensive cross-browser testing |
| API response time | High | Low | Caching strategy, loading states |
| State management complexity | Medium | Medium | Thorough code review, clear documentation |

### Business Risks
| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| User adoption resistance | High | Low | User training, gradual rollout, feedback loop |
| Regression in existing features | High | Low | Comprehensive testing, feature flags |
| Unclear user value | Medium | Low | User research validation, metrics tracking |

---

## 📚 Documentation Requirements

### Developer Documentation
- Component API documentation (JSDoc)
- State management guide
- Testing guide and examples
- Deployment procedures
- Troubleshooting guide

### User Documentation
- Feature overview and benefits
- How-to guide with screenshots
- Keyboard shortcuts reference
- Video tutorial (2-3 minutes)
- FAQ section

---

## 👨‍💻 Team & Resources

### Development Team
- **Frontend Developer** (2): Component development, animations
- **Backend Developer** (1): API endpoints, data layer
- **QA Engineer** (1): Test planning and execution
- **UX Designer** (0.5): Design refinement, accessibility
- **Product Manager** (0.25): Requirements, stakeholder communication

### Estimated Effort
- **Total Story Points**: 34
- **Estimated Duration**: 7-8 weeks (4 sprints)
- **Total Developer Hours**: ~420 hours

---

## 🎯 Definition of Done

### Feature Complete When:
- ✅ All acceptance criteria met for each user story
- ✅ Unit test coverage >80%
- ✅ Integration tests passing
- ✅ E2E tests covering critical flows
- ✅ Accessibility audit passed (WCAG 2.1 AA)
- ✅ Cross-browser testing complete
- ✅ Performance benchmarks met
- ✅ Code review approved
- ✅ Documentation complete
- ✅ Security review passed
- ✅ Product owner acceptance
- ✅ Deployed to production with feature flag
- ✅ Metrics dashboard configured
- ✅ User training materials published

---

## 📞 Appendix

### Mock-up Reference
See `case_binder_proposal_c_mockup.html` for interactive prototype

### Related Documents
- Original Proposal Comparison Document
- User Research Findings
- API Specification Document
- Database Schema Documentation

### Contact Information
- **Product Owner**: [Name]
- **Tech Lead**: [Name]
- **UX Lead**: [Name]

---

**Document Version**: 1.0  
**Last Updated**: March 3, 2026  
**Next Review**: Pre-Sprint Planning  

---

## Quick Reference Checklist for Engineers

### Before Starting Development
- [ ] Review mock-up and understand all interactions
- [ ] Set up development environment with required dependencies
- [ ] Review database schema and API contracts
- [ ] Understand state management architecture
- [ ] Review accessibility requirements

### During Development
- [ ] Follow component structure outlined above
- [ ] Write tests alongside code (TDD approach)
- [ ] Ensure animations meet 60fps target
- [ ] Test keyboard navigation continuously
- [ ] Regular code reviews with team

### Before Code Review
- [ ] All unit tests passing
- [ ] Integration tests written and passing
- [ ] Manual testing in all supported browsers
- [ ] Accessibility testing with screen reader
- [ ] Performance profiling complete
- [ ] Documentation updated

### Before Deployment
- [ ] E2E tests passing
- [ ] Feature flag configured
- [ ] Metrics dashboard ready
- [ ] Rollback plan tested
- [ ] User documentation published
- [ ] Stakeholder demo completed

---

*This feature represents a significant UX improvement for case management workflows. Questions? Contact the Product Team.*