<h1 align="center">
    aijinsol's Tech Blog
</h1>

<p align="center">
    <img src="https://github.com/aijinsol/aijinsol.github.io/actions/workflows/deploy.yml/badge.svg?branch=main" alt="deploy" />
    <img src="https://img.shields.io/github/license/aijinsol/aijinsol.github.io?color=goldenrod" alt="license" /><br>
    <img src="https://img.shields.io/badge/Ruby-3.1.4-red?style=flat&logo=ruby" alt="ruby" />
    <img src="https://img.shields.io/badge/Node-20.9.0 LTS-green?style=flat&logo=nodedotjs" alt="node" /><br>
    <a href="https://aijinsol.github.io/" target="_blank" rel="noopener noreferrer">
        <img src="https://img.shields.io/badge/🏠-Click to VIEW Blog -brightgreen?style=for-the-badge" alt="go-to-blog" />
    </a>
</p>

> [!NOTE]  
> Theme: [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)

## 📖 How to Set Local Environment

### 🛠️ Prerequisites

- [rbenv](https://github.com/rbenv/rbenv)
- [nvm](https://github.com/nvm-sh/nvm)

### ✔️ Step 0. Git Clone with Submodules

> [!IMPORTANT]  
> Add `--recurse-submodules` option to git clone command

```bash
git clone --recurse-submodules https://github.com/aijinsol/aijinsol.github.io.git
```

### ✔️ Step 1. Install Node with [nvm](https://github.com/nvm-sh/nvm)

```bash
nvm install ${NodeVersion}
```

### ✔️ Step 2. Build with npm

Bundles code with Rollup in production mode using a [CommonJS config](./rollup.config.js)

```bash
npm install
```

```bash
npm run build
```

### ✔️ Step 3. Install Ruby with [rbenv](https://github.com/rbenv/rbenv)

```bash
rbenv install ${RubyVersion}
```

### ✔️ Step 4. Install Dependencies

```bash
bundle install
```

<br>

## 🚀 Run Server on Local

```bash
bundle exec jekyll s
```
