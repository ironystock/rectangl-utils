# WARP.md

This file provides comprehensive context about the HTMX codebase for Warp's AI terminal and other development tools.

## Project Overview

HTMX is a JavaScript library that extends HTML with modern web capabilities through declarative attributes. It enables dynamic web applications without complex JavaScript frameworks by bringing AJAX, CSS transitions, WebSockets, and Server-Sent Events directly to HTML.

### Key Features

- 🌐 **Hypermedia-driven development** - HTML as the engine of application state
- ⚡ **High performance** - 16KB minified and gzipped, zero dependencies
- 🔧 **Simple syntax** - Uses HTML attributes (`hx-*`) for dynamic behavior
- 🔄 **Any HTTP verb** - GET, POST, PUT, PATCH, DELETE support
- 🎯 **Progressive enhancement** - Works with existing HTML/CSS
- 🔌 **Extensible** - 27 official extensions for specialized functionality
- 📱 **Modern web features** - WebSockets, SSE, CSS transitions, history API

## Architecture

### Core Structure

**Main Library** (`src/htmx.js` - 5,310 lines)

- Single-file JavaScript library with 348 functions
- Self-contained with zero external dependencies
- Supports CommonJS, AMD, and ES modules via dist builds

**Core Components:**

1. **Public API Layer** (lines 4-850) - User-facing functions and configuration
2. **Request Processing** (lines 850-2000) - AJAX handling and HTTP verbs
3. **DOM Manipulation** (lines 2000-3500) - Element processing and swapping
4. **Event System** (lines 3500-4500) - Event handling and lifecycle management
5. **Extensions API** (lines 4950+) - Plugin system for extending functionality

### Build System

```text
src/htmx.js          # Source file (5,310 lines)
├── dist/htmx.js     # Distribution build (167KB)
├── dist/htmx.min.js # Minified build (51KB)
├── dist/htmx.min.js.gz # Gzipped build (16KB)
├── dist/htmx.esm.js # ES Module build
├── dist/htmx.cjs.js # CommonJS build
└── dist/htmx.amd.js # AMD build
```

**Module Formats:**

- **ESM**: `import htmx from 'htmx.org/dist/htmx.esm.js'`
- **CommonJS**: `const htmx = require('htmx.org')`
- **AMD**: `require(['htmx.org'], function(htmx) {...})`
- **UMD**: `<script src="https://unpkg.com/htmx.org@2.0.6"></script>`

### Dependencies

```json
{
  "name": "htmx.org",
  "version": "2.0.6",
  "license": "0BSD",
  "dependencies": {} // Zero runtime dependencies!
}
```

**Why zero dependencies?**

- **Minimal footprint**: Reduces bundle size and attack surface
- **Maximum compatibility**: Works in any JavaScript environment
- **Faster load times**: Single file, no dependency resolution
- **Reliability**: No supply chain vulnerabilities

## Code Structure

### Source Organization

```text
htmx/
├── src/
│   ├── htmx.js              # Main library (5,310 lines)
│   └── htmx.test.ts         # TypeScript definitions test
├── dist/                    # Built distributions (7 formats)
│   └── ext/                 # Official extensions (27 files)
├── test/                    # Test suites (52 files)
│   ├── core/               # Core functionality tests (14 files)
│   ├── attributes/         # Attribute behavior tests (35 files)
│   └── manual/             # Manual testing scenarios
├── www/                    # Documentation site
└── scripts/                # Build and deployment scripts
```

### Testing Architecture

**Test Coverage:** 100% line coverage achieved

- **Core tests**: 14 files, 6,885 lines testing fundamental features
- **Attribute tests**: 35 files testing individual `hx-*` attributes
- **Manual tests**: Browser-based integration tests
- **Performance tests**: Load testing and benchmark suites

**Test Categories:**

- AJAX request handling and response processing
- DOM swapping and mutation strategies
- Event lifecycle and custom events
- Security features (CSP compatibility, XSS protection)
- Browser compatibility and edge cases

## API Surface

### Core HTMX Object

