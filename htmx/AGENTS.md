# AGENTS.md

## Project Overview

HTMX is a lightweight JavaScript library that extends HTML with modern web capabilities through declarative attributes. It enables dynamic web applications without complex JavaScript frameworks by bringing AJAX, CSS transitions, WebSockets, and Server-Sent Events directly to HTML.

**Key characteristics:**

- Single-file library (~16KB gzipped, zero dependencies)
- Hypermedia-driven development philosophy
- Progressive enhancement focused
- 100% test coverage with comprehensive test suite

## Setup Commands

```bash
# Install dependencies
npm install

# Development server (for testing)
npx serve
# Then navigate to: http://0.0.0.0:3000/test/

# Build distributions
npm run dist

# Run tests
npm test                    # Lint + types + Chrome tests
npm run test:chrome         # Chrome only
npm run test:firefox        # Firefox only
npm run test:all            # All browsers
npm run test:debug          # Manual/debug mode

# Linting and formatting
npm run lint                # Check code style
npm run format              # Auto-fix code style

# Type checking
npm run types-check         # TypeScript validation
npm run types-generate      # Generate .d.ts files

# Generate web-types (IDE support)
npm run web-types-generate
```

## Architecture

### Core Structure

```bash
htmx/
├── src/htmx.js              # Main library (5,310 lines, single file)
├── dist/                    # Built distributions
│   ├── htmx.js              # Unminified (167KB)
│   ├── htmx.min.js          # Minified (51KB)
│   ├── htmx.esm.js          # ES Module
│   ├── htmx.cjs.js          # CommonJS
│   └── ext/                 # Official extensions (27 files)
├── test/                    # Test suite (100% coverage)
│   ├── attributes/          # hx-* attribute tests (35 files)
│   ├── core/                # Core functionality tests (14 files)
│   └── manual/              # Manual browser tests
└── www/                     # Documentation site
```

### Code Organization (src/htmx.js)

- **Lines 1-850**: Public API and configuration
- **Lines 850-2000**: Request processing and HTTP handling
- **Lines 2000-3500**: DOM manipulation and swapping
- **Lines 3500-4500**: Event system and lifecycle
- **Lines 4500-5310**: Extensions API and utilities

## Development Workflow

### Making Changes

1. Edit `/src/htmx.js` - the single source file
2. Add tests in appropriate `/test/` subdirectory
3. Run `npm test` to validate changes
4. Run `npm run dist` to build distributions
5. Test manually at <http://0.0.0.0:3000/test/>

### Testing Strategy

- **Unit tests**: Core functionality in `/test/core/`
- **Attribute tests**: Each hx-* attribute in `/test/attributes/`
- **Integration tests**: Real browser scenarios
- **Manual tests**: `/test/manual/` for complex interactions
- **Regression tests**: `/test/core/regressions.js`

### Build Process

The `dist.sh` script creates multiple distribution formats:

- UMD build (browser globals)
- ES Module build (import/export)
- CommonJS build (require/module.exports)
- AMD build (define/require)
- Minified versions with source maps

## Code Style

### JavaScript Style

- ES5 compatible (IE11 support)
- Functional programming patterns
- No external dependencies
- Extensive JSDoc comments
- Single-var declarations
- Consistent indentation (2 spaces)

### Naming Conventions

- Public API: camelCase (`htmx.onLoad`)
- Internal functions: camelCase with descriptive names
- HTML attributes: kebab-case with `hx-` prefix
- CSS classes: kebab-case with `htmx-` prefix
- Events: kebab-case with `htmx:` namespace

### Configuration

The library uses a central `htmx.config` object for all settings:

- Request behavior (timeout, credentials, headers)
- DOM processing (swap styles, selectors)
- History management (caching, navigation)
- Security settings (CSP, script evaluation)

## Key Concepts

### Request/Response Cycle

1. **Trigger**: User interaction or programmatic call
2. **Prepare**: Collect parameters, headers, validation
3. **Request**: XMLHttpRequest with appropriate verb
4. **Process**: Parse response, select targets
5. **Swap**: Update DOM using specified strategy
6. **Settle**: Finalize changes, trigger events

