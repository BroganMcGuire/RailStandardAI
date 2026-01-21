# RailStandardAI - Complete Code Explanation Guide for Beginners

## Table of Contents
1. [Project Overview](#project-overview)
2. [What This Application Does](#what-this-application-does)
3. [Technologies Used](#technologies-used)
4. [File Structure](#file-structure)
5. [Detailed Code Explanations](#detailed-code-explanations)
   - [Configuration Files](#configuration-files)
   - [Type Definitions](#type-definitions)
   - [Entry Point Files](#entry-point-files)
   - [Main Application](#main-application)
   - [Services](#services)
   - [Components](#components)
6. [How Everything Works Together](#how-everything-works-together)
7. [Key Programming Concepts](#key-programming-concepts)

---

## Project Overview

**RailStandardAI** is a web application designed to help users interact with railway standards documents (PDF files) using artificial intelligence. Think of it as a smart assistant that reads your PDF documents and answers questions about them.

### What This Application Does

Imagine you have many technical PDF documents about railway standards, and you need to find specific information quickly. Instead of reading through hundreds of pages manually, this application lets you:

1. **Upload PDF Documents**: Drop your PDF files into the application
2. **Ask Questions**: Type questions in plain English about the content
3. **Get AI Answers**: The application uses Google's Gemini AI to read your documents and provide accurate answers
4. **View References**: Click on citations to see the exact page and clause in the original PDF
5. **Store Locally**: All your documents are saved in your browser - no cloud storage needed

### Technologies Used

For beginners, here's what each technology does:

- **React**: A JavaScript library for building user interfaces (the visual parts you see and interact with)
- **TypeScript**: A programming language that adds "types" to JavaScript, making code safer and easier to understand
- **Vite**: A modern build tool that bundles your code and makes development faster
- **Tailwind CSS**: A styling framework for making the app look good without writing lots of CSS
- **Google Gemini AI**: Artificial intelligence service that understands and answers questions
- **PDF.js**: A library from Mozilla that reads and displays PDF files in the browser
- **IndexedDB**: Browser storage that works like a database on your computer

---

## File Structure

```
RailStandardAI/
├── package.json          # Lists all dependencies and project info
├── tsconfig.json         # Tells TypeScript how to compile the code
├── vite.config.ts        # Configures the build process
├── index.html            # The main HTML page that loads everything
├── index.tsx             # The starting point of our React app
├── App.tsx               # The main component with all the app logic
├── types.ts              # Defines the shape of our data
├── services/             # Backend-like functions
│   ├── pdfService.ts     # Extracts text from PDF files
│   ├── storageService.ts # Saves and retrieves data from browser
│   └── geminiService.ts  # Communicates with AI
└── components/           # Reusable UI pieces
    ├── ClauseModal.tsx   # Shows PDF in a popup window
    ├── DocumentList.tsx  # Displays uploaded documents
    └── EmptyState.tsx    # Shows when no documents uploaded
```

---

## Detailed Code Explanations

### Configuration Files

#### 1. package.json

**Purpose**: This file tells Node.js (the JavaScript runtime) what your project is called, what libraries it needs, and what commands you can run.

**Key Sections**:
```json
{
  "name": "rail-standard-ai",  // Project name
  "version": "1.0.0",           // Version number
  "type": "module",             // Use modern JavaScript modules
  
  "scripts": {
    "dev": "vite",              // Run development server
    "build": "tsc && vite build", // Build for production
    "preview": "vite preview"   // Preview production build
  },
  
  "dependencies": {             // Libraries needed to run the app
    "@google/genai": "^1.36.0", // Google AI SDK
    "react": "^19.2.3",         // React library
    "react-dom": "^19.2.3"      // React for web browsers
  },
  
  "devDependencies": {          // Libraries needed for development only
    "@types/react": "^19.0.0",  // Type definitions for React
    "typescript": "^5.7.2",     // TypeScript compiler
    "vite": "^6.0.3"            // Build tool
  }
}
```


**Beginner Explanation**:
- `^` symbol means "compatible with this version or newer minor versions"
- Dependencies are external code libraries that save you from writing everything from scratch
- When you run `npm install`, it downloads all these libraries

---

#### 2. tsconfig.json

**Purpose**: Configures how TypeScript should convert (compile) your TypeScript code into regular JavaScript that browsers can understand.

```json
{
  "compilerOptions": {
    "target": "ESNext",          // Compile to latest JavaScript version
    "lib": ["DOM", "ESNext"],    // Include browser and modern JS features
    "jsx": "react-jsx",          // How to handle React JSX syntax
    "strict": true,              // Enable all strict type checking
    "module": "ESNext",          // Use modern module system
    "moduleResolution": "Node",  // How to find imported modules
    "noEmit": true               // Don't output JS files (Vite handles this)
  }
}
```

**Beginner Explanation**:
- TypeScript adds "types" to JavaScript - like saying "this variable must be a number"
- The compiler checks your code for mistakes before it runs
- "strict: true" means TypeScript will be very picky, helping catch more errors

---

#### 3. vite.config.ts

**Purpose**: Configures Vite, the tool that bundles your code and serves it during development.

```typescript
import { defineConfig, loadEnv } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig(({ mode }) => {
  // Load environment variables (like API keys)
  const env = loadEnv(mode, process.cwd(), '');
  
  return {
    plugins: [react()],           // Use React plugin
    base: './',                   // Asset path for GitHub Pages
    define: {
      // Replace 'process.env.API_KEY' in code with actual value
      'process.env.API_KEY': JSON.stringify(env.API_KEY),
    },
    build: {
      outDir: 'dist',             // Output folder for built files
      assetsDir: 'assets',        // Folder for images, fonts, etc.
      sourcemap: false,           // Don't generate source maps
    },
    server: {
      port: 3000                  // Development server runs on port 3000
    }
  };
});
```

**Line-by-Line Explanation**:
- **Line 1-2**: Import tools from Vite
- **Line 4**: `defineConfig` creates configuration with a function
- **Line 6**: `mode` tells us if we're in development or production
- **Line 7**: `loadEnv` loads secret keys from .env files
- **Line 11**: Tells Vite we're using React
- **Line 12**: `base: './'` makes the app work on GitHub Pages
- **Line 14-16**: Replaces `process.env.API_KEY` in code with the real API key from GitHub Secrets
- **Line 18-22**: Configuration for building the final production app
- **Line 23-25**: Development server will run on `http://localhost:3000`

**Beginner Concepts**:
- **Environment Variables**: Secret values (like API keys) that shouldn't be in your code
- **Build Process**: Converting your source code into optimized files for the web
- **Development Server**: A local web server that reloads when you change code

---

### Type Definitions

#### types.ts

**Purpose**: Defines the "shape" of data used throughout the application. Think of it as blueprints for your data.

```typescript
// A RailDocument represents one uploaded PDF file
export interface RailDocument {
  id: string;                    // Unique identifier (like "abc-123-def")
  name: string;                  // File name (like "Standard-001.pdf")
  content: string;               // Extracted text from PDF
  size: number;                  // File size in bytes
  uploadDate: Date;              // When it was uploaded
  pdfData: Uint8Array;          // Raw PDF file data for viewing
}
```

**Beginner Explanation**:
- `interface` is like a contract - it says "any RailDocument MUST have these properties"
- `string` means text, `number` means a number, `Date` is a date/time
- `Uint8Array` is an array of bytes (raw file data)
- TypeScript will give you an error if you try to create a RailDocument without all these properties

```typescript
// A Citation is a reference to a specific part of a document
export interface Citation {
  standard: string;              // Which document (e.g., "Standard-001.pdf")
  clause: string;                // Which section (e.g., "3.2.1")
  page?: number;                 // Page number (? means optional)
}
```

**Beginner Explanation**:
- The `?` after `page` means it's optional - a Citation can exist without a page number
- This represents where the AI found information in the documents

```typescript
// A ChatMessage is one message in the conversation
export interface ChatMessage {
  id: string;                    // Unique identifier
  role: 'user' | 'assistant';   // Who sent it: user or AI
  text: string;                  // The message content
  citations?: Citation[];        // References (only for AI messages)
  timestamp: Date;               // When it was sent
}
```

**Beginner Explanation**:
- `'user' | 'assistant'` means role can ONLY be one of these two values
- `Citation[]` means an array (list) of Citation objects
- `citations?` with the `?` means assistant messages might have citations, user messages won't

```typescript
// GeminiResponse is what we get back from the AI
export interface GeminiResponse {
  answer: string;                // The AI's answer
  citations: Citation[];         // Where it found the info
}
```

**Beginner Explanation**:
- This defines what the AI service will return to us
- We use this to ensure we handle the AI's response correctly

---

### Entry Point Files

#### index.html

**Purpose**: The main HTML file that loads when you visit the website.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <!-- UTF-8 means it can display any character from any language -->
  
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <!-- Makes the site work well on mobile devices -->
  
  <title>RailStandard AI</title>
  <!-- Text shown in browser tab -->
  
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Loads Tailwind CSS styling library from CDN (Content Delivery Network) -->
  
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
  <!-- Loads PDF.js library for reading PDFs -->
  
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  <!-- Loads Inter font from Google Fonts -->
```

**Beginner Explanation**:
- HTML is the structure of web pages (like the skeleton)
- `<script>` tags load JavaScript code
- `<link>` tags load CSS stylesheets and fonts
- CDN means the files are loaded from fast servers around the world

```html
  <style>
    body { font-family: 'Inter', sans-serif; margin: 0; padding: 0; overflow: hidden; }
    /* Sets font, removes default spacing, hides scroll bars on body */
    
    .custom-scrollbar::-webkit-scrollbar { width: 6px; }
    /* Makes scrollbars only 6 pixels wide */
    
    .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
    /* Makes scrollbar track invisible */
    
    .custom-scrollbar::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 10px; }
    /* Styles the draggable part of scrollbar */
    
    @keyframes fade-in { from { opacity: 0; } to { opacity: 1; } }
    /* Animation that fades element from invisible to visible */
    
    .animate-fade-in { animation: fade-in 0.2s ease-out forwards; }
    /* Applies the fade-in animation over 0.2 seconds */
  </style>
```

**Beginner Explanation**:
- CSS styles control how things look (colors, sizes, positions)
- `@keyframes` defines animations
- `.custom-scrollbar` creates styled scrollbars instead of ugly default ones
- Animations make the UI feel smooth and professional

```html
  <script type="importmap">
  {
    "imports": {
      "react": "https://esm.sh/react@^19.2.3",
      "react-dom/": "https://esm.sh/react-dom@^19.2.3/",
      "@google/genai": "https://esm.sh/@google/genai@^1.37.0"
    }
  }
  </script>
  <!-- Import map tells the browser where to load these libraries from -->
</head>
```

**Beginner Explanation**:
- `importmap` is a way to tell the browser where to find JavaScript libraries
- `esm.sh` is a service that hosts npm packages for browsers
- This lets us use React and other libraries without installing them locally in development

```html
<body class="bg-slate-50">
  <!-- Sets light gray background -->
  
  <div id="root"></div>
  <!-- This is where our React app will be inserted -->
  
  <script type="module" src="index.tsx"></script>
  <!-- Loads our main TypeScript/React code -->
</body>
</html>
```

**Beginner Explanation**:
- The `<div id="root">` is empty initially
- React will fill it with all our app components
- `type="module"` means we're using modern JavaScript modules


---

#### index.tsx

**Purpose**: The entry point of the React application. This is the first JavaScript file that runs.

```typescript
import React from 'react';
// Import the React library
```

**Beginner Explanation**:
- `import` brings code from other files into this file
- `from 'react'` says "get this from the react library"

```typescript
import { createRoot } from 'react-dom/client';
// Import the function that connects React to the HTML page
```

**Beginner Explanation**:
- `{ createRoot }` uses "destructuring" to import just one specific function
- `react-dom/client` is the part of React that works with web browsers

```typescript
import App from './App';
// Import our main App component from App.tsx
```

**Beginner Explanation**:
- `'./App'` means "the file called App.tsx in the same folder"
- No need for `.tsx` extension when importing

```typescript
const container = document.getElementById('root');
// Find the <div id="root"> element from index.html
```

**Beginner Explanation**:
- `document.getElementById` is a browser function that finds HTML elements
- `container` will hold reference to that div element

```typescript
if (container) {
  // Only proceed if the root element exists
  
  const root = createRoot(container);
  // Create a React root in that container
```

**Beginner Explanation**:
- `if (container)` checks that we actually found the element
- `createRoot` prepares React to render into that element
- This is the newer way of starting React apps (React 18+)

```typescript
  root.render(
    <React.StrictMode>
      <App />
    </React.StrictMode>
  );
}
```

**Beginner Explanation**:
- `root.render()` tells React to display something
- `<React.StrictMode>` activates extra checks for bugs (only in development)
- `<App />` is our main component - this is JSX (looks like HTML but it's JavaScript)
- When this runs, React will put the App component into the `<div id="root">`

**Overall Flow**:
1. Find the root div in HTML
2. Create a React root there
3. Render the App component into it
4. React takes over and builds the entire user interface

---

### Services

Services are helper functions that handle specific tasks. They keep the main app code clean.

#### pdfService.ts

**Purpose**: Extracts text from PDF files so the AI can read them.

```typescript
declare const pdfjsLib: any;
// Tells TypeScript that pdfjsLib exists (it's loaded from CDN in index.html)
```

**Beginner Explanation**:
- `declare` means "trust me, this variable exists"
- We use this because pdfjsLib comes from an external script tag

```typescript
pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
// Tell PDF.js where to find its worker script
```

**Beginner Explanation**:
- PDF.js uses a "web worker" (a background thread) to process PDFs without freezing the page
- This line tells PDF.js where to download that worker from

```typescript
const MAX_PAGES_CONTEXT = 50;
// Only extract first 50 pages to prevent memory issues
```

**Beginner Explanation**:
- Large PDFs could crash the browser if we load everything
- This limits how much we process at once

```typescript
export async function extractTextFromPdf(file: File): Promise<string> {
```

**Beginner Explanation**:
- `export` makes this function available to other files
- `async` means this function does work that takes time (reading files)
- `file: File` means it expects a File object as input
- `Promise<string>` means it will eventually return a string

```typescript
  const arrayBuffer = await file.arrayBuffer();
  // Convert file to raw bytes (ArrayBuffer)
```

**Beginner Explanation**:
- `await` means "wait for this to finish"
- `.arrayBuffer()` gets the raw file data
- PDFs are binary files (not text), so we need the raw bytes

```typescript
  let pdf = null;
  // Variable to store the PDF document object
  
  try {
    // Try to load the PDF
    pdf = await pdfjsLib.getDocument({ 
      data: new Uint8Array(arrayBuffer),  // The PDF bytes
      disableAutoFetch: true,             // Don't automatically load all pages
      disableStream: true,                // Load entire file, don't stream
      stopAtErrors: false                 // Keep going if one page fails
    }).promise;
```

**Beginner Explanation**:
- `try` starts a block where errors might happen
- `pdfjsLib.getDocument()` loads the PDF file
- Configuration options control how PDF.js behaves
- `Uint8Array` is an array of bytes (numbers 0-255)

```typescript
    let fullText = '';
    // Variable to accumulate all extracted text
    
    if (!pdf || pdf.numPages === 0) {
      return "[Error: This PDF contains no readable pages.]";
    }
    // Check if PDF is valid
```

**Beginner Explanation**:
- `!pdf` means "if pdf is null/undefined"
- `pdf.numPages` tells us how many pages the PDF has
- Early return prevents errors if PDF is broken

```typescript
    const pagesToExtract = Math.min(pdf.numPages, MAX_PAGES_CONTEXT);
    // Extract up to 50 pages or total pages, whichever is less
```

**Beginner Explanation**:
- `Math.min()` returns the smaller of two numbers
- If PDF has 30 pages, extract 30. If it has 100 pages, extract only 50.

```typescript
    for (let i = 1; i <= pagesToExtract; i++) {
      // Loop through each page (PDFs are 1-indexed, not 0-indexed)
      
      try {
        const page = await pdf.getPage(i);
        // Get page object
        
        const textContent = await page.getTextContent();
        // Extract text content from page
```

**Beginner Explanation**:
- `for` loop runs multiple times, once per page
- `i` starts at 1 (first page) and goes up to the last page
- Each page must be loaded and processed separately

```typescript
        const pageText = textContent.items
          .map((item: any) => item.str)
          .join(' ');
        // Convert text items array into one string
```

**Beginner Explanation**:
- `textContent.items` is an array of text pieces
- `.map()` transforms each item, getting just the string part (`item.str`)
- `.join(' ')` combines all pieces with spaces between them

```typescript
        fullText += `[Page ${i}] ${pageText}\n\n`;
        // Add page marker and text to our full text
        
      } catch (pageErr) {
        console.warn(`Failed to extract text from page ${i}:`, pageErr);
        fullText += `[Page ${i}] [Text extraction failed for this page]\n\n`;
      }
    }
```

**Beginner Explanation**:
- `+=` means "add to the end of"
- We mark each page with `[Page 1]`, `[Page 2]`, etc.
- If a page fails, we catch the error and add a note instead of crashing

```typescript
    if (pdf.numPages > MAX_PAGES_CONTEXT) {
      fullText += `\n\n[Note: Only the first ${MAX_PAGES_CONTEXT} pages were indexed to ensure browser stability.]`;
    }
    // Add warning if we didn't extract all pages
```

**Beginner Explanation**:
- This tells users that we only processed part of a large document
- Prevents confusion about missing information

```typescript
    return fullText;
    // Return the extracted text
    
  } catch (err) {
    console.error("PDF Extraction Error:", err);
    throw new Error("Failed to extract text from PDF. The file might be corrupted, protected, or too complex.");
  }
```

**Beginner Explanation**:
- If anything goes wrong, catch the error
- `throw new Error()` creates a new error message
- This error will be shown to the user

```typescript
  finally {
    if (pdf) {
      try {
        await pdf.destroy();
        // CRITICAL: Release memory back to browser
      } catch (e) {
        console.error("Error destroying PDF instance:", e);
      }
    }
  }
}
```

**Beginner Explanation**:
- `finally` runs whether or not an error happened
- `pdf.destroy()` frees up memory used by the PDF
- PDFs can use a lot of memory, so cleanup is important
- Without this, the browser could slow down or crash


---

#### storageService.ts

**Purpose**: Saves and retrieves data from the browser's local storage (IndexedDB).

```typescript
import { RailDocument, ChatMessage } from '../types';
// Import our type definitions
```

```typescript
const DB_NAME = 'RailStandardsDB';        // Name of our database
const DOC_STORE = 'documents';            // Name of document storage
const CHAT_STORE = 'chat_history';        // Name of chat storage
const DB_VERSION = 2;                     // Database version number
```

**Beginner Explanation**:
- IndexedDB is like a database that lives in your browser
- These constants name different parts of our storage
- `DB_VERSION` increases when we change the database structure

```typescript
export async function initDB(): Promise<IDBDatabase> {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open(DB_NAME, DB_VERSION);
```

**Beginner Explanation**:
- `initDB` creates or opens the database
- `Promise` represents something that will complete in the future
- `indexedDB.open()` is a browser API for opening databases
- `(resolve, reject)` are functions we call when done or when an error happens

```typescript
    request.onerror = () => reject(request.error);
    // If opening fails, reject the promise with the error
    
    request.onsuccess = () => resolve(request.result);
    // If opening succeeds, resolve the promise with the database
```

**Beginner Explanation**:
- `onerror` and `onsuccess` are event handlers (they run when something happens)
- `resolve()` means "we succeeded, here's the result"
- `reject()` means "we failed, here's the error"

```typescript
    request.onupgradeneeded = (event) => {
      // This runs when database is first created or version increases
      
      const db = (event.target as IDBOpenDBRequest).result;
      // Get the database object
```

**Beginner Explanation**:
- `onupgradeneeded` is for setting up the database structure
- This is where we create "object stores" (like tables in a database)

```typescript
      if (!db.objectStoreNames.contains(DOC_STORE)) {
        db.createObjectStore(DOC_STORE, { keyPath: 'id' });
      }
      // Create documents store if it doesn't exist
      
      if (!db.objectStoreNames.contains(CHAT_STORE)) {
        db.createObjectStore(CHAT_STORE, { keyPath: 'id' });
      }
      // Create chat history store if it doesn't exist
    };
  });
}
```

**Beginner Explanation**:
- `objectStoreNames.contains()` checks if a store exists
- `createObjectStore()` creates a new storage area
- `keyPath: 'id'` means each item will be identified by its `id` property
- Like creating a table where the `id` field is the primary key

```typescript
export async function saveDocument(doc: RailDocument): Promise<void> {
  const db = await initDB();
  // Open the database first
  
  return new Promise((resolve, reject) => {
    const transaction = db.transaction(DOC_STORE, 'readwrite');
    // Start a transaction (a unit of work on the database)
    
    const store = transaction.objectStore(DOC_STORE);
    // Get the documents store
    
    const request = store.put(doc);
    // Save the document (put replaces if it exists)
    
    request.onsuccess = () => resolve();
    // Success!
    
    request.onerror = () => reject(request.error);
    // Failed :(
  });
}
```

**Beginner Explanation**:
- `transaction` is like starting a database operation
- `'readwrite'` means we want to both read and write data
- `store.put()` saves data (creates or updates)
- This pattern is common: open DB → start transaction → do operation → handle success/error

```typescript
export async function getAllDocuments(): Promise<RailDocument[]> {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const transaction = db.transaction(DOC_STORE, 'readonly');
    // 'readonly' because we're only reading, not changing
    
    const store = transaction.objectStore(DOC_STORE);
    const request = store.getAll();
    // Get all documents from the store
    
    request.onsuccess = () => resolve(request.result);
    // Return the array of documents
    
    request.onerror = () => reject(request.error);
  });
}
```

**Beginner Explanation**:
- Similar pattern but with `'readonly'` transaction
- `getAll()` retrieves every item in the store
- Returns an array of RailDocument objects

```typescript
export async function deleteDocument(id: string): Promise<void> {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const transaction = db.transaction(DOC_STORE, 'readwrite');
    const store = transaction.objectStore(DOC_STORE);
    const request = store.delete(id);
    // Delete the document with this id
    
    request.onsuccess = () => resolve();
    request.onerror = () => reject(request.error);
  });
}
```

**Beginner Explanation**:
- `store.delete(id)` removes an item by its id
- Needs `'readwrite'` permission since we're changing data

```typescript
export async function saveChatMessage(message: ChatMessage): Promise<void> {
  // Same pattern as saveDocument but for chat messages
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const transaction = db.transaction(CHAT_STORE, 'readwrite');
    const store = transaction.objectStore(CHAT_STORE);
    const request = store.put(message);
    request.onsuccess = () => resolve();
    request.onerror = () => reject(request.error);
  });
}
```

```typescript
export async function getChatHistory(): Promise<ChatMessage[]> {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const transaction = db.transaction(CHAT_STORE, 'readonly');
    const store = transaction.objectStore(CHAT_STORE);
    const request = store.getAll();
    
    request.onsuccess = () => {
      const history = request.result as ChatMessage[];
      // Sort by timestamp to ensure correct order
      history.sort((a, b) => new Date(a.timestamp).getTime() - new Date(b.timestamp).getTime());
      resolve(history);
    };
    request.onerror = () => reject(request.error);
  });
}
```

**Beginner Explanation**:
- Gets all chat messages
- `.sort()` arranges them by time (oldest first)
- `.getTime()` converts Date to number (milliseconds since 1970)
- Subtracting dates tells us which came first

```typescript
export async function clearChatHistory(): Promise<void> {
  const db = await initDB();
  return new Promise((resolve, reject) => {
    const transaction = db.transaction(CHAT_STORE, 'readwrite');
    const store = transaction.objectStore(CHAT_STORE);
    const request = store.clear();
    // Delete everything in the chat store
    
    request.onsuccess = () => resolve();
    request.onerror = () => reject(request.error);
  });
}
```

**Beginner Explanation**:
- `store.clear()` removes all items from a store
- Like the "Clear All" button functionality

---

#### geminiService.ts

**Purpose**: Communicates with Google's Gemini AI to answer questions.

```typescript
import { GoogleGenAI, Type } from "@google/genai";
// Import Google's AI SDK
```

```typescript
import { RailDocument, GeminiResponse } from "../types";
// Import our types
```

```typescript
export async function queryStandards(
  question: string,
  documents: RailDocument[]
): Promise<GeminiResponse> {
```

**Beginner Explanation**:
- This function takes a question and array of documents
- Returns a promise that will give us a GeminiResponse

```typescript
  const apiKey = process.env.API_KEY;
  // Get API key from environment variable
```

**Beginner Explanation**:
- `process.env.API_KEY` is replaced by Vite with the actual key
- We store API keys in environment variables for security (not in code)

```typescript
  if (!apiKey || apiKey === "undefined" || apiKey === "") {
    throw new Error("Missing API Key. Please verify that you have added the API_KEY secret...");
  }
  // Check that we have a valid API key
```

**Beginner Explanation**:
- Safety check to provide helpful error if key is missing
- Better than cryptic error from the AI service

```typescript
  const ai = new GoogleGenAI({ apiKey });
  // Create AI client with our API key
```

```typescript
  const context = documents
    .map(doc => `--- FILENAME: ${doc.name} ---\n${doc.content}`)
    .join('\n\n');
```

**Beginner Explanation**:
- `.map()` transforms each document into a formatted string
- `.join('\n\n')` combines all documents with double line breaks
- Creates one big text with all document contents for the AI to read

```typescript
  const systemInstruction = `
    You are an expert in Network Rail standards and engineering specifications. 
    Your task is to answer user questions accurately based ONLY on the provided document text.
    
    The text provided includes page markers like "[Page X]". 
    
    CRITICAL RULES:
    1. For every answer, you MUST provide citations.
    2. The "standard" field in the citation MUST be the EXACT "FILENAME" provided in the context.
    3. The "clause" field is the specific clause number found (e.g. 3.2.1).
    4. The "page" field MUST be the integer page number found in the text (e.g. if info follows [Page 5], page is 5).
    5. If the answer isn't in the provided text, state that the information is not found.
    6. Format your response as a valid JSON object.
  `;
```

**Beginner Explanation**:
- This tells the AI how to behave and what format to use
- Clear instructions make the AI more accurate
- We want specific citation format so our app can display clickable references

```typescript
  try {
    const response = await ai.models.generateContent({
      model: 'gemini-3-flash-preview',  // Which AI model to use
      contents: `Context:\n${context}\n\nQuestion: ${question}`,
      // Send the context and question
      
      config: {
        systemInstruction,
        thinkingConfig: { thinkingBudget: 0 },  // Don't show reasoning
        responseMimeType: "application/json",   // Request JSON response
```

**Beginner Explanation**:
- `generateContent()` asks the AI to generate a response
- We give it the document context and user's question
- `responseMimeType: "application/json"` ensures we get structured data

```typescript
        responseSchema: {
          type: Type.OBJECT,
          properties: {
            answer: { type: Type.STRING },
            citations: {
              type: Type.ARRAY,
              items: {
                type: Type.OBJECT,
                properties: {
                  standard: { type: Type.STRING },
                  clause: { type: Type.STRING },
                  page: { type: Type.INTEGER }
                },
                required: ["standard", "clause", "page"]
              }
            }
          },
          required: ["answer", "citations"]
        }
      }
    });
```

**Beginner Explanation**:
- `responseSchema` defines the exact structure we want
- Like saying "give me an object with answer (string) and citations (array of objects)"
- This ensures the AI's response matches our GeminiResponse type

```typescript
    const resultText = response.text;
    if (!resultText) {
      throw new Error("Empty response received from the AI.");
    }
    
    return JSON.parse(resultText.trim()) as GeminiResponse;
    // Convert JSON string to JavaScript object
```

**Beginner Explanation**:
- `response.text` gets the text from AI's response
- `JSON.parse()` converts JSON string into a JavaScript object
- `.trim()` removes any extra whitespace
- `as GeminiResponse` tells TypeScript what type this is

```typescript
  } catch (error) {
    console.error("Gemini API Error:", error);
    throw new Error(error instanceof Error ? error.message : "Unable to reach the AI assistant...");
  }
}
```

**Beginner Explanation**:
- Catch any errors from the AI service
- `instanceof Error` checks if it's an Error object
- Provides user-friendly error message


---

### Components

Components are reusable UI pieces. Think of them like LEGO blocks that combine to build the interface.

#### EmptyState.tsx

**Purpose**: Shows a friendly message when no documents have been uploaded yet.

```typescript
import React from 'react';

export const EmptyState: React.FC = () => {
  return (
    <div className="flex flex-col items-center justify-center h-full text-center px-4">
      {/* Center everything vertically and horizontally */}
```

**Beginner Explanation**:
- `React.FC` means "React Functional Component"
- `className` in React is like `class` in HTML (used for styling)
- Tailwind CSS classes: `flex` = flexbox layout, `items-center` = center items, `h-full` = full height

```typescript
      <div className="w-20 h-20 bg-blue-100 rounded-full flex items-center justify-center mb-6">
        {/* Circle icon container: 20x20, light blue, rounded, centered */}
        
        <svg xmlns="http://www.w3.org/2000/svg" className="h-10 w-10 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
        </svg>
        {/* SVG icon of a document */}
      </div>
```

**Beginner Explanation**:
- SVG is a way to draw scalable graphics
- This draws a document icon
- `stroke="currentColor"` makes it use the text color

```typescript
      <h3 className="text-xl font-semibold text-slate-800 mb-2">No Standards Uploaded</h3>
      {/* Heading text */}
      
      <p className="text-slate-500 max-w-sm">
        Upload your Network Rail standards in PDF format to start asking questions and getting referenced answers.
      </p>
      {/* Description text */}
    </div>
  );
};
```

**Beginner Explanation**:
- Simple component that just displays some styled text and an icon
- Shows when `chatHistory.length === 0` in the main app

---

#### DocumentList.tsx

**Purpose**: Displays the list of uploaded documents in the sidebar.

```typescript
import React from 'react';
import { RailDocument } from '../types';

interface DocumentListProps {
  documents: RailDocument[];    // Array of documents to display
  onDelete: (id: string) => void;  // Function to call when delete button clicked
}
```

**Beginner Explanation**:
- `Props` are inputs to a component (like function parameters)
- This component needs an array of documents and a delete function
- `(id: string) => void` means "a function that takes a string and returns nothing"

```typescript
export const DocumentList: React.FC<DocumentListProps> = ({ documents, onDelete }) => {
  // Destructuring props directly in the parameter
```

**Beginner Explanation**:
- `{ documents, onDelete }` extracts these properties from the props object
- Same as: `const documents = props.documents; const onDelete = props.onDelete;`

```typescript
  return (
    <div className="space-y-3">
      {/* Container with spacing between children */}
      
      <h3 className="text-xs font-bold text-slate-400 uppercase tracking-wider px-2">
        Uploaded Standards ({documents.length})
      </h3>
      {/* Header showing count */}
```

**Beginner Explanation**:
- `{documents.length}` is JavaScript inside JSX - shows number of documents
- Curly braces `{}` let you write JavaScript inside JSX

```typescript
      <div className="space-y-1">
        {documents.map((doc) => (
          // Loop through each document and create a UI element
```

**Beginner Explanation**:
- `.map()` transforms each item in an array
- For each document, we create a div (the UI for that document)
- This is how you create lists in React

```typescript
          <div 
            key={doc.id} 
            className="group flex items-center justify-between p-3 bg-white border border-slate-200 rounded-lg hover:border-blue-300 transition-colors"
          >
            {/* Document card */}
```

**Beginner Explanation**:
- `key={doc.id}` helps React track which items changed (required for lists)
- `group` is a Tailwind class for hover effects on children
- `hover:border-blue-300` changes border color on mouse hover

```typescript
            <div className="flex items-center space-x-3 overflow-hidden">
              {/* Left side: icon and info */}
              
              <div className="flex-shrink-0 w-8 h-8 bg-slate-100 rounded flex items-center justify-center">
                <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 text-slate-500" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M4 4a2 2 0 012-2h4.586A1 1 0 0111 2.293l4.707 4.707a1 1 0 01.293.707V16a2 2 0 01-2 2H6a2 2 0 01-2-2V4zm2 6a1 1 0 011-1h6a1 1 0 110 2H7a1 1 0 01-1-1zm1 3a1 1 0 100 2h6a1 1 0 100-2H7z" clipRule="evenodd" />
                </svg>
                {/* Document icon */}
              </div>
```

**Beginner Explanation**:
- `flex-shrink-0` prevents the icon from shrinking
- Creates a nice gray box with a document icon inside

```typescript
              <div className="truncate">
                {/* Text container that truncates if too long */}
                
                <p className="text-sm font-medium text-slate-700 truncate">{doc.name}</p>
                {/* Document name */}
                
                <p className="text-[10px] text-slate-400">{(doc.size / 1024 / 1024).toFixed(2)} MB</p>
                {/* File size in megabytes */}
              </div>
            </div>
```

**Beginner Explanation**:
- `{doc.name}` displays the document's filename
- `doc.size / 1024 / 1024` converts bytes to megabytes
- `.toFixed(2)` rounds to 2 decimal places

```typescript
            <button 
              onClick={() => onDelete(doc.id)}
              className="p-1 text-slate-400 hover:text-red-500 rounded opacity-0 group-hover:opacity-100 transition-opacity"
            >
              {/* Delete button that appears on hover */}
              
              <svg xmlns="http://www.w3.org/2000/svg" className="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
              </svg>
              {/* Trash icon */}
            </button>
          </div>
        ))}
      </div>
    </div>
  );
};
```

**Beginner Explanation**:
- `onClick={() => onDelete(doc.id)}` creates a function that calls onDelete when clicked
- Arrow function is needed to pass the id parameter
- `opacity-0 group-hover:opacity-100` hides button until you hover over the card
- The trash icon only shows when you hover over a document

---

#### ClauseModal.tsx (Complex Component)

**Purpose**: Displays a PDF document in a draggable, resizable modal window.

This is the most complex component, so we'll break it down carefully.

**State Variables** (data that changes over time):

```typescript
const [currentPage, setCurrentPage] = useState(Math.max(1, initialPage));
// Which page we're viewing (starts at initialPage, minimum 1)

const [totalPages, setTotalPages] = useState(0);
// How many pages the PDF has

const [loading, setLoading] = useState(true);
// Are we still loading the PDF?

const [error, setError] = useState<string | null>(null);
// Error message, or null if no error

const [scale, setScale] = useState(1.0);
// Zoom level (1.0 = 100%, 1.5 = 150%)

const [pos, setPos] = useState({ x: 0, y: 0 });
// Position of modal on screen

const [size, setSize] = useState({ width: 900, height: 800 });
// Size of modal window

const [isDragging, setIsDragging] = useState(false);
// Are we currently dragging the modal?

const [isResizing, setIsResizing] = useState(false);
// Are we currently resizing the modal?
```

**Beginner Explanation**:
- `useState` creates a piece of state
- Returns [currentValue, functionToUpdateIt]
- When state changes, React re-renders the component with new values

**Refs** (references to values that don't cause re-renders):

```typescript
const canvasRef = useRef<HTMLCanvasElement>(null);
// Reference to the <canvas> element where we draw the PDF

const pdfDocRef = useRef<any>(null);
// Reference to the loaded PDF document

const renderTaskRef = useRef<any>(null);
// Reference to the current rendering task (so we can cancel it)

const modalRef = useRef<HTMLDivElement>(null);
// Reference to the modal div element

const dragStart = useRef({ x: 0, y: 0 });
// Starting position when drag begins

const sizeStart = useRef({ w: 0, h: 0 });
// Starting size when resize begins
```

**Beginner Explanation**:
- `useRef` creates a container that persists across renders
- Unlike state, changing a ref doesn't cause a re-render
- `.current` holds the actual value

**Effect for initializing modal size and position**:

```typescript
useEffect(() => {
  if (isOpen) {
    const w = Math.min(window.innerWidth * 0.9, 1000);
    const h = Math.min(window.innerHeight * 0.9, 850);
    // Size is 90% of window, but max 1000x850
    
    setSize({ width: w, height: h });
    setPos({
      x: (window.innerWidth - w) / 2,
      y: (window.innerHeight - h) / 2
    });
    // Center the modal on screen
  }
}, [isOpen]);
```

**Beginner Explanation**:
- `useEffect` runs code when something changes
- `[isOpen]` means "run this when isOpen changes"
- This centers the modal when it first opens

**PDF Loading Effect**:

```typescript
useEffect(() => {
  if (!isOpen) return;
  // Don't do anything if modal is closed
  
  const loadPdf = async () => {
    setLoading(true);
    setError(null);
    
    try {
      const loadingTask = pdfjsLib.getDocument({ 
        data: pdfData.slice(0),           // Copy of PDF data
        disableAutoFetch: true,           // Don't load all pages at once
        disableStream: true,              // Load complete file
      });
      
      const pdf = await loadingTask.promise;
      // Wait for PDF to load
      
      pdfDocRef.current = pdf;
      // Store PDF in ref
      
      setTotalPages(pdf.numPages);
      // Save how many pages it has
      
      const startPage = Math.max(1, Math.min(initialPage, pdf.numPages));
      // Clamp page number to valid range
      
      setCurrentPage(startPage);
      renderPage(startPage, scale);
      // Render the first page
      
    } catch (err: any) {
      console.error("PDF load error:", err);
      setError(`Could not load PDF document: ${err.message}`);
      setLoading(false);
    }
  };
  
  loadPdf();
```

**Beginner Explanation**:
- This effect runs when modal opens
- Loads the PDF file using PDF.js
- Handles errors gracefully
- Renders the initial page

```typescript
  return () => {
    // Cleanup function - runs when component unmounts or before effect re-runs
    
    if (renderTaskRef.current) {
      renderTaskRef.current.cancel();
      // Cancel any ongoing rendering
    }
    if (pdfDocRef.current) {
      pdfDocRef.current.destroy();
      // Free memory used by PDF
      pdfDocRef.current = null;
    }
  };
}, [isOpen, pdfData, initialPage, scale, renderPage]);
```

**Beginner Explanation**:
- The return function is cleanup
- Runs when modal closes or dependencies change
- Important to prevent memory leaks

**Rendering Pages**:

```typescript
const renderPage = useCallback(async (num: number, currentScale: number) => {
  if (!pdfDocRef.current || !canvasRef.current) return;
  // Safety check
  
  const pageNum = Math.max(1, Math.min(num, pdfDocRef.current.numPages));
  // Ensure page number is valid
  
  try {
    if (renderTaskRef.current) {
      renderTaskRef.current.cancel();
      // Cancel previous render if still going
    }
    
    const page = await pdfDocRef.current.getPage(pageNum);
    // Get the page object
    
    const canvas = canvasRef.current;
    const context = canvas.getContext('2d');
    // Get 2D drawing context
    
    const viewport = page.getViewport({ scale: currentScale });
    // Calculate how big the page should be at this zoom level
    
    const pixelRatio = window.devicePixelRatio || 1;
    // Handle high-DPI displays (Retina screens)
    
    canvas.height = viewport.height * pixelRatio;
    canvas.width = viewport.width * pixelRatio;
    canvas.style.height = `${viewport.height}px`;
    canvas.style.width = `${viewport.width}px`;
    // Set canvas size (actual pixels vs CSS pixels)
    
    context.setTransform(pixelRatio, 0, 0, pixelRatio, 0, 0);
    // Scale for high-DPI displays
    
    const renderTask = page.render({
      canvasContext: context,
      viewport: viewport
    });
    renderTaskRef.current = renderTask;
    // Start rendering
    
    await renderTask.promise;
    // Wait for rendering to complete
    
    setLoading(false);
    
  } catch (err: any) {
    if (err.name === 'RenderingCancelledException') return;
    // It's okay if we cancelled it
    
    console.error("Page render error:", err);
    setError(`Failed to render page ${pageNum}.`);
    setLoading(false);
  }
}, []);
```

**Beginner Explanation**:
- `useCallback` creates a memoized function (doesn't recreate on every render)
- Draws PDF page onto a canvas element
- Handles cancellation of previous render
- Supports high-resolution displays

**Drag and Resize Handlers**:

```typescript
const handleMouseDown = (e: React.MouseEvent) => {
  if ((e.target as HTMLElement).closest('.drag-handle')) {
    setIsDragging(true);
    dragStart.current = { x: e.clientX - pos.x, y: e.clientY - pos.y };
    // Store offset from mouse to modal position
  }
};
```

**Beginner Explanation**:
- When you click on the drag handle (modal header)
- Starts dragging
- Remembers where you clicked relative to modal position

```typescript
useEffect(() => {
  const handleMouseMove = (e: MouseEvent) => {
    if (isDragging) {
      setPos({
        x: e.clientX - dragStart.current.x,
        y: e.clientY - dragStart.current.y
      });
      // Update modal position as mouse moves
    }
    if (isResizing) {
      const deltaX = e.clientX - dragStart.current.x;
      const deltaY = e.clientY - dragStart.current.y;
      setSize({
        width: Math.max(400, sizeStart.current.w + deltaX),
        height: Math.max(300, sizeStart.current.h + deltaY)
      });
      // Update modal size as mouse moves
    }
  };
  
  const handleMouseUp = () => {
    setIsDragging(false);
    setIsResizing(false);
    // Stop dragging/resizing when mouse released
  };
  
  if (isDragging || isResizing) {
    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('mouseup', handleMouseUp);
  }
  
  return () => {
    window.removeEventListener('mousemove', handleMouseMove);
    window.removeEventListener('mouseup', handleMouseUp);
  };
  // Cleanup: remove event listeners
}, [isDragging, isResizing]);
```

**Beginner Explanation**:
- Adds global mouse event listeners while dragging/resizing
- Updates position or size as mouse moves
- Removes listeners when done (cleanup)

The component JSX structure renders:
1. A backdrop (dark overlay)
2. The modal window (with inline styles for position/size)
3. Header with title, zoom controls, close button
4. Navigation bar with page controls
5. Canvas for displaying PDF
6. Resize handle in bottom-right corner


---

### Main Application

#### App.tsx

**Purpose**: The main component that ties everything together. This is the heart of the application.

**State Management** (what data the app keeps track of):

```typescript
const [documents, setDocuments] = useState<RailDocument[]>([]);
// All uploaded PDFs

const [chatHistory, setChatHistory] = useState<ChatMessage[]>([]);
// All conversation messages

const [inputText, setInputText] = useState('');
// Text in the input box

const [isProcessingFile, setIsProcessingFile] = useState(false);
// Are we currently processing an uploaded file?

const [isAsking, setIsAsking] = useState(false);
// Are we waiting for AI response?

const [error, setError] = useState<string | null>(null);
// Current error message, if any

const [isDragging, setIsDragging] = useState(false);
// Is user dragging files over the window?

const [activeClause, setActiveClause] = useState<ActiveClause | null>(null);
// Currently open PDF modal (null = closed)
```

**Key Functions Explained**:

**1. Loading Data on Startup**:

```typescript
useEffect(() => {
  const loadAppData = async () => {
    try {
      const [storedDocs, storedChat] = await Promise.all([
        getAllDocuments(),
        getChatHistory()
      ]);
      // Load both documents and chat history in parallel
      
      setDocuments(storedDocs);
      setChatHistory(storedChat);
    } catch (err) {
      console.error("Failed to load local data:", err);
    } finally {
      setIsLoadingLibrary(false);
    }
  };
  loadAppData();
}, []);
```

**Beginner Explanation**:
- `useEffect` with empty array `[]` runs once when app starts
- `Promise.all` loads documents and chat at the same time (faster than one-by-one)
- `finally` runs whether success or error - stops the loading spinner

**2. Processing Uploaded Files**:

```typescript
const processFile = async (file: File): Promise<RailDocument | null> => {
  // Check if it's a PDF
  if (file.type !== 'application/pdf' && !file.name.toLowerCase().endsWith('.pdf')) {
    return null;
  }
  
  // Convert file to bytes
  const arrayBuffer = await file.arrayBuffer();
  
  // Check file size (max 50MB)
  if (file.size > 50 * 1024 * 1024) {
    throw new Error(`File "${file.name}" is too large (max 50MB).`);
  }
  
  // Extract text from PDF
  const text = await extractTextFromPdf(file);
  
  // Clean up filename (remove duplicate numbers like "file (1).pdf")
  const cleanedName = cleanFileName(file.name);
  
  // Create document object
  const doc: RailDocument = {
    id: crypto.randomUUID(),           // Generate unique ID
    name: cleanedName,
    content: text,                     // Extracted text
    size: file.size,
    uploadDate: new Date(),
    pdfData: new Uint8Array(arrayBuffer)  // Raw PDF bytes
  };
  
  // Save to IndexedDB
  await saveDocument(doc);
  return doc;
};
```

**Beginner Explanation**:
- Takes a File object, returns a RailDocument
- Validates file type and size
- Extracts text using pdfService
- Generates unique ID with `crypto.randomUUID()`
- Saves to browser storage
- Returns null if not a PDF

**3. Handling File Uploads**:

```typescript
const handleFileUpload = async (e: React.ChangeEvent<HTMLInputElement>) => {
  const files = e.target.files;
  if (!files || files.length === 0) return;
  
  setIsProcessingFile(true);
  setError(null);
  
  try {
    const newDocs: RailDocument[] = [];
    for (let i = 0; i < files.length; i++) {
      const doc = await processFile(files[i]);
      if (doc) newDocs.push(doc);
    }
    setDocuments(prev => [...prev, ...newDocs]);
    // Add new documents to existing array
  } catch (err: any) {
    setError(err.message || 'Error processing PDF');
  } finally {
    setIsProcessingFile(false);
    if (fileInputRef.current) fileInputRef.current.value = '';
    // Reset file input
  }
};
```

**Beginner Explanation**:
- Triggered when user selects files
- Processes each file one by one
- Updates state with new documents
- Shows error if something goes wrong
- Resets input so you can upload same file again if needed

**4. Drag and Drop File Support**:

The app supports dragging PDF files (or folders) directly onto the window:

```typescript
const handleDrop = async (e: React.DragEvent) => {
  e.preventDefault();
  e.stopPropagation();
  setIsDragging(false);
  
  const items = e.dataTransfer.items;
  if (!items) return;
  
  setIsProcessingFile(true);
  
  try {
    const filesToProcess: File[] = [];
    
    // Scan for all PDFs (including in folders)
    for (let i = 0; i < items.length; i++) {
      const item = items[i].webkitGetAsEntry();
      if (item) {
        const scanned = await scanFiles(item);
        filesToProcess.push(...scanned);
      }
    }
    
    // Process all found PDFs
    const newDocs: RailDocument[] = [];
    for (const file of filesToProcess) {
      const doc = await processFile(file);
      if (doc) newDocs.push(doc);
    }
    
    if (newDocs.length > 0) {
      setDocuments(prev => [...prev, ...newDocs]);
    } else {
      setError("No PDF files found.");
    }
  } catch (err: any) {
    setError(err.message || "Failed to process items.");
  } finally {
    setIsProcessingFile(false);
  }
};

const scanFiles = async (item: FileSystemEntry): Promise<File[]> => {
  const files: File[] = [];
  
  if (item.isFile) {
    // It's a file
    const file = await new Promise<File>((resolve) => 
      (item as FileSystemFileEntry).file(resolve)
    );
    if (file.name.toLowerCase().endsWith('.pdf')) {
      files.push(file);
    }
  } else if (item.isDirectory) {
    // It's a folder - recursively scan it
    const directoryReader = (item as FileSystemDirectoryEntry).createReader();
    const entries = await new Promise<FileSystemEntry[]>((resolve) => {
      directoryReader.readEntries(resolve);
    });
    for (const entry of entries) {
      const entryFiles = await scanFiles(entry);
      files.push(...entryFiles);
    }
  }
  
  return files;
};
```

**Beginner Explanation**:
- `handleDrop` handles when user drops files
- `scanFiles` recursively finds all PDFs (even in folders)
- `webkitGetAsEntry()` gives us folder structure
- If it's a directory, we look inside it for more files
- Collects all PDFs and processes them

**5. Asking Questions to the AI**:

```typescript
const handleAsk = async (e: React.FormEvent) => {
  e.preventDefault();  // Prevent form from reloading page
  
  if (!inputText.trim() || isAsking || documents.length === 0) return;
  // Don't proceed if empty, already asking, or no documents
  
  // Create user message
  const userMsg: ChatMessage = {
    id: crypto.randomUUID(),
    role: 'user',
    text: inputText,
    timestamp: new Date()
  };
  
  // Add to chat history
  setChatHistory(prev => [...prev, userMsg]);
  await saveChatMessage(userMsg);
  
  const currentQuery = inputText;
  setInputText('');      // Clear input box
  setIsAsking(true);     // Show loading animation
  setError(null);
  
  try {
    // Ask the AI
    const result = await queryStandards(currentQuery, documents);
    
    // Create AI response message
    const assistantMsg: ChatMessage = {
      id: crypto.randomUUID(),
      role: 'assistant',
      text: result.answer,
      citations: result.citations,
      timestamp: new Date()
    };
    
    // Add to chat history
    setChatHistory(prev => [...prev, assistantMsg]);
    await saveChatMessage(assistantMsg);
    
  } catch (err: any) {
    setError(err.message || 'Failed to get an answer.');
  } finally {
    setIsAsking(false);  // Hide loading animation
  }
};
```

**Beginner Explanation**:
- Runs when user submits the question form
- Creates a message for the user's question
- Sends question to AI via `queryStandards`
- Creates a message for the AI's answer
- Both messages saved to chat history and browser storage
- Shows loading animation while waiting

**6. Viewing Citations**:

```typescript
const handleCitationClick = (citation: Citation) => {
  // Find the document mentioned in the citation
  const target = citation.standard.toLowerCase().trim();
  const targetNoExt = target.replace(/\.pdf$/i, '');
  
  // Try exact match first
  let doc = documents.find(d => {
    const docName = d.name.toLowerCase();
    const docNameNoExt = docName.replace(/\.pdf$/i, '');
    return docName === target || docNameNoExt === targetNoExt;
  });
  
  // If not found, try fuzzy match
  if (!doc) {
    const candidates = documents.filter(d => {
      const docNameNoExt = d.name.toLowerCase().replace(/\.pdf$/i, '');
      return docNameNoExt.includes(targetNoExt) || targetNoExt.includes(docNameNoExt);
    });
    if (candidates.length > 0) {
      // Pick the one with most similar length
      candidates.sort((a, b) => 
        Math.abs(a.name.length - citation.standard.length) - 
        Math.abs(b.name.length - citation.standard.length)
      );
      doc = candidates[0];
    }
  }
  
  if (!doc) {
    setError(`Standard "${citation.standard}" not found in your local library.`);
    return;
  }
  
  // Open the PDF modal at the cited page
  setActiveClause({
    standardName: doc.name,
    clauseNumber: citation.clause,
    pdfData: doc.pdfData,
    page: Math.max(1, citation.page || 1)
  });
};
```

**Beginner Explanation**:
- When user clicks a citation, find the matching document
- First tries exact name match
- If not found, tries "fuzzy" matching (partial matches)
- Opens the PDF modal showing the correct page
- Shows error if document not found

**The UI Structure**:

The app is divided into three main sections:

1. **Sidebar** (left side):
   - App title and logo
   - Upload button
   - Search online button
   - List of uploaded documents
   - Storage indicator

2. **Main Area** (right side):
   - Header with title
   - Chat messages area
   - Input box for questions

3. **PDF Modal** (overlay):
   - Shows when you click a citation
   - Displays the PDF document
   - Can drag and resize

```typescript
return (
  <div className="flex h-screen">
    {/* Drag overlay - shows when dragging files */}
    {isDragging && (
      <div className="absolute inset-0 z-[100] bg-blue-600/10 backdrop-blur-sm...">
        {/* Nice visual feedback when dragging */}
      </div>
    )}
    
    {/* Sidebar */}
    <aside className="w-80 bg-slate-900">
      {/* Upload button, document list, etc. */}
    </aside>
    
    {/* Main chat area */}
    <main className="flex-1 flex flex-col">
      {/* Header */}
      <header>...</header>
      
      {/* Chat messages */}
      <div className="flex-1 overflow-y-auto">
        {chatHistory.length === 0 ? (
          <EmptyState />
        ) : (
          chatHistory.map((msg) => (
            /* Render each message */
          ))
        )}
      </div>
      
      {/* Input box */}
      <div className="p-6">
        <form onSubmit={handleAsk}>
          <input ... />
          <button type="submit">Ask AI</button>
        </form>
      </div>
    </main>
    
    {/* PDF Modal (only when activeClause is set) */}
    {activeClause && (
      <ClauseModal 
        isOpen={!!activeClause}
        onClose={() => setActiveClause(null)}
        standardName={activeClause.standardName}
        clauseNumber={activeClause.clauseNumber}
        pdfData={activeClause.pdfData}
        initialPage={activeClause.page}
      />
    )}
  </div>
);
```

**Beginner Explanation**:
- `flex` creates a side-by-side layout
- Sidebar is fixed width, main area fills remaining space
- Chat messages scroll if there are too many
- Modal only renders when `activeClause` is not null
- `!!activeClause` converts to boolean (true if exists, false if null)

---

## How Everything Works Together

Let's trace what happens when you use the application:

### Scenario 1: Uploading a Document

1. **User clicks "Upload Standards" button**
   - Triggers file input dialog
   - User selects PDF files

2. **handleFileUpload runs**
   - Gets the File objects from the input
   - Calls `processFile` for each

3. **processFile runs for each file**
   - Checks if it's a valid PDF
   - Checks file size (max 50MB)
   - Calls `extractTextFromPdf` (pdfService)

4. **extractTextFromPdf extracts text**
   - Uses PDF.js to load the PDF
   - Loops through pages
   - Extracts text from each page
   - Returns formatted text with page markers

5. **processFile continues**
   - Creates RailDocument object
   - Calls `saveDocument` (storageService)

6. **saveDocument saves to IndexedDB**
   - Opens database
   - Stores document in 'documents' object store

7. **Back to handleFileUpload**
   - Adds new documents to state
   - React re-renders the DocumentList
   - User sees the new document in sidebar

### Scenario 2: Asking a Question

1. **User types question and clicks "Ask AI"**
   - `handleAsk` function runs

2. **Create user message**
   - Build ChatMessage object with question
   - Add to chatHistory state
   - Save to IndexedDB via `saveChatMessage`
   - React re-renders, showing user's message

3. **Query the AI**
   - Call `queryStandards` (geminiService)
   - Pass the question and all documents

4. **geminiService processes**
   - Combines all document text into context
   - Sends to Google Gemini AI with instructions
   - Waits for response

5. **AI responds**
   - Returns answer and citations as JSON
   - Parse JSON into GeminiResponse object

6. **Create assistant message**
   - Build ChatMessage with AI's answer and citations
   - Add to chatHistory state
   - Save to IndexedDB
   - React re-renders, showing AI's response

7. **User sees the answer**
   - Answer text displayed
   - Citations shown as clickable buttons

### Scenario 3: Clicking a Citation

1. **User clicks citation button**
   - `handleCitationClick` runs

2. **Find the document**
   - Search through uploaded documents
   - Match by filename

3. **Open PDF modal**
   - Set `activeClause` state with:
     - Document name
     - Clause number
     - PDF data
     - Page number
   - React re-renders

4. **ClauseModal appears**
   - Loads PDF with PDF.js
   - Renders the specified page on canvas
   - User can view, zoom, navigate pages

5. **User closes modal**
   - Sets `activeClause` to null
   - Modal disappears

---

## Key Programming Concepts Explained

### 1. Components

**What**: Reusable pieces of UI code

**Why**: Breaking the interface into components makes code:
- Easier to understand (each piece has one job)
- Reusable (use DocumentList anywhere)
- Easier to maintain (fix bug in one place)

**Example**: EmptyState component shows "No Standards Uploaded" - we don't mix that code with chat code.

### 2. State

**What**: Data that can change and triggers re-renders

**Why**: React needs to know when to update the screen. When state changes, React automatically re-renders the component with new data.

**Example**: When you add a document, `documents` state changes, and React re-displays the document list.

### 3. Props

**What**: Data passed from parent to child component

**Why**: Allows components to be customized and reused

**Example**: DocumentList receives `documents` array and `onDelete` function as props.

### 4. Hooks (useState, useEffect, useRef, useCallback)

**What**: Special functions that let you "hook into" React features

**Why**: They allow functional components to have state, run side effects, and store references.

**Examples**:
- `useState`: Store and update data
- `useEffect`: Run code when something changes or component loads
- `useRef`: Store a value that persists but doesn't cause re-renders
- `useCallback`: Create a function that doesn't change on every render

### 5. Async/Await

**What**: Modern way to handle asynchronous code (code that takes time)

**Why**: Makes asynchronous code look like synchronous code, easier to read.

**Example**: 
```typescript
const text = await extractTextFromPdf(file);
// Wait for text extraction to finish, then continue
```

Instead of callbacks:
```typescript
extractTextFromPdf(file, (text) => {
  // This runs later when done
});
```

### 6. Promises

**What**: Objects representing eventual completion (or failure) of an async operation

**Why**: They allow you to handle asynchronous operations in a clean way

**Example**: 
```typescript
await saveDocument(doc);
// Returns a Promise<void> that we wait for
```

### 7. Array Methods (map, filter, sort, etc.)

**What**: Functions that transform or filter arrays

**Why**: Common operations made easy and readable

**Examples**:
- `.map()`: Transform each item → `documents.map(doc => doc.name)` gets all names
- `.filter()`: Keep only items that match → `documents.filter(d => d.size > 1000)` gets large files
- `.sort()`: Arrange items → `history.sort((a, b) => a.timestamp - b.timestamp)` sorts by time

### 8. Destructuring

**What**: Extracting values from objects/arrays into variables

**Example**:
```typescript
const { name, size } = document;
// Same as: const name = document.name; const size = document.size;
```

### 9. TypeScript Types

**What**: Annotations that describe what data looks like

**Why**: Catch errors before running code. TypeScript checks that you use data correctly.

**Example**:
```typescript
function processFile(file: File): Promise<RailDocument | null>
// This function takes a File and returns a Promise of (RailDocument or null)
```

If you try to pass a string instead of File, TypeScript shows an error!

### 10. JSX

**What**: HTML-like syntax in JavaScript/TypeScript

**Why**: Makes writing UI code intuitive

**Example**:
```typescript
<div className="container">
  <h1>{title}</h1>
</div>
```

This isn't actually HTML - it's transformed into JavaScript function calls that create elements.

---

## Summary

**RailStandardAI** is a sophisticated yet beginner-friendly application that:

1. **Manages PDF Documents**: Upload, store locally, and organize railway standards
2. **Extracts Text**: Uses PDF.js to read PDF content
3. **AI-Powered Q&A**: Uses Google Gemini to answer questions based on your documents
4. **Provides Citations**: Shows exactly where information comes from
5. **Displays PDFs**: Interactive PDF viewer with zoom and navigation

**The application demonstrates**:
- React component architecture
- State management with hooks
- Browser storage with IndexedDB
- PDF processing with PDF.js
- AI integration with Google Gemini
- Modern CSS with Tailwind
- TypeScript for type safety
- Async programming patterns

**Key takeaways for beginners**:
- Break complex UIs into small, manageable components
- Use state to store data that changes
- Handle async operations with async/await
- Store data locally for persistence
- Integrate external services (AI) via APIs
- TypeScript helps catch bugs early
- Modern web apps can do a lot in the browser without servers!

This application runs entirely in your browser - no backend server needed! All processing, storage, and AI calls happen client-side, making it fast, private, and easy to deploy.

---

## Next Steps for Learning

If you want to understand this project better:

1. **Start with the simple components**: EmptyState.tsx and DocumentList.tsx
2. **Understand types.ts**: See how data is structured
3. **Read the services**: See how they interact with external systems
4. **Explore App.tsx**: See how everything connects
5. **Experiment**: Try modifying styles, adding features, or changing behavior

Remember: Even experienced developers don't understand everything at once. Take it one piece at a time, and refer back to this guide as needed!

