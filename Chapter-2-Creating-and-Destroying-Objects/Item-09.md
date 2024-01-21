# Item 9: Prefer try-with-resources to try-finally

> ## Key Points:
> `try-finally`:
>   - It is a common way to ensure that a resource is closed properly after use, but it is verbose, error-prone, and difficult to read.
> 
> `try-with-resources`:
>   - It is a newer construct that simplifies the management of resources that implement the AutoCloseable interface, such as streams, sockets, and database connections. <br>
>   - It automatically closes the resources declared in the try clause, in the reverse order of their creation, regardless of whether the try block completes normally or abruptly. <br>
>   - It also preserves the exceptions thrown by the try block and the resources, and adds them as suppressed exceptions to the primary exception. <br>
> 
> ***It's the preferred way to use resources that need to be closed, as it makes the code more clear, concise, and correct.***

## Detailed Notes:

*   **Issue with Manual Resource Closure:**
    
    *   Java libraries have resources (e.g., InputStream, OutputStream, java.sql.Connection) requiring manual closure.
    *   Closing resources manually is often overlooked, leading to performance issues.
    *   Finalizers are not a reliable safety net for resource closure (Item 8).
*   **Historical Approach - try-finally:**
    
    *   Traditionally, try-finally statements ensured proper resource closure.
    *   Examples:
        *   Method to read the first line of a file.
            
            ```java
            static String firstLineOfFile(String path) throws IOException {
                BufferedReader br = new BufferedReader(new FileReader(path));
                try {
                    return br.readLine();
                } finally {
                    br.close();
                }
            }
            ```
        *   Method to copy data between files.
*   **Issues with try-finally and Multiple Resources:**
    
    *   try-finally becomes cumbersome with multiple resources.
    *   Example:
        
        ```java
        static void copy(String src, String dst) throws IOException {
            InputStream in = new FileInputStream(src);
            try {
                OutputStream out = new FileOutputStream(dst);
                try {
                    byte[] buf = new byte[BUFFER_SIZE];
                    int n;
                    while ((n = in.read(buf)) >= 0)
                        out.write(buf, 0, n);
                } finally {
                    out.close();
                }
            } finally {
                in.close();
            }
        }
        ```
    *   Even experienced programmers may make mistakes with this approach.
*   **Introduction of try-with-resources (Java 7):**
    
    *   Java 7 introduced the try-with-resources statement.
    *   Resources must implement the AutoCloseable interface.
    *   AutoCloseable consists of a single void-returning close method.
*   **Examples of try-with-resources:**
    
    *   Improved version of the firstLineOfFile method.
        
        ```java
        static String firstLineOfFile(String path) throws IOException {
            try (BufferedReader br = new BufferedReader(new FileReader(path))) {
                return br.readLine();
            }
        }
        ```
    *   Enhanced version of the copy method with try-with-resources.
        
        ```java
        static void copy(String src, String dst) throws IOException {
            try (InputStream in = new FileInputStream(src);
                 OutputStream out = new FileOutputStream(dst)) {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0)
                    out.write(buf, 0, n);
            }
        }
        ```
*   **Advantages of try-with-resources:**
    
    *   Code is shorter and more readable.
    *   Provides better diagnostics.
    *   Suppresses unnecessary exceptions to preserve the relevant ones.
    *   Allows catch clauses on try-with-resources statements without additional nesting.
*   **Additional Information:**
    
    *   Even correct try-finally code has a subtle deficiency with exception handling.
    *   Java 7's try-with-resources simplifies and enhances resource management.
    *   Recommends always using try-with-resources over try-finally for better code and exception handling.
