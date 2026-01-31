# Simple Guide to Prevent XSS Attacks

## What is XSS?
*XSS (Cross-Site Scripting)* is when a hacker manages to execute malicious code in your browser.  
Think of how hackers put viruses on computers: with XSS, they put "viruses" in the web pages you visit.

### What can happen if a site has XSS?
- *Password theft*: The hacker can steal your credentials
- *Session hijacking*: They can impersonate you on the site
- *Malicious redirects*: Takes you to fake sites
- *Page modification*: Changes what you see on the site

## How It Works in Practice (Example)

### ❌ WITHOUT PROTECTION:
Let's say you have a simple blog with a comment section:

```html
<!-- Blog page HTML -->
<h1>My Blog Post</h1>
<p>This is my blog content...</p>

<h2>Comments</h2>
<div id="comments">
  <!-- Comments loaded here -->
</div>

<!-- JavaScript code -->
<script>
// ❌ VULNERABLE CODE - Directly inserts user input
function addComment(commentText) {const commentDiv = document.getElementById('comments');
    commentDiv.innerHTML += <div class="comment">${commentText}</div>;}

// User submits this comment:
const maliciousComment = "<script>alert('Hacked!');</script><img src='x' onerror='stealCookies()'>";
addComment(maliciousComment);
</script>

### ❌ What happen:
<!-- Blog page HTML - same as before -->

<!-- JavaScript code - FIXED -->
<script>
// ✅ SECURE CODE - Encodes user input
function addCommentSafe(commentText) {
    const commentDiv = document.getElementById('comments');
    
    // Method 1: Use textContent (recommended)
    const newComment = document.createElement('div');
    newComment.className = 'comment';
    newComment.textContent = commentText; // Automatically escapes HTML
    
    // Method 2: Manual HTML encoding
    // const encodedText = htmlEncode(commentText);
    // commentDiv.innerHTML += <div class="comment">${encodedText}</div>;
    
    commentDiv.appendChild(newComment);
}

// Helper function for HTML encoding
function htmlEncode(text) {
    return text
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#x27;');
}

// Same malicious comment
const maliciousComment = "<script>alert('Hacked!');</script><img src='x' onerror='stealCookies()'>";
addCommentSafe(maliciousComment);
</script>

User enters: <script>alert('Hacked!');</script><img src='x' onerror='stealCookies()'>

The site directly inserts it: commentDiv.innerHTML += <div class="comment">${maliciousComment}</div>

the result displayed: <div class="comment">

### ✅ WITH PROTECTION:
Same blog, but with proper protection:
<!-- Blog page HTML - same as before -->

<!-- JavaScript code - FIXED -->
<script>
// SECURE CODE - Encodes user input
function addCommentSafe(commentText) {
    const commentDiv = document.getElementById('comments');
    
    // Method 1: Use textContent (recommended)
    const newComment = document.createElement('div');
    newComment.className = 'comment';
    newComment.textContent = commentText; // Automatically escapes HTML
    
    // Method 2: Manual HTML encoding
    // const encodedText = htmlEncode(commentText);
    // commentDiv.innerHTML += <div class="comment">${encodedText}</div>;
    
    commentDiv.appendChild(newComment);
}

// Helper function for HTML encoding
function htmlEncode(text) { return text
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#x27;');}

// Same malicious comment
const maliciousComment = "<script>alert('Hacked!');</script><img src='x' onerror='stealCookies()'>";
addCommentSafe(maliciousComment);
</script>

what happens?

User enters: <script>alert('Hacked!');</script><img src='x' onerror='stealCookies()'>

textContent automatically converts it to: &lt;script&gt;alert('Hacked!');&lt;/script&gt;&lt;img src='x' onerror='stealCookies()'&gt;

Result displayed: <div class="comment">&lt;script&gt;alert('Hacked!');&lt;/script&gt;&lt;img src='x' onerror='stealCookies()'&gt;</div>
[22:20, 31/01/2026] muditha peters: The text is displayed as plain text, NOT executed

The text is displayed as plain text, NOT executed!

🎯 Conclusion:
Web security is not about how complex your code is, but about how you handle data you don't control. XSS prevention comes down to one fundamental rule:

"Never trust user input."
