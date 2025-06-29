🔴🔵☑️✔️ ➡️ ✓→•←⁕⁂※⁜‣

# ➡️ RestAPI Annotations

### 🔵 **@RestController**
- Can be used to put on top of a class. 
- This will expose your methods as REST APIs. 
- Developers can also use **@Controller**(top of a class) + **@ResponseBody**(top of a method) for same behaviour.
- **@ResponseBody** can be used on top of a method to build a Rest API when we are using **@Controller** on top of a Java class

### 🔵 **@GetMapping** / **@PostMapping** / **@PutMapping** / **@DeleteMapping**
- Shortcut for `@RequestMapping(value = ..., method = ...)`. 
- e.g:  **@RequestMapping**(`"/example"`)
- e.g:  **@RequestMapping**(`value = "/example", method = RequestMethod.GET`)

### 🔵 **@RequestHeader** & **@RequestBody** & **@RequestParam**
- Used to receive the request body and header individually.
- **@RequestParam** is used to extract query parameter from an HTTP request and bind them to method parameters within a controller.
- **Query Parameter** are append to URL after a question mark(?). (e.g: url?name=John&age=27)

### 🔵 **@PathVariable**
- Binds URL path parameters to method arguments.
- **Usage**:
  ```java
  @GetMapping("/users/{id}")
  public User getUser(@PathVariable Long id) {
      // Logic here
  }
### 🔵 **ResponseEntity<T>** 
- Allow developers to send response body, status, and headers on the HTTP response.

### 🔵 **RequestEntity<T>**
- Allow developers to receive the request body, header in a HTTP request.


### 🔵 **@ControllerAdvice** 
- Used to mark the class as a REST controller advice. Along with **@ExceptionHandler**, this can be used 
   to handle exceptions globally inside app. 
- We have another annotation **@RestControllerAdvice** which is same as **@ControllerAdvice** + **@ResponseBody**.
