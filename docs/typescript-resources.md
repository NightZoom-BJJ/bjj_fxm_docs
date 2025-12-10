# TypeScript Resource Management

This guide covers everything you need to know about building and managing TypeScript resources with BJJ FX Manager.

## Table of Contents

- [Why This Matters](#why-this-matters)
- [Automatic Detection](#automatic-detection)
- [Build Process](#build-process)
- [Isolated Build Environments](#isolated-build-environments)
- [Resource Structure](#resource-structure)
- [Configuration Examples](#configuration-examples)
- [Build Performance](#build-performance)
- [Troubleshooting Builds](#troubleshooting-builds)

## Why This Matters

FiveM's ecosystem has rapidly adopted TypeScript for resource development, but the platform provides **no native automation** for TypeScript compilation. Before BJJ FXM, developers had to:

1. Manually SSH into Linux servers
2. Navigate to each resource directory
3. Run `npm install` and `npm run build` individually
4. Manage Node.js versions and build tools system-wide
5. Restart the server manually after builds
6. Deal with contaminated system environments from build dependencies

BJJ FXM automates this entire workflow while maintaining system cleanliness through isolated build contexts.

## Automatic Detection

BJJ FXM automatically identifies TypeScript resources by scanning for:

1. **`.ts` files** in resource directories
2. **`tsconfig.json`** configuration files
3. **`package.json`** with TypeScript dependencies
4. **Custom build scripts** defined in `package.json`

### Detection Process

When you run `fxm build` or `fxm start`, BJJ FXM:

1. Scans the resources directory
2. Identifies all directories containing TypeScript files
3. Checks for configuration files
4. Determines the appropriate build method
5. Queues resources for building

## Build Process

BJJ FXM executes an intelligent build pipeline for each TypeScript resource:

### Phase 1: Dependency Management

**Checks:**
- Looks for `package.json`
- Detects package manager (npm/yarn/pnpm) by lock files
- Checks if `node_modules` exists

**Actions:**
- Installs dependencies if `node_modules` is missing
- Uses the appropriate package manager
- Runs in isolated context
- Logs installation progress

### Phase 2: Build Execution

**Priority Order:**
1. **Custom build script** - Uses `npm run build` if defined
2. **Direct TypeScript compilation** - Uses `npx tsc` if `tsconfig.json` exists
3. **Fallback compilation** - Generates basic `tsconfig.json` and compiles

**Build Command Examples:**
```bash
# Custom script (if package.json has "build" script)
npm run build

# Direct compilation (if tsconfig.json exists)
npx tsc

# Fallback (generates config and compiles)
npx tsc --init && npx tsc
```

### Phase 3: Validation

**Checks:**
- Verifies build output exists
- Checks for compilation errors
- Validates file permissions
- Confirms resource structure

**Actions:**
- Logs build results
- Reports errors with context
- Updates resource status
- Prepares for server start

## Isolated Build Environments

Critical for production Linux servers: **All build operations run in separate Node.js processes that terminate after completion.**

### Benefits

**System Cleanliness:**
- No global npm package pollution
- No persistent build tool processes
- Clean system Node environment

**Performance:**
- Build processes don't consume memory after completion
- No background build tool daemons
- Resources free immediately after build

**Reliability:**
- No dependency conflicts between resources
- Each resource builds in isolation
- Failures don't affect other resources

### How It Works

```typescript
// Conceptual overview - BJJ FXM's build process
for (const resource of typescriptResources) {
  // Spawn isolated Node process
  const buildProcess = spawn('node', [buildScript], {
    cwd: resource.path,
    env: cleanEnvironment
  });
  
  // Wait for completion
  await buildProcess;
  
  // Process terminates, memory freed
}
```

## Resource Structure

### Recommended Directory Layout

Organize TypeScript resources for optimal BJJ FXM integration:

```
resources/
├── my-typescript-resource/
│   ├── src/                      # TypeScript source files
│   │   ├── client/
│   │   │   ├── index.ts
│   │   │   └── controllers/
│   │   ├── server/
│   │   │   ├── index.ts
│   │   │   └── services/
│   │   └── shared/
│   │       ├── types.ts
│   │       ├── config.ts
│   │       └── utils.ts
│   ├── dist/                     # Build output (git-ignored)
│   │   ├── client/
│   │   │   └── index.js
│   │   └── server/
│   │       └── index.js
│   ├── fxmanifest.lua           # FiveM resource manifest
│   ├── package.json             # Dependencies and build scripts
│   ├── tsconfig.json            # TypeScript configuration
│   └── .gitignore               # Ignore dist/ and node_modules/
```

### Multi-Project Structure

For complex resources with web UI:

```
resources/
├── my-fullstack-resource/
│   ├── src/                      # Server-side TypeScript
│   │   ├── client/
│   │   └── server/
│   ├── web/                      # React/Vue/etc. UI
│   │   ├── src/
│   │   ├── public/
│   │   └── package.json
│   ├── dist/                     # Server build output
│   ├── web-dist/                 # UI build output
│   ├── fxmanifest.lua
│   ├── package.json
│   └── tsconfig.json
```

## Configuration Examples

### Basic package.json

```json
{
  "name": "my-fivem-resource",
  "version": "1.0.0",
  "description": "My awesome FiveM resource",
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch",
    "clean": "rm -rf dist"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "typescript": "^5.0.0"
  }
}
```

### Advanced package.json with Custom Build

```json
{
  "name": "advanced-resource",
  "version": "2.0.0",
  "scripts": {
    "prebuild": "npm run clean",
    "build": "npm run build:client && npm run build:server",
    "build:client": "tsc -p tsconfig.client.json",
    "build:server": "tsc -p tsconfig.server.json",
    "postbuild": "npm run copy-assets",
    "clean": "rm -rf dist",
    "copy-assets": "cp -r static/* dist/",
    "watch": "concurrently \"npm run build:client -- --watch\" \"npm run build:server -- --watch\""
  },
  "devDependencies": {
    "@citizenfx/client": "^2.0.0",
    "@citizenfx/server": "^2.0.0",
    "@types/node": "^20.0.0",
    "concurrently": "^8.0.0",
    "typescript": "^5.0.0"
  }
}
```

### Basic tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "moduleResolution": "node",
    "resolveJsonModule": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Advanced tsconfig.json with Path Mapping

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@shared/*": ["src/shared/*"],
      "@client/*": ["src/client/*"],
      "@server/*": ["src/server/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts"]
}
```

### Separate Client/Server Configs

**tsconfig.client.json:**
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./dist/client",
    "rootDir": "./src/client",
    "lib": ["ES2020", "DOM"]
  },
  "include": ["src/client/**/*", "src/shared/**/*"]
}
```

**tsconfig.server.json:**
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./dist/server",
    "rootDir": "./src/server"
  },
  "include": ["src/server/**/*", "src/shared/**/*"]
}
```

## Build Performance

### Optimization Tips

**1. Incremental Compilation**
```json
{
  "compilerOptions": {
    "incremental": true,
    "tsBuildInfoFile": "./dist/.tsbuildinfo"
  }
}
```

**2. Skip Lib Check**
```json
{
  "compilerOptions": {
    "skipLibCheck": true
  }
}
```

**3. Parallel Builds**
BJJ FXM automatically builds independent resources in parallel.

**4. Watch Mode for Development**
```bash
# Automatic rebuilds on file changes
fxm build --watch
```

### Build Time Expectations

| Resource Size | Files | Build Time | Watch Rebuild |
|---------------|-------|------------|---------------|
| Small         | <10   | 5-15 sec   | 1-3 sec       |
| Medium        | 10-50 | 15-45 sec  | 2-5 sec       |
| Large         | 50+   | 45-90 sec  | 5-10 sec      |

## Troubleshooting Builds

### Common Build Errors

**Error: Cannot find module**
```
Solution: Install missing dependencies
cd resources/your-resource
npm install
```

**Error: Build timeout**
```
Solution: Increase timeout in fxm.config.yml
resources:
  buildTimeout: 120000  # 2 minutes
```

**Error: Permission denied**
```
Solution: Fix file permissions
chmod -R 755 resources/your-resource
```

**Error: TSC not found**
```
Solution: Install TypeScript in resource
cd resources/your-resource
npm install --save-dev typescript
```

### Debug Build Issues

**Enable verbose logging:**
```yaml
# fxm.config.yml
logLevel: debug
```

**Manual build test:**
```bash
cd resources/your-resource
npm install
npm run build  # or npx tsc
```

**Check build logs:**
```bash
cat logs/build.log | grep ERROR
```

### Resource-Specific Build Configuration

Create `.fxmrc.json` in resource directory:

```json
{
  "buildTimeout": 120000,
  "buildCommand": "npm run custom-build",
  "watchIgnore": ["*.log", "temp/*"],
  "preBuildScript": "./scripts/prepare.sh",
  "postBuildScript": "./scripts/finalize.sh"
}
```

## Best Practices

### 1. Use Proper .gitignore

```gitignore
# Build outputs
dist/
*.js
*.js.map

# Dependencies
node_modules/
package-lock.json
yarn.lock

# TypeScript cache
*.tsbuildinfo

# IDE
.vscode/
.idea/

# Logs
*.log
```

### 2. Separate Source and Build

Always keep TypeScript source in `src/` and output in `dist/`:
- Cleaner project structure
- Easier to exclude from git
- Clear separation of concerns

### 3. Version Lock Dependencies

Use exact versions for critical dependencies:
```json
{
  "devDependencies": {
    "typescript": "5.3.0"
  }
}
```

### 4. Document Build Process

Add README to complex resources:
```markdown
# My Resource

## Building
npm install
npm run build

## Development
npm run watch
```

### 5. Test Locally Before Deployment

```bash
# Local build test
fxm build

# Verify output
ls -la resources/my-resource/dist/
```

---

[Back to Main README](../readme.md) | [Next: Production Guide](./production-guide.md)
