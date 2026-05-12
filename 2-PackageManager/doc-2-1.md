# 2. Package Managers & Version Compatibility

> Comparing npm, yarn, and pnpm — and understanding Node.js version requirements for Expo and React Native.

---

## 2.1 Expo SDK ↔ React Native ↔ Node.js Version Map

| Expo SDK | React Native | Node.js (min) | Notes |
|----------|-------------|---------------|-------|
| SDK 55   | 0.80        | 20            | Latest (2025) |
| SDK 54   | 0.79        | 20            | |
| SDK 53   | 0.79        | 20 (recommended) | Node 18 EOL April 2025, still works but not recommended |
| SDK 52   | 0.76 / 0.77 | 18            | Supports both RN 0.76 and 0.77 |
| SDK 51   | 0.74 / 0.75 | 18            | Supports both RN 0.74 and 0.75 |
| SDK 50   | 0.73        | 18            | Node 18 became default on EAS Build |
| SDK 49   | 0.72        | 16            | Last SDK supporting Node 16 |

---

## 2.2 React Native ↔ Node.js ↔ JDK Version Map

| React Native | Node.js (min) | JDK   | Android SDK (min) | Xcode (min) |
|-------------|---------------|-------|-------------------|-------------|
| 0.84–0.86   | 22.11.0       | 17    | Android 7.0       | 16.1        |
| 0.81–0.83   | 20.19.4       | 17    | Android 7.0       | 16.1        |
| 0.76–0.80   | 18            | 17    | Android 7.0       | 15.1        |
| 0.74–0.75   | 18            | 17    | Android 6.0       | 15.1        |
| 0.73        | 18            | 17    | Android 5.0       | 15.1        |
| 0.71–0.72   | 16            | 11    | Android 5.0       | 15.1        |

> **Key takeaway:** If you're starting a new project today, use **Node 20+** (LTS) and **JDK 17**.

---

## 2.3 Package Manager Comparison

### Overview

| Feature | npm | Yarn Classic (1.x) | Yarn Modern (3+/Berry) | pnpm |
|---------|-----|---------------------|------------------------|------|
| Comes with Node.js | Yes | No (install separately) | No | No |
| Speed | Moderate | Fast | Fast | Fastest |
| Disk usage | High (flat node_modules) | High (flat node_modules) | Low (PnP or node_modules) | Low (content-addressable store) |
| Lockfile | `package-lock.json` | `yarn.lock` | `yarn.lock` | `pnpm-lock.yaml` |
| Workspaces | Yes (v7+) | Yes | Yes | Yes |
| Deterministic | Yes | Yes | Yes | Yes |
| Plug'n'Play (PnP) | No | No | Yes (optional) | No |

---

### npm

The default package manager that ships with Node.js.

```bash
# Install
npm install

# Add a package
npm install react-native-reanimated

# Run scripts
npm run ios
```

**Pros:**
- Zero setup — comes with Node
- Widest community support
- Works out of the box with React Native & Expo

**Cons:**
- Slower than Yarn/pnpm for large projects
- Flat `node_modules` can cause phantom dependency issues

---

### Yarn Classic (1.x)

The original Yarn — still widely used in React Native projects.

```bash
# Install Yarn
npm install -g yarn

# Install dependencies
yarn

# Add a package
yarn add react-native-reanimated

# Run scripts
yarn ios
```

**Pros:**
- Faster than npm (parallel installs, offline cache)
- Very well-tested with React Native & Expo
- Historically the recommended choice for RN projects

**Cons:**
- Same flat `node_modules` as npm
- Yarn Classic is in maintenance mode (no new features)

---

### Yarn Modern (Berry / 3+)

Major rewrite of Yarn with Plug'n'Play (PnP) support.

```bash
# Enable Yarn Modern in a project
corepack enable
yarn set version stable

# Install
yarn

# Add a package
yarn add react-native-reanimated
```

**Pros:**
- PnP mode = no `node_modules`, instant installs
- Strict dependency resolution (no phantom deps)
- Excellent monorepo support

**Cons:**
- PnP mode **does not work** with React Native (Metro bundler expects `node_modules`)
- Must use `nodeLinker: node-modules` in `.yarnrc.yml` for RN/Expo
- More complex setup

> **If using Yarn Berry with React Native**, add this to `.yarnrc.yml`:
> ```yaml
> nodeLinker: node-modules
> ```

---

### pnpm

Fast, disk-efficient package manager using a content-addressable store with symlinks.

```bash
# Install pnpm
npm install -g pnpm

# Install dependencies
pnpm install

# Add a package
pnpm add react-native-reanimated

# Run scripts
pnpm run ios
```

**Pros:**
- Fastest install times
- Saves significant disk space (shared store across projects)
- Strict by default (prevents phantom dependencies)

**Cons:**
- React Native / Metro bundler has issues with symlinked `node_modules`
- Requires workaround: set `node-linker=hoisted` in `.npmrc`
- Some Expo packages may need patching

> **If using pnpm with React Native**, add this to `.npmrc`:
> ```ini
> node-linker=hoisted
> ```

---

## 2.4 Recommendation for React Native / Expo Projects

| Scenario | Recommended |
|----------|------------|
| New Expo project (beginner) | **npm** — zero config, just works |
| Team project, monorepo | **Yarn Classic** or **Yarn Berry** (with `nodeLinker: node-modules`) |
| Performance-focused, experienced dev | **pnpm** (with `node-linker=hoisted`) |
| Maximum compatibility, least headaches | **npm** or **Yarn Classic** |

### TL;DR

- **npm** → Safe default. Works everywhere. No extra setup.
- **Yarn Classic** → Battle-tested with RN. Slightly faster. Use if your team already uses it.
- **Yarn Berry** → Great for monorepos, but must disable PnP for RN.
- **pnpm** → Fastest & most efficient, but requires config tweaks for Metro compatibility.

---

## 2.5 Managing Node.js Versions

Use a version manager to switch Node versions per project:

### nvm (Node Version Manager)

```bash
# Install a specific version
nvm install 20

# Use it
nvm use 20

# Set default
nvm alias default 20

# Per-project: create .nvmrc
echo "20" > .nvmrc
nvm use  # reads .nvmrc
```

### Volta (recommended for teams)

```bash
# Install Node via Volta
volta install node@20

# Pin to project (writes to package.json)
volta pin node@20
volta pin yarn@1.22
```

Volta automatically switches versions when you `cd` into a project — no manual `nvm use` needed.

---

## Checklist

- [ ] Understand which Node.js version your Expo SDK / RN version requires
- [ ] Choose a package manager (npm, yarn, pnpm)
- [ ] Set up a Node version manager (nvm or Volta)
- [ ] Configure `.nvmrc` or Volta pin in your project
- [ ] If using pnpm: add `node-linker=hoisted` to `.npmrc`
- [ ] If using Yarn Berry: add `nodeLinker: node-modules` to `.yarnrc.yml`

---

## References

- [React Native External Dependencies Support](https://github.com/reactwg/react-native-releases/blob/main/docs/support.md)
- [Expo SDK Changelog](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/)
- [Expo SDK 53 Release Notes](https://expo.dev/changelog/sdk-53)
- [npm Docs](https://docs.npmjs.com/)
- [Yarn Docs](https://yarnpkg.com/)
- [pnpm Docs](https://pnpm.io/)
- [Volta](https://volta.sh/)
- [nvm](https://github.com/nvm-sh/nvm)