```javascript
// Global htmx object with public API
const htmx = {
  // Event processing
  onLoad: function(callback),     // Execute callback on DOM ready
  process: function(element),     // Process htmx attributes on element
  on: function(event, selector, handler), // Event delegation
  off: function(event, selector, handler), // Remove event listeners
  trigger: function(selector, event, detail), // Trigger custom events

  // AJAX utilities
  ajax: function(verb, path, context), // Programmatic AJAX requests

  // DOM querying
  find: function(selector),       // Find single element
  findAll: function(selector),    // Find multiple elements
  closest: function(element, selector), // Find closest ancestor
  values: function(element, verb), // Get form values

  // DOM manipulation
  remove: function(element),      // Remove element from DOM
  addClass: function(element, class), // Add CSS class
  removeClass: function(element, class), // Remove CSS class
  toggleClass: function(element, class), // Toggle CSS class
  takeClass: function(element, class), // Take class from siblings

  // Configuration
  config: {
    historyEnabled: true,         // Enable history API
    historyCacheSize: 10,         // History cache size
    refreshOnHistoryMiss: false,  // Refresh on cache miss
    defaultSwapStyle: 'innerHTML', // Default swap strategy
    defaultSwapDelay: 0,          // Swap animation delay
    defaultSettleDelay: 20,       // DOM settle delay
    includeIndicatorStyles: true, // Include loading indicators
    indicatorClass: 'htmx-indicator', // Loading indicator class
    requestClass: 'htmx-request', // Active request class
    addedClass: 'htmx-added',     // Added element class
    settlingClass: 'htmx-settling', // Settling element class
    swappingClass: 'htmx-swapping', // Swapping element class
    allowEval: true,              // Allow script evaluation
    allowScriptTags: true,        // Allow script tag processing
    inlineScriptNonce: '',        // CSP nonce for inline scripts
    attributePrefix: 'hx-',       // Attribute prefix
    useTemplateFragments: false,  // Use template fragments
    disableSelector: '[hx-disable], [data-hx-disable]', // Disable selector
    withCredentials: false,       // Send credentials with requests
    timeout: 0,                   // Request timeout (0 = no timeout)
    wsReconnectDelay: 'full-jitter', // WebSocket reconnection strategy
    wsBinaryType: 'blob',         // WebSocket binary data type
    disableInheritance: false,    // Disable attribute inheritance
    responseHandling: [...],      // Response handling rules
    allowNestedOobSwaps: true     // Allow nested out-of-band swaps
  }
}
```

### HTML Attributes (hx-*)

**Request Attributes:**

- `hx-get`, `hx-post`, `hx-put`, `hx-patch`, `hx-delete` - HTTP verbs
- `hx-trigger` - Event that triggers the request
- `hx-target` - Element to update with response
- `hx-swap` - How to swap the response content
- `hx-params` - Parameters to include/exclude
- `hx-headers` - Custom headers to send
- `hx-vals` - Additional values to submit
- `hx-confirm` - Confirmation dialog before request
- `hx-push-url` - Update browser URL
- `hx-replace-url` - Replace browser URL

**Behavior Attributes:**

- `hx-boost` - Progressive enhancement for forms/links
- `hx-indicator` - Show/hide loading indicators
- `hx-disabled-elt` - Disable elements during request
- `hx-include` - Include additional form fields
- `hx-preserve` - Preserve elements during swaps
- `hx-select` - Select portion of response
- `hx-select-oob` - Out-of-band swaps
- `hx-swap-oob` - Out-of-band target specification
- `hx-encoding` - Request encoding type

**Event Attributes:**

- `hx-on` - Inline event handlers
- `hx-on:*` - Event-specific handlers (e.g., `hx-on:click`)

### Extension System

HTMX provides a powerful extension API with 27 official extensions:

**Popular Extensions:**

- `ws.js` (13,899 lines) - WebSocket support
- `sse.js` (10,320 lines) - Server-Sent Events
- `preload.js` (4,987 lines) - Intelligent preloading
- `response-targets.js` (4,672 lines) - Response-based targeting
- `head-support.js` (6,484 lines) - `<head>` tag merging
- `loading-states.js` (5,375 lines) - Advanced loading states
- `client-side-templates.js` (4,512 lines) - Template rendering

**Extension API:**

```javascript
htmx.defineExtension('my-extension', {
  onEvent: function(name, evt) {}, // Handle htmx events
  transformResponse: function(text, xhr, elt) {}, // Transform response
  isInlineSwap: function(swapStyle) {}, // Custom swap styles
  handleSwap: function(swapStyle, target, fragment, settleInfo) {}, // Custom swapping
  encodeParameters: function(xhr, parameters, elt) {} // Custom parameter encoding
})
```

## Request/Response Flow

### 1. Request Initiation

```javascript
// Triggered by user interaction or programmatically
htmx.ajax('GET', '/api/data', '#target')

// Or via HTML attributes
<button hx-get="/api/data" hx-target="#target">Load Data</button>
```

