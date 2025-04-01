# GolangMicroservices
This codebase consists of two main parts: an HTML document (for the website's frontend) and a Go programming language backend server (for handling HTTP requests). Let's break it into its components:

---

### **1. HTML Code (Frontend)**

The HTML includes two primary sections:

#### A. **Form Submission Page**
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8"/>
    </head>
    <body>
        <div>
            <form method="POST" action="/form">
            <label>Name</label><input name="name" type="text" value=""/>
            <label>Address</label><input name ="address" type="text" value=""/>

            <input type="submit" value="submit"/>
            </form>
        </div>
    </body>
</html>
```

- **Purpose**:
  - Displays a form with two input fields: `name` and `address`.
  - Data is submitted using the **POST** method to the `/form` endpoint when the user clicks the Submit button.

- **Key Elements**:
  - `<form method="POST" action="/form">`: Sends a POST request to the `/form` server endpoint.
  - `<input name="name">` and `<input name="address">`: Allow users to provide their name and address in text fields.

---

#### B. **Static Website Title Page**
```html
<html>
    <head>
        <title>Static Website</title>
    </head>
    <body>
        <h2>Static website</h2>
    </body>
</html>
```

- **Purpose**:
  - This is another HTML page that simply displays the title "Static Website" and the heading "Static website."
  - It could serve as a basic informational/static page for the website.

- **Note**: The inclusion of two `<html>` documents suggests an oversight in the organization of the HTML code. Typically, you should only have one `<html>` document per file or serve individual pages dynamically for separate routes on the server.

---

### **2. Go Code (Backend)**

The Go program acts as a web server that handles client requests submitted through routes. Let's break it down:

#### A. **formHandler Function**
```go
func formHandler(w http.ResponseWriter, r *http.Request) {
	if err := r.ParseForm(); err != nil {
		fmt.Fprintf(w, "ParseForm() err: %v", err)
		return
	}
	fmt.Fprintf(w, "POST request successful")
	name := r.FormValue("name")
	address := r.FormValue("address")
	fmt.Fprintf(w, "Name = %s\n", name)
	fmt.Fprintf(w, "Address = %s\n", address)
}
```

- **Purpose**:
  - Handles POST requests made to the `/form` endpoint (which corresponds to the form in the HTML code).
  - Parses the submitted form data and prints the values of `name` and `address` back to the client.

- **How It Works**:
  1. `r.ParseForm()`: Parses the POST form data.
     - If an error occurs during parsing, it writes the error message to the response.
  2. `r.FormValue("name")` and `r.FormValue("address")` retrieve the submitted `name` and `address` values, respectively.
  3. Writes back a response with the parsed name and address to the user.

---

#### B. **helloHandler Function**
```go
func helloHandler(w http.ResponseWriter, r *http.Request) {
	if r.URL.Path != "/hello" {
		http.Error(w, "404 not found", http.StatusNotFound)
		return
	}
	if r.Method != "GET" {
		http.Error(w, "method is not supported", http.StatusNotFound)
		return
	}
	fmt.Fprintf(w, "hello!")
}
```

- **Purpose**:
  - Handles GET requests sent to the `/hello` endpoint.
  - Responds with the message `"hello!"` if the path and request method are correct.

- **How It Works**:
  1. Checks if the request's URL path is `/hello` and responds with a "404 not found" error if it's not.
  2. Ensures the HTTP method is **GET** and returns another error if it isn't.
  3. If both conditions are met, it writes `"hello!"` to the response.

---

#### C. **main Function**
```go
func main() {
	fileServer := http.FileServer(http.Dir("./static"))
	http.Handle("/", fileServer)
	http.HandleFunc("/form", formHandler)
	http.HandleFunc("/hello", helloHandler)

	fmt.Printf("Starting server at port 8080\n")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```

- **Purpose**:
  - Sets up the HTTP server to handle specific routes and serve static files.

- **How It Works**:
  1. **Serve Static Files**:
     - `http.FileServer(http.Dir("./static"))`: Serves static files (like CSS, images, or other HTML/JS files) from the `./static` directory.
     - The `/` route serves these static files.
  2. **Register Handlers**:
     - `/form`: Maps to the `formHandler` for handling form submissions.
     - `/hello`: Maps to the `helloHandler` for a simple "hello!" response.
  3. **Start Server**:
     - `http.ListenAndServe(":8080", nil)`: Starts the HTTP server on port `8080`. If there's an error, it logs the error and exits.

---

### **How This Code Works Together**
1. **Frontend**:
   - A user visits the website and interacts with either the form (HTML input fields) or other parts of the static site.
   - Submitted form data is sent via a POST request to the `/form` backend.

2. **Backend**:
   - HTTP requests are routed to specific handlers (`formHandler`, `helloHandler`, or file serving).
   - For `/form`, the `formHandler` processes submitted form data and displays the parsed responses (name and address).
   - For `/hello`, the `helloHandler` verifies the client request path/method and responds with `"hello!"`.
   - Static files are served for any other file requests under the `./static` directory.

---

### Notes and Potential Improvements
1. **Invalid HTML Structure**:
   - The HTML contains two `<html>` documents. This is a mistake that should be rectified.

2. **Frontend-Backend Integration**:
   - Organize front-end files into a `/static` directory so they are served properly by the Go server.

3. **Error Handling**:
   - Improve user-facing error messages for invalid routes or methods.

4. **Port & Address Exposure**:
   - The port `8080` is hardcoded. It might be better to use environment variables for flexibility.

