```mermaid
graph LR
A[Specification]
  
  A --> B[Core Specs]
  B --> B1[RFC 6749 - The OAuth 2.0 Authorization Framework]
  B --> B2[RFC 6750 - The OAuth 2.0 Authorization Framework: Bearer Token Usage]
  
  A --> C[Security]
  C --> C1[RFC 6819 - OAuth 2.0 Threat Model and Security Considerations]
  C --> C2[OAuth 2.0 Security Best Current Practice]
  
  A --> D[Authorization]
  D --> D1[RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients]
  D --> D2[RFC 8628 - OAuth 2.0 Device Authorization Grant]
  D --> D3[RFC 8707 - Resource Indicators for OAuth 2.0]
  
  A --> E[Token Management]
  E --> E1[RFC 7519 - JSON Web Token]
  E --> E2[RFC 7662 - OAuth 2.0 Token Introspection]
  E --> E3[RFC 7009 - OAuth 2.0 Token Revocation]
  E --> E4[RFC 8693 - OAuth 2.0 Token Exchange]
  
  A --> F[Client Management]
  F --> F1[RFC 7591 - OAuth 2.0 Dynamic Client Registration Protocol]
  F --> F2[RFC 7592 - OAuth 2.0 Dynamic Client Registration Management Protocol]
  
  A --> G[Additional Security]
  G --> G1[RFC 8705 - OAuth 2.0 Mutual-TLS Client Authentication and Certificate-Bound Access Tokens]
  
  A --> H[Metadata]
  H --> H1[RFC 8414 - OAuth 2.0 Authorization Server Metadata]
  
  A --> I[Native Apps]
  I --> I1[RFC 8252 - OAuth 2.0 for Native Apps]
  
  class D,D1,D2,D3 authColor
  class E,E1,E2,E3,E4 tokenColor
  class F,F1,F2 clientColor
  class G,G1 securityColor
  class H,H1 metadataColor
  class I,I1 nativeColor

  classDef authColor fill:#f9f,stroke:#333,stroke-width:2px;
  classDef tokenColor fill:#bbf,stroke:#333,stroke-width:2px;
  classDef clientColor fill:#bfb,stroke:#333,stroke-width:2px;
  classDef securityColor fill:#ffb,stroke:#333,stroke-width:2px;
  classDef metadataColor fill:#fbf,stroke:#333,stroke-width:2px;
  classDef nativeColor fill:#bff,stroke:#333,stroke-width:2px;
  ```