### 2. Request Processing

1. **Event validation** - Check if request should proceed
2. **Parameter collection** - Gather form data and custom values
3. **Header preparation** - Set custom headers and CSRF tokens
4. **Request dispatch** - Execute XMLHttpRequest or fetch
5. **Progress indication** - Show loading states and indicators

### 3. Response Handling

1. **Status validation** - Check HTTP response codes
2. **Content processing** - Parse and prepare response content
3. **Target selection** - Determine which elements to update
4. **Swapping strategy** - Apply content using swap method
5. **Settlement** - Finalize DOM changes and cleanup

### 4. DOM Updates

**Swap Strategies:**

- `innerHTML` - Replace element content (default)
- `outerHTML` - Replace entire element
- `beforebegin` - Insert before element
- `afterbegin` - Insert at element start
- `beforeend` - Insert at element end
- `afterend` - Insert after element
- `delete` - Delete target element
- `none` - Don't swap content

## Security Model

### Content Security Policy (CSP)

- **CSP Compatible**: `allowEval: false` disables eval usage
- **Nonce Support**: `inlineScriptNonce` for inline script security
- **Script Processing**: Configurable script tag evaluation
- **Safe Defaults**: Conservative security settings

### Cross-Site Scripting (XSS) Protection

- **Input Sanitization**: Server-side responsibility
- **Response Filtering**: Content type validation
- **Script Isolation**: Controlled script execution context
- **Attribute Validation**: Safe attribute parsing

### Cross-Site Request Forgery (CSRF)

- **Token Headers**: Automatic CSRF token inclusion
- **Credential Handling**: Configurable `withCredentials`
- **Origin Validation**: Same-origin policy enforcement
- **Custom Headers**: Anti-CSRF header support

## Performance Characteristics

### Bundle Size Analysis

- **Source**: 167KB (readable, documented)
- **Minified**: 51KB (production build)
- **Gzipped**: 16KB (over-the-wire size)
- **Parse Time**: ~2ms on modern browsers
- **Memory Footprint**: ~100KB runtime overhead

### Runtime Performance

- **DOM Queries**: Optimized selector caching
- **Event Delegation**: Efficient event bubbling
- **Request Pooling**: Automatic request deduplication
- **Memory Management**: Automatic cleanup of event listeners
- **Progressive Enhancement**: No framework lock-in

### Optimization Features

- **Lazy Loading**: On-demand resource fetching
- **Request Caching**: Configurable history cache
- **Debouncing**: Built-in input debouncing
- **Batching**: Request batching for efficiency
- **Preloading**: Intelligent resource prefetching

## Browser Compatibility

### Supported Browsers

- **Modern Browsers**: Chrome 60+, Firefox 55+, Safari 12+, Edge 79+
- **Legacy Support**: IE11 with polyfills (XMLHttpRequest, Promise)
- **Mobile Browsers**: iOS Safari 12+, Chrome Mobile 60+
- **Server-Side Rendering**: Node.js with jsdom

### Polyfill Requirements (IE11)

```html
<!-- For IE11 support -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6,fetch"></script>
```

## Development Workflow

### Build Process

```bash
# Development setup
npm install                    # Install dev dependencies
npm run build                 # Build all distributions
npm run minify                # Create minified builds
npm test                      # Run test suite
npm run test:coverage         # Generate coverage reports
```

### Testing Commands

```bash
npm test                      # Full test suite
npm run test:unit            # Unit tests only
npm run test:integration     # Integration tests
npm run test:manual          # Manual browser tests
npm run test:performance     # Performance benchmarks
```

### Distribution Building

```bash
# Build all formats
scripts/dist.sh

# Generate TypeScript definitions
scripts/generate-web-types.mjs

# Create website
scripts/www.sh
```

## Common Usage Patterns

### Basic AJAX Form

```html
<form hx-post="/submit" hx-target="#result">
  <input name="email" type="email" required>
  <button type="submit">Submit</button>
</form>
<div id="result"></div>
```

### Live Search

```html
<input hx-get="/search"
       hx-trigger="keyup changed delay:300ms"
       hx-target="#results"
       name="query">
<div id="results"></div>
```

### Infinite Scroll

```html
<div hx-get="/more"
     hx-trigger="revealed"
     hx-swap="afterend"
     hx-target="this">
  Load More...
</div>
```

### Real-time Updates

