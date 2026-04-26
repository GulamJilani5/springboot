⏺️ ➡️ 🟦 🟩 🟢 🔵 🔷 🔹🔴 ☑️ ✔️ ✓→•←⁕⁂※⁜‣

# ⏺️ Response Body
- The response body is the actual data sent from backend to client.
- The `Content-Type` header in the headers section tells what kind of data is in the body.
- Spring uses `Jackson`
   - Java Object → JSON
   - JSON → Java Object
   - We don’t manually convert → Spring does it automatically
### ➡️ GuideLines
##### 🟦 Best Practices
- Always use Standard Response Wrapper
- Separate Success & Error Structure
- Never expose Entity directly but use DTO
- Keep response predictable
- Use meaningful messages
##### 🟦 Security Considerations in Response Body
- Never expose Entity directly but use DTO
- Mask sensitive data
- NEVER return:
  - Password
  - OTP
  - Internal IDs
  - Tokens (unless required)🔴

##### 🟦 Flow
- Request → Backend → Response
```text
React → Controller → Service → DB
                       ↓
                  Response DTO
                       ↓
                ResponseEntity
                       ↓
              JSON Response Body
```

### ➡️ Success and Failure Example
- ###### 🔵 generic wrapper for all responses (success + error)
```java
import com.fasterxml.jackson.annotation.JsonInclude;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.Instant;

@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)  // Hide null fields in JSON
public class ApiResponse<T> {

    private String status;          // "SUCCESS" or "ERROR"
    private String message;         // Human-readable message
    private String code;            // Business error/success code (e.g., "TXN_001", "ERR_429")
    private T data;                 // Actual payload (null on error)
    private ErrorDetails error;     // Only present on error
    private MetaData meta;          // Pagination, timestamp, requestId, etc.
    private Instant timestamp;      // Always useful for auditing

    // Helper static methods (recommended)
    public static <T> ApiResponse<T> success(T data, String message) {
        return ApiResponse.<T>builder()
                .status("SUCCESS")
                .message(message)
                .data(data)
                .timestamp(Instant.now())
                .build();
    }

    public static <T> ApiResponse<T> success(T data, String message, String code) {
        return ApiResponse.<T>builder()
                .status("SUCCESS")
                .message(message)
                .code(code)
                .data(data)
                .timestamp(Instant.now())
                .build();
    }

    public static <T> ApiResponse<T> error(String message, String code, ErrorDetails errorDetails) {
        return ApiResponse.<T>builder()
                .status("ERROR")
                .message(message)
                .code(code)
                .error(errorDetails)
                .timestamp(Instant.now())
                .build();
    }
}
```
- ###### 🔵 Supporting Classes
```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ErrorDetails {
    private String errorCode;       // e.g., "INSUFFICIENT_BALANCE", "INVALID_OTP"
    private String detail;          // Technical but safe message
    private Object additionalInfo;  // e.g., field errors for validation
}

@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class MetaData {
    private String requestId;       // Unique ID for tracing (very important in fintech)
    private Integer page;
    private Integer size;
    private Long totalElements;
    // Add more: correlationId, version, etc.
}
```

##### 🟦 Success Response Body 
- ###### 🔵 Get Account Balance (Simple Success)
```java
@RestController
@RequestMapping("/api/v1/accounts")
@RequiredArgsConstructor
public class AccountController {

    private final AccountService accountService;

    @GetMapping("/{accountId}/balance")
    public ResponseEntity<ApiResponse<AccountBalanceDto>> getBalance(
            @PathVariable String accountId,
            @RequestHeader("X-Request-ID") String requestId) {   // Good practice: pass requestId

        AccountBalanceDto balance = accountService.getBalance(accountId);

        ApiResponse<AccountBalanceDto> response = ApiResponse.success(
                balance,
                "Account balance retrieved successfully",
                "BAL_001"
        );

        // Add metadata
        response.setMeta(MetaData.builder()
                .requestId(requestId)
                .timestamp(Instant.now())  // or set in builder
                .build());

        return ResponseEntity.ok(response);
    }
}
```
- ###### 🔵 JSON Response Body (200 OK):
  - **Note:** Never return full sensitive fields (mask account numbers if needed).
```json
{
  "status": "SUCCESS",
  "message": "Account balance retrieved successfully",
  "code": "BAL_001",
  "data": {
    "accountId": "ACC_987654",
    "availableBalance": 12450.75,
    "currency": "INR",
    "lastUpdated": "2026-04-26T13:45:00Z"
  },
  "meta": {
    "requestId": "req-abc123xyz"
  },
  "timestamp": "2026-04-26T13:45:12Z"
}
```
##### 🟦 Error Response Body 

- ###### 🔵 Custom Business Exceptions
```java
// Example custom exception
public class InsufficientBalanceException extends RuntimeException {
    private final String errorCode = "INSUFF_BAL";
    private final BigDecimal available;
    private final BigDecimal requested;
    // getters...
}
```
- ###### 🔵 Global Exception Handler (@RestControllerAdvice)
```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    // Handle specific business exception
    @ExceptionHandler(InsufficientBalanceException.class)
    public ResponseEntity<ApiResponse<Void>> handleInsufficientBalance(
            InsufficientBalanceException ex,
            HttpServletRequest request) {

        ErrorDetails errorDetails = ErrorDetails.builder()
                .errorCode(ex.getErrorCode())
                .detail("Available: " + ex.getAvailable() + ", Requested: " + ex.getRequested())
                .build();

        ApiResponse<Void> response = ApiResponse.error(
                "Insufficient balance in source account",
                ex.getErrorCode(),
                errorDetails
        );

        response.setMeta(MetaData.builder()
                .requestId(request.getHeader("X-Request-ID"))
                .build());

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(response);
    }

    // Validation errors (MethodArgumentNotValidException)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiResponse<Void>> handleValidationErrors(
            MethodArgumentNotValidException ex) {

        List<String> fieldErrors = ex.getBindingResult().getFieldErrors()
                .stream()
                .map(err -> err.getField() + ": " + err.getDefaultMessage())
                .toList();

        ErrorDetails errorDetails = ErrorDetails.builder()
                .errorCode("VALIDATION_FAILED")
                .detail("Input validation failed")
                .additionalInfo(fieldErrors)
                .build();

        ApiResponse<Void> response = ApiResponse.error(
                "Invalid request data",
                "VALIDATION_FAILED",
                errorDetails
        );

        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(response);
    }

    // Generic fallback
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiResponse<Void>> handleGenericException(Exception ex) {
        log.error("Unexpected error", ex);

        ApiResponse<Void> response = ApiResponse.error(
                "An unexpected error occurred. Please try again or contact support.",
                "INTERNAL_ERROR",
                ErrorDetails.builder()
                        .errorCode("SYS_500")
                        .detail("Internal server error")
                        .build()
        );

        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(response);
    }
}
```
- ###### 🔵 Sample Error JSON (400 Bad Request – Insufficient Balance):
```json
{
  "status": "ERROR",
  "message": "Insufficient balance in source account",
  "code": "INSUFF_BAL",
  "error": {
    "errorCode": "INSUFF_BAL",
    "detail": "Available: 4500.00, Requested: 10000.00"
  },
  "meta": {
    "requestId": "req-transfer-002"
  },
  "timestamp": "2026-04-26T14:15:30Z"
}
```


