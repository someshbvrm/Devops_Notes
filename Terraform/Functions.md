
---

### Key Characteristics

*   **No User-Defined Functions:** You cannot write your own functions; you can only use the ones provided by HashiCorp Configuration Language (HCL).
*   **Pure:** They do not interact with the outside world and will always return the same output for the same input.
*   **Called with Expression Syntax:** They use the syntax `function_name(argument1, argument2)`.

---

### Syntax and Usage

You use functions in expressions, most commonly in:
*   `resource` arguments (`name = lower("PROD-INSTANCE")`)
*   `output` values
*   `variable` validation and default values
*   `local` values
*   Dynamic blocks and other expressions

```hcl
# Example: Using the lower() and length() functions
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
  # Using a function to transform a string
  tags = {
    Name = lower("PRODUCTION-APP") # Result: "production-app"
  }
}

# Using a function in an output
output "instance_id_length" {
  # Calculate the length of the instance ID string
  value = length(aws_instance.example.id)
}

# Using a function for a default variable value
variable "app_port" {
  description = "Port for the application"
  type        = number
  default     = tonumber("8080") # Converts string "8080" to number 8080
}
```

---

### Categories of Functions

Terraform functions are broadly grouped into several categories. Here are the most essential ones with examples:

#### 1. String Functions
Manipulate and work with text strings.

*   `upper("hello")` → `"HELLO"`
*   `lower("HELLO")` → `"hello"`
*   `replace("hello-world", "-", "_")` → `"hello_world"`
*   `substr("hello world", 0, 5)` → `"hello"`
*   `trimprefix("aws_instance.my_instance", "aws_")` → `"instance.my_instance"`

#### 2. Numeric Functions
Perform mathematical operations.

*   `max(5, 12, 9)` → `12`
*   `min(5, 12, 9)` → `5`
*   `ceil(4.3)` → `5`
*   `floor(4.7)` → `4`
*   `parseint("15", 10)` → `15` (parses a string as an integer of base 10)

#### 3. Collection Functions
Work with lists, maps, and sets.

*   **For Lists:**
    *   `length(["a", "b", "c"])` → `3`
    *   `element(["a", "b", "c"], 1)` → `"b"` (0-based indexing)
    *   `slice(["a", "b", "c", "d"], 1, 3)` → `["b", "c"]`
    *   `concat(["a", "b"], ["c", "d"])` → `["a", "b", "c", "d"]`
*   **For Maps:**
    *   `keys({name = "App", env = "dev"})` → `["env", "name"]`
    *   `values({name = "App", env = "dev"})` → `["dev", "App"]`
    *   `lookup({name = "App"}, "name", "default")` → `"App"` (gets value or a default if key doesn't exist)

#### 4. Encoding Functions
Encode and decode data in different formats.

*   `jsonencode({"key" = "value"})` → `{"key":"value"}` (converts HCL to JSON string)
*   `yamlencode({"key" = "value"})` → `"key": "value\n"` (converts HCL to YAML string)
*   `filebase64("path/to/file.zip")` → (reads a file and returns its base64-encoded content)

#### 5. Filesystem Functions (**Caution: Use sparingly!**)
Read files from the local disk. **These make your configuration non-portable** because the result depends on a file on the machine where `terraform apply` is run.

*   `file("path/to/file.txt")` → reads file contents as a string
*   `fileexists("path/to/file.txt")` → `true` or `false`

#### 6. Date and Time Functions
Work with timestamps.

*   `timestamp()` → returns the current date and time in RFC 3339 format (e.g., `"2023-10-27T08:47:12Z"`). **This function is the exception to the "pure" rule; it changes value on every run.**
*   `timeadd("2023-10-27T00:00:00Z", "24h")` → `"2023-10-28T00:00:00Z"`

#### 7. Type Conversion Functions
Convert values from one type to another.

*   `tostring(123)` → `"123"`
*   `tonumber("123")` → `123`
*   `tobool("true")` → `true`

#### 8. Cryptographic and Hash Functions
Generate hashes and UUIDs.

*   `md5("hello")` → `"5d41402abc4b2a76b9719d911017c592"`
*   `sha256("hello")` → `"2cf24d...b982"`
*   `uuidv5("ns:URL", "www.example.com")` → (generates a name-based UUID)

---

### How to Experiment with Functions: `terraform console`

The easiest way to test and learn about functions is using the built-in console:

```bash
terraform console
> lower("TEST-VALUE")
"test-value"
> length(["a", "b"])
2
> jsonencode({"name" = "test"})
"{\"name\":\"test\"}"
> exit
```

This provides a safe, interactive environment to try functions before putting them in your configuration.