```html
<!-- Server-Sent Events -->
<div hx-ext="sse"
     sse-connect="/events"
     sse-swap="message"
     hx-target="#notifications">
</div>

<!-- WebSocket -->
<div hx-ext="ws"
     ws-connect="/websocket"
     hx-target="#chat">
</div>
```

### Progressive Enhancement

```html
<!-- Works without JavaScript -->
<a href="/page2" hx-boost="true">Next Page</a>

<!-- Enhanced with HTMX -->
<form action="/submit" method="post" hx-boost="true">
  <input name="data" required>
  <button type="submit">Submit</button>
</form>
```

## Integration Patterns

### Backend Frameworks

**Python:**

- Django with django-htmx
- FastAPI with htmx responses
- Flask with render_template

**JavaScript:**

- Express.js with template engines
- Next.js for SSR/SSG hybrid
- Node.js with server-side rendering

**Go:**

- Gin with html/template
- Echo with templating
- Standard library http server

**Ruby:**

- Rails with Turbo (similar concepts)
- Sinatra with ERB templates
- Hanami framework

### CSS Frameworks

```html
<!-- Bootstrap -->
<div class="spinner-border htmx-indicator"></div>

<!-- Tailwind CSS -->
<div class="animate-spin htmx-indicator">...</div>

<!-- Custom CSS -->
.htmx-indicator { display: none; }
.htmx-request .htmx-indicator { display: block; }
```

### State Management

```javascript
// Global state via htmx events
document.addEventListener('htmx:afterRequest', function(evt) {
  if (evt.detail.xhr.status === 401) {
    window.location.href = '/login'
  }
})

// Local state via data attributes
<div hx-get="/api/user"
     hx-trigger="load"
     data-user-id="123"
     hx-include="[data-user-id]">
</div>
```

## Error Handling

### HTTP Error Responses

```javascript
// Global error handling
document.addEventListener('htmx:responseError', function(evt) {
  if (evt.detail.xhr.status === 404) {
    evt.detail.target.innerHTML = '<p>Not found</p>'
  }
})

// Per-element error handling
<div hx-get="/api/data"
     hx-on:htmx:response-error="handle_error(event)">
</div>
```

### Network Error Handling

```javascript
// Connection failures
document.addEventListener('htmx:sendError', function(evt) {
  showNotification('Connection failed, please try again')
})

// Timeout handling
htmx.config.timeout = 5000 // 5 second timeout
document.addEventListener('htmx:timeout', function(evt) {
  evt.detail.target.innerHTML = '<p>Request timed out</p>'
})
```

## Debugging and Development

### Debug Mode

```html
<script src="htmx.org/dist/ext/debug.js"></script>
<script>htmx.logAll()</script> <!-- Log all events -->
```

### Event Monitoring

```javascript
// Monitor all htmx events
htmx.on('htmx:*', function(evt) {
  console.log(evt.type, evt.detail)
})

// Specific event monitoring
htmx.on('htmx:beforeRequest', function(evt) {
  console.log('Requesting:', evt.detail.pathInfo.requestPath)
})
```

### Browser DevTools

- **Network Tab**: Monitor AJAX requests and responses
- **Elements Tab**: Inspect dynamic DOM changes
- **Console**: View htmx events and debug output
- **Performance Tab**: Analyze request timing and DOM updates

## Future Roadmap & Extensions

### Planned Features

- Enhanced TypeScript support
- Improved accessibility features
- Better debugging tools
- Performance optimizations
- Additional swap strategies

### Extension Ecosystem

Active extensions for specialized use cases:

- **Alpine.js Integration**: Reactive state management
- **Morphdom**: Advanced DOM diffing
- **Template Engines**: Client-side templating
- **Loading States**: Advanced progress indicators
- **WebSocket/SSE**: Real-time communication
- **Path Dependencies**: Smart caching strategies

### Community Contributions

- **Third-party Extensions**: Community-built extensions
- **Framework Integrations**: Official framework support
- **Documentation**: Community-maintained examples
- **Testing**: Community test contributions

## AI Agent Notes

HTMX is exceptionally well-suited for AI-assisted development:

- **Declarative Syntax**: HTML attributes are self-documenting
- **Minimal JavaScript**: Less complexity for AI to understand
- **Progressive Enhancement**: Works without JavaScript
- **Single File**: Entire library in one comprehensible file
- **Extensive Tests**: 100% test coverage with clear examples
- **Rich Documentation**: Comprehensive guides and examples
- **Hypermedia Philosophy**: Simple request/response patterns

The codebase follows functional programming patterns with clear separation of concerns, making it ideal for AI code analysis and enhancement.
