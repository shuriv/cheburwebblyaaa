

## AGENTS.md - Project: **Cheburweb - Simple Web Browser**

### Project Overview

**Cheburweb** is a lightweight, customizable web browser built using the **Wails** framework and **Golang**. The browser should support basic navigation (back, forward, refresh), an address bar, settings page, local account management, and an option to choose between multiple search engines (e.g., DuckDuckGo, Google, Yandex, Bing). The goal is to create a smooth and modern web browsing experience with a clean, easy-to-use interface.

---

### **Project Structure**

* **Frontend**: HTML for the layout, CSS for styling, and JavaScript for interactivity.
* **Backend**: Golang code for managing browser logic (navigation, search engine switching, URL handling).
* **Search Engine**: Ability to switch between different search engines like Google, DuckDuckGo, Yandex, Bing.
* **Settings & Account Page**: Manage user preferences and accounts.

---

### **1. Setup and Initialization**

#### 1.1 Install Wails Framework

First, install the Wails framework on your system by running the following command:

```bash
go install github.com/wailsapp/wails/v2/cmd/wails@latest
```

#### 1.2 Create Project

Create a new Wails project:

```bash
wails init -n Cheburweb
cd Cheburweb
```

This command initializes a basic Wails project structure.

---

### **2. Frontend Design (HTML + CSS)**

The frontend should consist of an address bar, search engine selector, navigation controls, and pages for account settings. Here's the full design:

#### 2.1 HTML Layout

This is the complete HTML structure for the browser interface:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cheburweb Browser</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div id="app-container">
        <!-- Navigation Bar -->
        <header id="nav-bar">
            <div class="nav-buttons">
                <button id="backBtn" title="Go Back"><img src="icons/back.png" alt="Back"></button>
                <button id="forwardBtn" title="Go Forward"><img src="icons/forward.png" alt="Forward"></button>
                <button id="refreshBtn" title="Refresh"><img src="icons/refresh.png" alt="Refresh"></button>
            </div>

            <!-- Address Bar -->
            <input type="text" id="address-bar" placeholder="Enter URL..." autocomplete="off">

            <!-- Search Engine Selector -->
            <select id="searchEngineSelector" title="Choose Search Engine">
                <option value="duckduckgo">DuckDuckGo</option>
                <option value="google">Google</option>
                <option value="yandex">Yandex</option>
                <option value="bing">Bing</option>
            </select>

            <!-- Account and Settings -->
            <div class="account-settings">
                <button id="settingsBtn" title="Settings"><img src="icons/settings.png" alt="Settings"></button>
                <button id="accountBtn" title="Account"><img src="icons/account.png" alt="Account"></button>
            </div>
        </header>

        <!-- Main Content (Browser View) -->
        <main id="browser-view">
            <iframe id="browser-frame" src="" frameborder="0"></iframe>
        </main>
    </div>

    <script src="main.js"></script>
</body>
</html>
```

In this layout:

* **Navigation buttons**: Back, Forward, Refresh with icons.
* **Address bar** for inputting URLs.
* **Search Engine Selector** dropdown.
* **Account and Settings buttons** with icons for easy access.

#### 2.2 CSS Styling

Here's the detailed CSS to make the layout beautiful and responsive:

```css
/* Reset default browser styling */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

/* General body styling */
body {
    font-family: 'Arial', sans-serif;
    background-color: #f4f4f4;
    display: flex;
    flex-direction: column;
    height: 100vh;
}

/* Main container for the browser */
#app-container {
    display: flex;
    flex-direction: column;
    height: 100%;
}

/* Navigation bar */
#nav-bar {
    background-color: #2c3e50;
    padding: 10px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: white;
}

/* Buttons and icons in the navigation bar */
.nav-buttons button,
.account-settings button {
    background: none;
    border: none;
    color: white;
    cursor: pointer;
    padding: 10px;
    transition: background 0.3s ease;
}

.nav-buttons button:hover,
.account-settings button:hover {
    background-color: #34495e;
}

/* Address bar */
#address-bar {
    width: 60%;
    padding: 8px;
    font-size: 16px;
    border: 1px solid #34495e;
    border-radius: 4px;
    background-color: white;
    margin: 0 20px;
    transition: width 0.3s ease;
}

#address-bar:focus {
    width: 80%;
    outline: none;
    border-color: #1abc9c;
}

/* Search engine selector */
#searchEngineSelector {
    padding: 8px;
    background-color: #34495e;
    color: white;
    border: 1px solid #2c3e50;
    border-radius: 4px;
    font-size: 16px;
}

