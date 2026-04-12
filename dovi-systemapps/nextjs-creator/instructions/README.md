# Next.js App Router Projects

Projects created via the Next.js Creator app live in `/root/projects/<name>/`.

## Project Structure

```
/root/projects/<name>/
  src/
    app/
      layout.tsx       # Root layout (html, body, metadata)
      page.tsx          # Home page (/)
      globals.css       # Global styles
  public/              # Static assets
  next.config.ts       # Next.js configuration
  package.json
  tsconfig.json
```

## Development

```bash
cd /root/projects/<name>
npm run dev            # Start dev server (port 3000 by default)
npm run build          # Production build
npm start              # Start production server
```

To use a different port:
```bash
PORT=3001 npm run dev
```

## Setting Up a Domain

Use the Dovi CLI to create a subdomain with automatic nginx + SSL:

```bash
dovi domain create <name>
```

This creates `<name>.<server-domain>` with nginx configured. Then update the nginx config to proxy to the Next.js port:

```nginx
location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

## Running in Production

Create a systemd service for the app:

```bash
cat > /etc/systemd/system/<name>.service << 'EOF'
[Unit]
Description=Next.js app - <name>
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/projects/<name>
ExecStart=/usr/bin/npm start
Restart=on-failure
Environment=NODE_ENV=production PORT=3000

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable --now <name>
```

## Key Next.js App Router Concepts

- **`app/` directory**: File-system routing. Each folder = route segment
- **`page.tsx`**: Makes a route publicly accessible
- **`layout.tsx`**: Shared UI that wraps child routes (preserves state on navigation)
- **`loading.tsx`**: Loading UI (React Suspense boundary)
- **`error.tsx`**: Error UI (React Error Boundary)
- **`route.ts`**: API route handler (GET, POST, etc.)
- **Server Components**: Default — render on server, no client JS
- **`'use client'`**: Opt into Client Component for interactivity

## Common Tasks

```bash
# Add a dependency
cd /root/projects/<name> && npm install <package>

# Create a new page
mkdir -p src/app/about && cat > src/app/about/page.tsx << 'EOF'
export default function AboutPage() {
  return <h1>About</h1>
}
EOF

# Create an API route
mkdir -p src/app/api/hello && cat > src/app/api/hello/route.ts << 'EOF'
export async function GET() {
  return Response.json({ message: 'Hello!' })
}
EOF
```
