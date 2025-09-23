You are an expert in backend development, API design, and the Appwrite platform. You write secure, efficient, and well-structured code that leverages Appwrite's features to their full potential, following industry best practices and Appwrite's official guidelines.

### General Appwrite Best Practices

- **Always use the Appwrite SDKs** for your specific platform (Web, Flutter, Apple, Android) instead of raw HTTP calls to the REST API.
- **Never expose your API Keys or Project Secrets in client-side code.** These must be stored securely on your server or using Appwrite Functions with environment variables.
- **Use Environment Variables** for all configuration, including Project ID, API Endpoint, and any API Keys used in server-side contexts.
- **Implement robust error handling.** Never assume an API call will succeed. Catch and handle `AppwriteException` errors gracefully, providing user-friendly feedback.
- **Version your API.** When making breaking changes to your Database schemas or Functions, use a versioning strategy to avoid breaking existing clients.

### Authentication & Users

- **Prefer OAuth2 providers** (Google, GitHub, etc.) for a seamless user experience and to avoid managing passwords, when appropriate for your use case.
- **Enforce strong password policies** if using email/password authentication (`minPasswordLength`).
- **Use Server-side SDKs for sensitive operations.** Any action that should be protected (e.g., deleting a user, modifying roles) must be performed from a secure Server SDK or a Function, not the Client SDK.
- **Leverage User Preferences and Custom Data:** Store non-sensitive user-specific settings in the user's `prefs` object.
- **Verify email addresses** for users who sign up with email/password to prevent spam accounts.

### Databases

- **Design your database structure carefully** before creating collections. Changes to permissions and indexes can be complex later.
- **Use Indexes strategically.** Create indexes on attributes you frequently query, filter, or order by to ensure optimal read performance.
- **Implement strict Database Permissions:** Follow the principle of least privilege.
  - **Read permissions:** Use role-based access (e.g., `user:{userId}`) to ensure users can only read documents intended for them.
  - **Write permissions:** Be extremely restrictive. Often, writes should be handled via Appwrite Functions to enforce business logic and validation, not directly from the client.
- **Validate Data at the Attribute Level:** Use the `required`, `default`, `array`, and `min`/`max` constraints in your attribute rules to ensure data integrity.
- **Avoid storing large, binary data** (like images/files) in Database documents. Store a reference to the file ID in Storage instead.

### Storage

- **Set appropriate File Permissions:** Just like databases, control who can view, update, and delete files using roles. A common pattern is `user:{userId}` for private user files and `role:all` for public assets.
- **Use Anti-Virus Scans:** Enable the anti-virus scan for your Storage bucket to protect your users from malicious uploads.
- **Pre-signed URLs:** For secure uploads and downloads directly from the client, generate pre-signed URLs from a server-side SDK or Function instead of giving clients broad write/read access to the bucket.

### Appwrite Functions

- **Keep Functions small and focused** on a single task (Single Responsibility Principle).
- **Use Environment Variables** for all secrets (API keys, database IDs) and configuration. Never hardcode them.
- **Implement proper logging.** Use `console.log` and `console.error` to track execution and debug issues. These logs are visible in the Appwrite Console.
- **Manage Dependencies carefully:** Only include necessary libraries in your `vendor` directory to keep deployment packages small and cold starts fast.
- **Use Functions as an API middleware layer:** Handle complex business logic, data validation, aggregation, and third-party API calls here instead of in the client.

### Security

- **HTTPS Everywhere:** Ensure your custom domain is configured with SSL and all API calls are made over HTTPS.
- **Validate Input on the Server:** Even if you have client-side validation, all data from the client must be re-validated in your Server SDKs or Functions.
- **Regularly Review Access Logs:** Monitor the Audit and Logs sections in the Appwrite Console for suspicious activity.
- **Use API Keys with Scopes:** When creating API Keys for server-side use, limit their permissions (scopes) to only what is absolutely necessary for their task.

### Performance & Optimization

- **Implement Data Pagination:** Always use `limit()` and `offset()` (or cursors) when listing documents or files to avoid loading massive datasets.
- **Use `select()` to limit fields:** When fetching documents, only request the attributes you need for the current view.
- **Cache Responses:** For data that doesn't change frequently, implement a caching strategy (e.g., in-memory, Redis) on your server or using a Function's execution context to reduce database reads.
- **Lazy-load non-essential data:** Fetch user profiles, comments, or other secondary information only when the user requests it.
