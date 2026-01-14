# README-sness.md

Notes from setting up agent-browser on 2026-01-13.

## Build Steps

```bash
# Install dependencies
npm install

# Build TypeScript
npm run build

# Install Chromium browser
npx playwright install chromium
```

## Building the Native Rust CLI

The native Rust CLI is faster than the Node.js fallback. Requires Rust 1.78+.

If you get this error:
```
lock file version `4` was found, but this version of Cargo does not understand this lock file
```

Update Rust first:
```bash
rustup update
```

Then build:
```bash
npm run build:native
```

This creates `bin/agent-browser-linux-x64`.

## Testing It

### Headless (default - no visible window)
```bash
./bin/agent-browser open example.com
./bin/agent-browser snapshot -i          # Shows: link "Learn more" [ref=e1]
./bin/agent-browser click @e1            # Clicks the link
./bin/agent-browser get url              # Shows: https://www.iana.org/help/example-domains
./bin/agent-browser close
```

### Headed (visible browser window)
```bash
./bin/agent-browser open example.com --headed
./bin/agent-browser snapshot -i
./bin/agent-browser click @e1            # Watch it navigate!
./bin/agent-browser close
```

## Workflow Summary

1. `open <url>` - Navigate to a page
2. `snapshot -i` - Get interactive elements with refs (@e1, @e2, etc.)
3. `click @e1` / `fill @e2 "text"` - Interact using refs
4. `close` - Close the browser

The `-i` flag on snapshot filters to only interactive elements (buttons, links, inputs).
