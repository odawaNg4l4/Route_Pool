# ROUTE POOL
## Cybersecurity Architecture and Risk Checklist

# 1. Security Architecture Overview
A Defense-in-Depth security architecture will be implemented to protect:
- User identity
- Delivery data
- Location data
- API endpoints
- Database integrity
- Authentication tokens

## Security Layers
1. Authentication Layer  
2. Access Control Layer  
3. API Protection Layer  
4. Data Protection Layer  
5. Infrastructure & Environment Layer  

# 2. Authentication Security (OTP & Session Management)
## 2.1 OTP-Based Login Security
The system will implement:
- 6-digit cryptographically secure random OTP
- OTP expiration set to 5 minutes
- Maximum 3 verification attempts
- Rate limiting per phone number and IP address
- OTP stored in hashed format (never plaintext)
### Risks Mitigated
- Brute force attacks
- OTP replay attacks
- Account takeover

## 2.2 JWT Session Security
After successful OTP verification:
- A JWT token will be issued
- The token will be signed using a strong secret key
- Token expiration will be 15–30 minutes
- Token will be stored in an HTTP-only cookie
- Secure and SameSite flags will be enabled
### Risks Mitigated
- Token theft via XSS
- Session hijacking
- Token manipulation

# 3. Access Control Architecture (RBAC)
## 3.1 Role-Based Access Control
Defined roles:
- Retailer
- Rider
- (Future: Admin)
### Server-Side Enforcement
- All permissions will be enforced strictly on the server.
- Role will be extracted from a verified JWT.
- Role will never be trusted from request body input.

## 3.2 Permission Matrix
| Action                    | Retailer | Rider |
|---------------------------|----------|--------|
| Create delivery           | ✅        | ❌      |
| View own deliveries       | ✅        | ❌      |
| View assigned batch       | ❌        | ✅      |
| Confirm delivery          | ✅        | ❌      |
| Mark delivery completed   | ❌        | ✅      |
### Risks Mitigated
- Broken access control
- Privilege escalation
- Horizontal privilege abuse

# 4. API Security Controls
All API endpoints will implement:
- Schema-based input validation
- Rejection of unknown fields
- Input sanitization
- Parameterized database queries
- Authentication middleware
- Authorization middleware
- Rate limiting
## 4.1 Critical Endpoints Protected
- `/api/login`
- `/api/verify-otp`
- `/api/create-delivery`
- `/api/confirm-delivery`
- `/api/rate-rider`
### Risks Mitigated
- Injection attacks
- Parameter pollution
- API abuse
- Unauthorized requests

# 5. Data Protection Strategy
## 5.1 Data in Transit
- HTTPS will be enforced
- TLS encryption will be used
- Secure cookies will be enabled
## 5.2 Data at Rest
Sensitive data will be stored securely:
- OTP codes (hashed)
- JWT secrets (environment variables)
- Database credentials (environment variables)
### Database Access Controls
- Principle of least privilege
- Application-specific database user
- No root database access from application layer
### Risks Mitigated
- Data leakage
- Credential compromise
- Man-in-the-middle attacks

# 6. Environment & Secret Management
The system will implement secure configuration management:
- Secrets stored in environment variables
- `.env` files excluded from version control
- Separate environments (development, staging, production)
- Secrets rotated periodically
- Production secrets managed via hosting dashboard
### Risks Mitigated
- Secret exposure in GitHub
- Configuration mismanagement
- Environment cross-contamination

# 7. Rate Limiting & Abuse Prevention
Rate limiting policies will be implemented for:
- OTP request endpoint
- OTP verification endpoint
- Login attempts
- Delivery submission
## Example Policy
- Maximum 5 OTP requests per hour per phone number
- Maximum 3 OTP verification attempts
- Maximum 10 API calls per minute per IP address
### Risks Mitigated
- Brute force attacks
- Automated bot abuse
- API flooding

# 8. Logging & Monitoring Strategy
The system will log:
- Failed login attempts
- OTP verification failures
- Access control violations
- Rate limit violations
The system will NOT log:
- OTP values
- JWT tokens
- Secret keys
- Raw passwords
Logs will be monitored for suspicious patterns.
### Risks Mitigated
- Undetected intrusion
- Silent privilege abuse
- Security blind spots

# 9. Project-Wide Risk Checklist
| Risk                     | Control Implemented                              |
|--------------------------|--------------------------------------------------|
| SQL Injection            | Schema validation + parameterized queries        |
| XSS                      | Input sanitization + output escaping             |
| Broken Access Control    | Server-side RBAC enforcement                     |
| OTP Brute Force          | Rate limiting + attempt cap                      |
| Token Theft              | HTTP-only secure cookies                         |
| Secret Exposure          | Environment variable management                  |
| API Abuse                | Rate limiting                                    |
| Role Escalation          | JWT role validation                              |
| Data Interception        | HTTPS + TLS enforcement                          |
| Misconfiguration         | Separate environment setup                       |

