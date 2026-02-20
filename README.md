# board-game-tool
My Board Game Collection Tool
https://jt1919191919.github.io/board-game-tool/


Better printout of this:
https://docs.google.com/document/d/1JW5A7kgBpgTeNwW8wFhn-zEKD0njhBLRqAawnI0XYys/edit?tab=t.0#heading=h.lnq6yjeh720a

Board Game Collection Manager
A comprehensive web application for cataloging, filtering, and sharing your board game collection with cloud storage via GitHub.
Table of Contents

Overview
Features
How It Works
Setup & Installation
Using the Tool
Data Storage
Sharing Collections
Technical Architecture
Development Notes

Overview
This is a single-page web application (HTML/CSS/JavaScript) that helps you manage your board game collection. It runs entirely in the browser and can store data locally or sync to GitHub for backup and sharing. The tool fetches game data from BoardGameGeek (BGG) and provides powerful filtering, sorting, and visualization features.
Features
Core Functionality

Game Catalog Management: Add, edit, delete games in your collection
BoardGameGeek Integration: Auto-fetch game data from BGG URLs
Advanced Filtering: Filter by players, playtime, complexity, designers, publishers, categories, mechanisms, and more
Personal Notes & Ratings: Add private notes and thumbs up/down ratings
Visual Gallery: Image carousels with multiple photos per game
Responsive Design: Works on desktop and mobile devices

Data Features

Local Storage: Works offline with browser storage
GitHub Sync: Optional cloud backup and sync across devices
Import/Export: JSON-based data portability
Image Caching: Improved performance with IndexedDB caching

Sharing Features

Collection Sharing: Share filtered collections via GitHub Gists
View-Only Mode: Recipients see shared collections without your private data
Filter Preservation: Shared links maintain your filters and sorting

How It Works
Data Flow

Input: Add games manually or fetch from BoardGameGeek
Storage: Data stored locally and optionally synced to GitHub
Processing: Real-time filtering and sorting in JavaScript
Display: Dynamic card-based gallery with image carousels
Sharing: Generate public Gist links for filtered collections

Storage Architecture

Local: Browser localStorage for immediate access
Cache: IndexedDB for images and performance
Cloud: GitHub repository file for backup/sync
Sharing: GitHub Gists for public collection sharing

Setup & Installation
Basic Setup (Local Only)

Save the HTML file to your computer
Open in any modern web browser
Start adding games - data saves automatically to browser storage

GitHub Integration Setup

Create GitHub Personal Access Token:

Go to GitHub.com → Settings → Developer settings → Personal access tokens
Generate new token (classic)
Select "repo" scope
Copy the token


Connect to GitHub:

Click the user icon (desktop) or hamburger menu (mobile)
Click "Connect GitHub"
Paste your personal access token
Your data will now sync to GitHub


Repository Configuration:

The tool uses these GitHub settings (in the code):
Owner: jt1919191919 (your GitHub username)
Repo: board-game-tool (your repository name)
File: games-data.json (where collection is stored)



Repository Setup
Create a GitHub repository named board-game-tool (or update the code with your preferred name).

! How to Update Code
Go to github.com and sign in. Go here (or click on the file name ‘index.html’) https://github.com/jt1919191919/board-game-tool/blob/refs/heads/main/index.html
Hit the Pencil. Make changes. Hit Commit Changes.
Each update should show up in the Actions tab on github to refer back to.


Using the Tool
Adding Games