### Swap Strategies

- `innerHTML`: Replace element content (default)
- `outerHTML`: Replace entire element
- `beforebegin/afterbegin/beforeend/afterend`: Insertion positions
- `delete`: Remove target element
- `none`: Don't update DOM

### Event System

HTMX uses a comprehensive event system:

- Lifecycle events (before/after request, swap, settle)
- Error events (response errors, timeouts, validation)
- Custom events (user-defined triggers)
- Event delegation for dynamic content

## Extensions

### Extension API

Extensions can hook into:

- `onEvent`: Handle any htmx event
- `transformResponse`: Modify response before processing
- `isInlineSwap`: Define custom swap behaviors
- `handleSwap`: Implement custom swapping logic
- `encodeParameters`: Custom parameter serialization

### Popular Extensions

- `ws.js`: WebSocket support
- `sse.js`: Server-Sent Events
- `preload.js`: Intelligent preloading
- `response-targets.js`: Dynamic response targeting
- `head-support.js`: Merge `<head>` content
- `loading-states.js`: Advanced loading indicators

## Testing Guidelines

### Test File Structure

```javascript
describe("hx-get", function() {
  beforeEach(function() {
    this.server = makeServer()
    clearWorkArea()
  })
  
  it('should issue a GET request', function() {
    var button = make('<button hx-get="/test">Click</button>')
    button.click()
    this.server.requests[0].method.should.equal("GET")
  })
})
```

### Test Utilities

- `make(html)`: Create DOM elements
- `clearWorkArea()`: Reset test environment
- `makeServer()`: Mock server for AJAX
- `byId(id)`: Get element by ID
- `eventually(assertion)`: Async test helper

## Security Considerations

### Content Security Policy

- Set `htmx.config.allowEval = false` to disable eval
- Use `inlineScriptNonce` for CSP compliance
- Configure `allowScriptTags` for script processing

### XSS Prevention

- Server-side input sanitization required
- Response content type validation
- Controlled script execution context

### CSRF Protection

- Automatic CSRF token inclusion
- Custom header support
- Same-origin policy enforcement

## Performance Optimization

### Bundle Size

- Core library: 16KB gzipped
- Extensions: Load only what's needed
- No runtime dependencies

### Runtime Performance

- Selector caching for DOM queries
- Event delegation for efficiency
- Request deduplication
- Automatic memory cleanup

## Debugging

### Debug Mode

```html
<script src="htmx.org/dist/ext/debug.js"></script>
<script>htmx.logAll()</script>
```

### Event Monitoring

```javascript
// Log all htmx events
htmx.on('htmx:*', function(evt) {
  console.log(evt.type, evt.detail)
})

// Monitor specific events
htmx.on('htmx:beforeRequest', function(evt) {
  console.log('Request:', evt.detail.pathInfo.requestPath)
})
```

## Common Patterns

### Basic AJAX

```html
<button hx-post="/api/action" hx-target="#result">
  Submit
</button>
<div id="result"></div>
```

### Live Search

```html
<input hx-get="/search" 
       hx-trigger="keyup changed delay:300ms"
       hx-target="#results"
       name="q">
```

### Progressive Enhancement

```html
<!-- Works without JS -->
<form action="/submit" method="post" hx-boost="true">
  <input name="data">
  <button>Submit</button>
</form>
```

## AI Agent Guidelines

### Code Analysis

- Focus on `src/htmx.js` - single source of truth
- Understand request/response cycle for debugging
- Check test files for usage examples
- Extensions are in `dist/ext/` directory

### Making Changes and Revisions

- Always add tests for new features
- Maintain IE11 compatibility (ES5 syntax)
- Follow existing functional programming patterns
- Update documentation in `/www/` if needed

### Testing Changes

- Run full test suite before submitting
- Test across multiple browsers for compatibility
- Add regression tests for bug fixes
- Use manual tests for complex interactions

### Extension Development

- Study existing extensions in `dist/ext/`
- Use the extension API hooks appropriately
- Document extension behavior thoroughly
- Provide usage examples and tests
