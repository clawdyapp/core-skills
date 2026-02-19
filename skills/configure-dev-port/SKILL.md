---
name: configure-dev-port
description: When the user is developing an application with a dev server and needs to configure the port it runs on. Also use when the user mentions "set dev port," "configure server port," "dev server port," "run dev server on port," or needs to specify which port their development server uses.
---

# Configure Dev Port

You are an expert in configuring development server ports for applications. Your goal is to help users set up the correct port for their dev server in the project configuration files and ensure they can access it via the public URL.

## Initial Assessment

Before proceeding, verify:

1. **What port does the user want to use?** (obtain from context or ask if not specified)
2. **What is the project type?** (Next.js, Vite, Express, Node.js, etc.)
3. **How is the port currently configured?** (check for `package.json` scripts or `.env` file)
4. **What port variable/script is used?** (PORT, SERVER_PORT, DEV_PORT, VITE_PORT, etc.)

## Port Configuration

### Step 1: Identify Configuration Method

Check which configuration method the project uses:

**Option A: package.json**
```json
{
  "scripts": {
    "dev": "next dev",
    "start": "PORT=3000 node server.js"
  }
}
```

**Option B: .env file**
```
PORT=3000
SERVER_PORT=3000
DEV_PORT=3000
```

**Option C: .env.local file**
```
NEXT_PUBLIC_PORT=3000
```

### Step 3: Update Configuration

Based on the configuration method identified, apply the detected port:

**For package.json scripts:**
```json
{
  "scripts": {
    "dev": "PORT=<DETECTED_PORT> next dev"
  }
}
```

**For .env file:**
```
PORT=<DETECTED_PORT>
```

**For multiple environment files:**
Update all relevant files (.env, .env.local, .env.development, etc.)

## Accessing the Development Server

Once configured, the development server is accessible at:

```
https://{PORT}--{machineSlug}.clawdy.app
```

### Examples

- Port `3000` on machine slug `dev-machine-1`: `https://3000--dev-machine-1.clawdy.app`
- Port `8080` on machine slug `workspace-42`: `https://8080--workspace-42.clawdy.app`
- Port `5173` on machine slug `vite-dev`: `https://5173--vite-dev.clawdy.app`

## Handling Common Scenarios

### Multiple Configuration Files

If the project has multiple configuration methods:
1. Check which one is actually used by the dev script
2. Update the primary configuration
3. Ensure consistency across all config files

### Framework-Specific Port Configuration

Different frameworks use different port variables and methods:

| Framework | Default Port | Variable | Configuration Method |
|-----------|--------------|----------|----------------------|
| Next.js | 3000 | PORT | .env.local or package.json |
| Vite | 5173 | VITE_PORT | .env.development |
| React (Create React App) | 3000 | PORT | .env or package.json |
| Express/Node.js | 3000 | PORT | .env or server code |
| Vue/Nuxt | 3000 | PORT | nuxt.config.ts or .env |
| Svelte/SvelteKit | 5173 | VITE_PORT | svelte.config.js or .env |

### Vite-Specific Configuration

For Vite projects, after setting the port, you may also need to configure `allowedHosts` in `vite.config.ts` or `vite.config.js`:

**vite.config.ts:**
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    allowedHosts: [
      'localhost',
      '127.0.0.1',
      '5173--{machineSlug}.clawdy.app',
      // Add your machine slug here, e.g., '5173--workspace-42.clawdy.app'
    ],
  },
})
```

**Or allow all hosts (less secure, for development only):**
```typescript
export default defineConfig({
  server: {
    allowedHosts: 'all',
  },
})
```

This ensures the Vite dev server accepts requests from the public clawdy.app domain.

### Persistent Configuration

To ensure the port persists across sessions:
1. Use `.env.local` for local development (not committed to git)
2. Use `.env` for shared defaults (committed to git)
3. Use `.env.production` for production ports if applicable
4. Document the expected port in project README

## Troubleshooting

### Port Already in Use
- Check if another process is using the port: `lsof -i :<PORT>`
- Kill the process occupying the port: `kill -9 <PID>`
- Or choose a different port and update configuration

### Vite: Host Mismatch Error
If you see an error like "Invalid Host Header" or the app doesn't work on the clawdy.app domain:
- Check your `vite.config.ts` has the correct `allowedHosts` configured
- Add the full domain: `{PORT}--{machineSlug}.clawdy.app`
- Or use `allowedHosts: 'all'` for development (restart dev server after change)
- Verify the port in the allowedHosts matches the port the server is running on

### Configuration Changes Not Applied
- Restart the development server after configuration update
- Clear environment cache: `unset PORT` (in bash) or restart terminal
- Verify the update was written to the correct file
- Check for conflicting environment variable settings

### Server Starts on Wrong Port
- Verify the PORT environment variable is set correctly
- Check that no hardcoded port settings in the code override the env variable
- Ensure the configuration file is in the correct location
- Check for multiple .env files and ensure the right one is being used

### Cannot Access via Public URL
- Confirm the port matches what the server is actually running on
- Verify the `machineSlug` from your environment
- Check network connectivity to the clawdy.app domain
- Ensure the dev server is fully started before accessing
- For Vite, verify `allowedHosts` configuration is correct

## Verification

After configuration:

```bash
# Verify the port environment variable is set
echo $PORT

# Verify the configuration file contains the port
cat .env.local | grep PORT

# Verify the server starts on the specified port
npm run dev
# Look for output like: "ready - started server on 0.0.0.0:3000"

# Test access via public URL
curl https://{PORT}--{machineSlug}.clawdy.app
```

## Related Skills

- **setup-dev-environment**: Initial project setup and dependencies
- **manage-environment-variables**: Advanced .env management
- **troubleshoot-port-conflicts**: Resolving port-related issues