/* Main browser view */
#browser-view {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: white;
    padding: 20px;
}

#browser-frame {
    width: 100%;
    height: 100%;
    border: none;
    border-radius: 8px;
}

/* Responsive layout for mobile devices */
@media (max-width: 768px) {
    #address-bar {
        width: 70%;
    }
    
    #searchEngineSelector {
        width: 50%;
    }
    
    #nav-bar {
        flex-direction: column;
        align-items: stretch;
    }
}
```

### **3. Backend Logic in Go**

#### 3.1 Open URL Handling

We need to create functions in Go to handle URL navigation and control. This Go code uses the Wails framework to interact with the frontend:

```go
package main

import (
    "github.com/wailsapp/wails/v2/pkg/runtime"
    "log"
)

// OpenURL handles opening a URL in the embedded browser.
func OpenURL(url string) {
    // Open the URL in the browser view
    if err := runtime.Window.OpenURL(url); err != nil {
        log.Println("Error opening URL:", err)
    }
}
```

#### 3.2 Search Engine Switching

Here’s how you can handle switching between search engines based on the user's selection:

```go
func HandleSearch(query, engine string) {
    var searchURL string

    switch engine {
    case "google":
        searchURL = "https://www.google.com/search?q=" + query
    case "duckduckgo":
        searchURL = "https://duckduckgo.com/?q=" + query
    case "yandex":
        searchURL = "https://yandex.com/search/?text=" + query
    case "bing":
        searchURL = "https://www.bing.com/search?q=" + query
    default:
        searchURL = "https://www.google.com/search?q=" + query
    }

    // Open the selected search engine's results page
    OpenURL(searchURL)
}
```

#### 3.3 Browser Controls (Back, Forward, Refresh)

We’ll add functions for the navigation controls like Back, Forward, and Refresh:

```go
func NavigateBack() {
    if err := runtime.Window.RunJS("window.history.back()"); err != nil {
        log.Println("Error navigating back:", err)
    }
}

func NavigateForward() {
    if err := runtime.Window.RunJS("window.history.forward()"); err != nil {
        log.Println("Error navigating forward:", err)
    }
}

func RefreshPage() {
    if err := runtime.Window.RunJS("window.location.reload()"); err != nil {
        log.Println("Error refreshing page:", err)
    }
}
```

### **4. Frontend JS for Interactivity**

We use JavaScript to handle user interactions (button clicks, address bar input, search engine changes):

```javascript
document.getElementById("searchEngineSelector").addEventListener("change", function() {
    const engine = this.value;
    const query = document.getElementById("address-bar").value;
    window.wails.Invoke('HandleSearch', query, engine);
});

document.getElementById("backBtn").addEventListener("click", function() {
    window.wails.Invoke('NavigateBack');
});

document.getElementById("forwardBtn").addEventListener("click", function() {
    window.wails.Invoke('NavigateForward');
});

document.getElementById("refreshBtn").addEventListener("click", function() {
    window.wails.Invoke('RefreshPage');
});

document.getElementById("address-bar").addEventListener("keydown", function(event) {
    if (event.key === 'Enter') {
        const url = this.value;
        window.wails.Invoke('OpenURL', url);
    }
});
```

### **5. Settings and Account Pages (Optional)**

You can create settings and account management pages as needed. For instance:

```html
<!-- Settings Page -->
<div id="settings-page">
    <h3>Settings</h3>
    <form id="settingsForm">
        <label for="defaultSearchEngine">Default Search Engine:</label>
        <select id="defaultSearchEngine">
            <option value="google">Google</option>
            <option value="duckduckgo">DuckDuckGo</option>
            <option value="bing">Bing</option>
            <option value="yandex">Yandex</option>
        </select>
        <button type="submit">Save Settings</button>
    </form>
</div>
```

---

### **6. Testing and Deployment**

After writing the Go functions and frontend code, you can test the browser:

1. Run the application locally:

   ```bash
   wails dev
   ```
2. Package the app for distribution:

   ```bash
   wails build
   ```

This will compile the project and create a native desktop application.

#### **7. Structure/Stack:
1.https://feathericons.com/
2.https://heroicons.com/
3.jQuery

#### **8. Elements i want to see in the project (NECESSARILY):
1.
2.
3.

---

### **Conclusion**

This prompt outlines the **Cheburweb** project, guiding you through:

* A detailed HTML layout for the browser interface.
* Enhanced CSS for a modern, responsive design.
* Go backend logic for handling browser functionality (URL loading, search engine switching, navigation).
* Frontend JavaScript to manage interactions between the user and the browser.