Method 1: BoardGameGeek Import
Click "Add Game"
Paste a BGG URL (e.g., https://boardgamegeek.com/boardgame/161936/pandemic-legacy-season-1)
Click "Fetch Game Data"
Review auto-filled data
Add personal notes
Click "Save Game"
FETCHING UPDATED. No longer fetching through CORS/APIs since bgg blocks them all it seems. Instead I’m doing cloudfare as a middleman. Basically I made a free cloudfare account with google logins, made a Worker, added custom code in there that scrapes the actual page of a bgg url i give it, looks for all info, spits it back as clean json code that then imports into the fields in the Tool. Works perfectly!
cloudfare.com
Google login
Worker named:
https://bgg-proxy.jamiertifft.workers.dev/
Summary:
Cloudflare Worker as your personal proxy (free, reliable, no CORS issues)
Scraping the BGG game page instead of using their XML API (which now blocks non-browser requests)
Parsing the embedded GEEK.geekitemPreload JSON that BGG injects into every game page, which has all the data you need
One thing to be aware of: the Worker is currently hardcoded with your worker URL in the HTML. If you ever need to update the Worker code in the future, you just go back to the Cloudflare dashboard — your HTML doesn't need to change as long as the Worker URL stays the same.


Method 2: Manual Entry

Click "Add Game"
Fill in game details manually
Add images (URLs, one per line)
Click "Save Game"

Managing Your Collection
Filtering Games

Text Search: Search names, notes, designers, publishers
Player Count: Filter by min/max players or best player count
Playtime: Filter by game duration
Complexity: Filter by BGG weight (1-5 scale)
Advanced: Designer nationality, publisher country, categories, mechanisms
Custom Filters: Create complex filtering rules

Rating Games

Thumbs Up (📌): Pin favorite games (always visible)
Thumbs Down (👁️‍🗨️): Hide games you don't want to see
Reset Button: Clear all ratings

Editing Games

Click "Edit" mode
Games become editable cards
Modify any field directly
Add/remove images
Save or discard all changes

Viewing Options

Columns: Cycle between 1-5 columns (desktop) or 1-2 (mobile)
Sorting: Sort by name, year, players, playtime, complexity
Detailed View: Click any game for full details and larger images

Data Storage
Local Storage Format
json{
  "games": {
    "gameId": {
      "bggId": "161936",
      "name": "Pandemic Legacy: Season 1",
      "year": 2015,
      "dateAcquired": "1991-11-19",
      "players": {"min": 2, "max": 4},
      "playtime": {"min": 60, "max": 60},
      "weight": 2.83,
      "bestPlayerCount": [3, 4],
      "designers": ["Rob Daviau", "Matt Leacock"],
      "artists": ["Chris Quilliams"],
      "publishers": ["Z-Man Games"],
      "images": ["url1", "url2"],
      "notes": "Personal notes here",
      "description": "Game description from BGG"
    }
  },
  "preferences": {
    "gameId": "up" // or "down"
  }
}
GitHub Storage

File: games-data.json in your repository
Format: Same as local storage with metadata
Sync: Automatic when GitHub is connected
Backup: Every change creates a commit

Cache Storage (IndexedDB)

Games: Cached for quick loading
Images: Downloaded and cached for offline viewing
Metadata: Last sync times and version info

Sharing Collections
Creating a Share Link

Filter your collection as desired
Click hamburger menu → "Share Collection"
Generates a GitHub Gist with:

All games (minus private notes and acquisition dates)
Current filters and sorting
Your thumbs up/down preferences


Share URL is copied to clipboard

Share Link Format
https://yourdomain.com/collection.html?gist=abc123def456
What Recipients See

Games: Your collection with applied filters
Ratings: Your thumbs up/down preferences as starting point
Privacy: No personal notes or acquisition dates
Interaction: Can apply their own filters and ratings
Limitation: Cannot edit games or save changes

Recipient Experience

Click shared link
See filtered collection immediately
Can modify filters and add personal ratings
Changes don't affect original collection
Can reset to original shared state

Technical Architecture
Frontend Structure

Single HTML File: Complete application in one file
Vanilla JavaScript: No external frameworks
CSS Grid/Flexbox: Responsive layout
Service Integration: BGG API, GitHub API

Key Classes

BoardGameCollection: Main application controller
CacheManager: IndexedDB operations
Game Rendering: Dynamic HTML generation
Filter Engine: Real-time search and filtering

External Dependencies

Swiper.js: Image carousels
Font Awesome: Icons
GitHub API: Storage and sharing
BGG XML API: Game data import

Performance Features

Lazy Loading: Images load as needed (mobile)
Debounced Search: Prevents excessive filtering
Chunked Rendering: Large collections render progressively
Image Caching: IndexedDB for offline images

Browser Compatibility

Modern Browsers: Chrome, Firefox, Safari, Edge
Mobile: iOS Safari, Chrome Mobile
Requirements: ES6+ support, IndexedDB, Fetch API

Development Notes
Code Structure
BoardGameCollection Class:
├── init() - Application startup
├── Data Management
│   ├── loadFromStorage()
│   ├── saveToStorage() 
│   └── GitHub sync methods
├── UI Management
│   ├── render()
│   ├── bindEvents()
│   └── responsive handlers
├── Filtering
│   ├── applyFilters()
│   ├── updateFilters()
│   └── custom filter logic
└── Sharing
    ├── shareCollection()
    └── checkForSharedView()
Configuration Variables
Update these in the code for your setup:
javascriptthis.GITHUB_OWNER = 'jt1919191919';
this.GITHUB_REPO = 'board-game-tool';
this.GITHUB_BRANCH = 'main';
this.GITHUB_FILE_PATH = 'games-data.json';
Sample Data
The tool includes sample games (Pandemic Legacy, Terraforming Mars) for first-time users.
Error Handling

BGG API: Timeout and retry logic
GitHub API: Token validation and error messages
Storage: Graceful fallbacks between storage methods
Images: Hide broken images automatically

Security Notes

GitHub Token: Stored in localStorage (consider security implications)
CORS: Uses proxy for BGG API calls
Data Privacy: Shared collections exclude personal data

Future Enhancement Areas

Authentication: More secure GitHub integration
Collaborative: Multiple users editing same collection
Analytics: Usage statistics and recommendations
Mobile App: Native mobile versions
BGG Integration: Two-way sync with BGG collection


Quick Reference
Essential Commands

Add Game: Hamburger menu → Add Game
GitHub Sync: Hamburger menu → Connect GitHub
Share: Hamburger menu → Share Collection
Filter: Filters button → Configure filters
Edit Mode: Hamburger menu → Edit

File Locations

Tool: Single HTML file (can be renamed)
GitHub Repo: board-game-tool repository
Data File: games-data.json in repo root
Shared Collections: Public GitHub Gists

This tool gives you complete control over your board game collection with powerful filtering, beautiful presentation, and easy sharing capabilities. The GitHub integration ensures your data is backed up and accessible from any device.
