# 🧩 Angular Module Federation Workspace

This monorepo uses **Module Federation** to implement a Micro-frontend (MFE) architecture. This allows multiple teams to build, ship, and deploy Angular applications independently while sharing a single runtime shell.

## 🏗️ Architecture Overview

* **Host (Shell):** The main entry point. It handles routing, shared authentication, and dynamic loading of remotes.
* **Remotes (Micro-apps):** Independent applications (e.g., `inventory`, `billing`) that are "plugged in" to the Host at runtime.
* **Shared Libs:** Logic used by both Host and Remotes (e.g., `data-access`, `ui-kit`).

---

## 🛠️ Project Structure

* `apps/shell`: The Host application.
* `apps/profile`: Remote MFE .
* `apps/projects`: Remote MFE .
* `libs/shared/ui`: Shared ui .

---

## 🚀 Local Development

### Running the Entire System

To run the Host and **all** associated remotes simultaneously:

```bash
npx nx serve shell --devRemotes=profile,projects

```

### Running Individually

If you are only working on one remote, you can run it standalone to save memory:

```bash
npx nx serve profile

```

---

## 📦 How to Add a New Remote

To add a new micro-frontend to this workspace, use the Nx generator:

```bash
npx nx g @nx/angular:remote my-new-app --host=shell

```

This command automatically:

1. Creates the app in `apps/my-new-app`.
2. Configures `module-federation.config.js`.
3. Adds the remote definition to the `shell` application's configuration.

---

## 🔗 Shared Dependencies

Module Federation allows us to share singleton instances of libraries (like `@angular/core` or `rxjs`) so they aren't downloaded multiple times.

* Check `module-federation.config.js` in each app to see which libraries are marked as shared.
* **Version Mismatch:** Ensure all apps use the same version of Angular to avoid "Version Mismatch" errors at runtime.

---

## 🚢 Deployment Strategy

1. **Build Remotes First:** Each remote should be built and deployed to its own bucket/server.
2. **Update Manifest:** The `shell` uses a `module-federation.manifest.json` (or hardcoded URLs) to find the remotes. Ensure these URLs point to your production CDN.

```bash
npx nx build profile --prod
npx nx build shell --prod

```

---

## 🧪 Testing

* **Unit Tests:** `npx nx test profile`
* **E2E (Cypress):** `npx nx e2e shell-e2e` (This will test the integration of all MFEs).

---

### Pro-Tip for Module Federation:

When debugging, check the **Network Tab** in your browser. You should see `remoteEntry.mjs` files being loaded from different ports/URLs. That is the "heartbeat" of Module Federation!
