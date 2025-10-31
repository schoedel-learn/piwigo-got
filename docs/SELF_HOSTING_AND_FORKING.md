# Self-Hosting and Fork Management Guide

This guide explains how to manage your self-hosted Piwigo instance, understand forking, and best practices for customization.

## Understanding Your Setup

### What is Self-Hosting?

Self-hosting means running Piwigo on your own infrastructure (VPS, dedicated server, etc.) rather than using piwigo.com's hosted service. This gives you:
- Full control over your data
- Ability to customize the code
- Choice of hosting environment
- No subscription fees

### What is a Fork?

A fork is your personal copy of the Piwigo repository on GitHub. When you fork Piwigo:
- You get a complete copy of the code in your GitHub account (e.g., `username/Piwigo`)
- You can make changes without affecting the original Piwigo repository
- You can pull updates from the original (upstream) repository
- You can optionally contribute changes back via Pull Requests

## Your Development Workflow

### The Key Question: Should You Connect Your Fork to Your Server?

**Short answer:** No, they serve different purposes.

### Understanding the Separation

- **Your fork on GitHub** = Your development workspace and version control
- **Your development environment** = Local setup on your machine for coding and testing
- **Your staging environment** = Test server that mirrors production for final validation
- **Your production server** (optional) = The live server for your client

**Why keep them separate:**
Your fork is where you develop and track code changes. Your environments (dev/staging/production) are where you run and test the application. You deploy code from your fork to your environments.

## Recommended Development Workflow

### Setup: Development Environment

This is where you'll spend most of your time coding and testing.

**Step 1: Clone Your Fork Locally**
```bash
# Clone your fork to your local machine
git clone https://github.com/YOUR-USERNAME/piwigo-got
cd piwigo-got

# Add upstream Piwigo for updates (optional)
git remote add upstream https://github.com/Piwigo/Piwigo
git remote -v
```

**Step 2: Set Up Local Development Environment**

You'll need a local web server environment. Common options:
- **XAMPP** (Windows/Mac/Linux)
- **MAMP** (Mac)
- **Local by Flywheel** (WordPress-focused but works for PHP apps)
- **Docker** (for containerized development)
- **Manual LAMP/LEMP stack** (Linux/Mac)

Basic requirements:
- Web server (Apache or nginx)
- PHP 7.4+
- MySQL 5+ or MariaDB
- ImageMagick or PHP GD

**Step 3: Configure Your Development Environment**
```bash
# In your local piwigo-got directory
# Install Piwigo through the web interface
# Access: http://localhost/piwigo-got (or your local URL)
# Follow the installation wizard
```

### Workflow: Making Changes

**Step 1: Create a Feature Branch**
```bash
# Create a branch for your client customizations
git checkout -b client-customizations

# Or create specific feature branches
git checkout -b feature-custom-gallery-layout
```

**Step 2: Develop in Your IDE**

Use your preferred IDE (Cursor, VS Code, PHPStorm, etc.):
- Edit files locally
- Test changes in your local environment
- Use Cursor's AI features or GitHub Copilot for assistance
- Commit changes frequently

**Step 3: Commit and Push Changes**
```bash
# Add your changes
git add .

# Commit with descriptive message
git commit -m "Add custom gallery layout for client"

# Push to your fork
git push origin client-customizations
```

**Step 4: Test Thoroughly**
- Test all new features in your local development environment
- Check different browsers
- Test edge cases
- Document any issues

### Setup: Staging Environment

Once development is far enough along, create a staging environment.

**What is Staging?**
A staging environment is a separate server that mirrors your production setup. It's where you do final testing before going live.

**Setting Up Staging:**

1. **Get a staging server** (can be on the same VPS as a subdomain):
   - Example: `staging.yourdomain.com` or `test.yourdomain.com`
   - Should have same server specs as production

2. **Deploy from your fork:**
   ```bash
   # On your staging server (via SSH)
   cd /path/to/web/root
   git clone https://github.com/YOUR-USERNAME/piwigo-got staging
   cd staging
   git checkout client-customizations
   
   # Set up database and configure
   # Access staging URL and complete installation
   ```

3. **Test in staging:**
   - Test all features in the staging environment
   - Share staging URL with client for feedback
   - Fix any issues found
   - Update your fork with fixes

### Deployment Workflow

```
1. Develop locally → 2. Commit to fork → 3. Deploy to staging → 4. Test → 5. Deploy to production
```

**Detailed Flow:**

1. **Local Development (Your Machine)**
   - Make changes in your IDE (Cursor, etc.)
   - Test locally at `http://localhost/piwigo-got`
   - Commit to your fork on GitHub

2. **Staging Deployment (Test Server)**
   ```bash
   # SSH into staging server
   ssh user@staging-server
   cd /path/to/staging
   
   # Pull latest changes
   git pull origin client-customizations
   
   # Clear any caches if needed
   ```

3. **Testing Phase**
   - Test on staging environment
   - Share with client for approval
   - Make adjustments as needed

4. **Production Deployment (when ready)**
   ```bash
   # SSH into production server
   ssh user@production-server
   cd /path/to/production
   
   # Pull tested changes
   git pull origin client-customizations
   
   # Clear caches, backup database, etc.
   ```

## Using Got Photo as a Model

When developing customizations inspired by Got Photo or another Piwigo installation:

### Research Phase
1. **Study the features you want:**
   - Visit the Got Photo site
   - Document features/functionality you want to replicate
   - Take screenshots or notes about UX

2. **Identify implementation approach:**
   - Check if features are from plugins (easier to identify)
   - Look for similar plugins in Piwigo plugin repository
   - Decide: custom code vs existing plugins

### Development Phase
3. **Implement in your fork:**
   ```bash
   # Create feature branch
   git checkout -b feature-got-photo-inspired-gallery
   
   # Develop the feature
   # Test locally
   
   # Commit and push
   git add .
   git commit -m "Implement Got Photo-inspired gallery view"
   git push origin feature-got-photo-inspired-gallery
   ```

4. **Test and iterate:**
   - Test locally first
   - Deploy to staging for further testing
   - Refine based on feedback

### Documentation
5. **Maintain documentation:**
   - Keep a README or CHANGELOG in your fork
   - Document what you've customized
   - Note which features are inspired by Got Photo
   - Document any dependencies or configurations

## Working with IDEs and GitHub

### Using Cursor IDE

**Cursor** is an AI-powered IDE that can significantly speed up development:

**Setup:**
1. Download and install Cursor from cursor.sh
2. Open your cloned fork: `File → Open Folder → /path/to/piwigo-got`
3. Cursor will index your codebase

**Features to Use:**
- **AI Chat (Cmd/Ctrl+L)**: Ask questions about Piwigo codebase
- **Inline Editing (Cmd/Ctrl+K)**: Generate or modify code inline
- **Terminal**: Access git commands without leaving the IDE
- **Multi-file Editing**: Make changes across multiple files simultaneously

**Example Workflow:**
```
1. Open Cursor → Open your piwigo-got folder
2. Use AI chat to ask: "How do I add a custom gallery view in Piwigo?"
3. Edit files with AI assistance
4. Use integrated terminal to commit changes
5. Push to GitHub
```

### Using GitHub Copilot

If you prefer working directly on GitHub or another IDE with Copilot:

**In VS Code or other IDEs:**
- Install GitHub Copilot extension
- Copilot suggests code as you type
- Use comments to guide Copilot: `// Create a function to fetch gallery images`

**On GitHub.com:**
- Use GitHub Codespaces for cloud-based development
- Edit files directly in the browser
- Commit changes through the web interface

### Git Workflow with IDEs

**Typical workflow in Cursor or VS Code:**

1. **Check current status:**
   - Use built-in Git panel (Source Control tab)
   - See modified files at a glance

2. **Make changes:**
   - Edit files using AI assistance
   - Save frequently

3. **Stage and commit:**
   ```bash
   # In integrated terminal
   git add .
   git commit -m "Add custom feature"
   ```
   
   Or use the IDE's GUI:
   - Click Source Control icon
   - Stage changes (+icon)
   - Write commit message
   - Click commit button

4. **Push to GitHub:**
   ```bash
   git push origin client-customizations
   ```

### Branch Management in IDEs

**Creating branches:**
- Bottom-left corner in VS Code/Cursor shows current branch
- Click to see all branches
- Click "+ Create new branch" to create feature branches

**Switching branches:**
```bash
# In terminal
git checkout feature-gallery-view

# Or use IDE's branch selector
```

## Managing Your Fork

### Initial Setup

```bash
# Clone your fork
git clone https://github.com/YOUR-USERNAME/piwigo-got
cd piwigo-got

# Add upstream remote
git remote add upstream https://github.com/Piwigo/Piwigo
git remote -v
```

### Keeping Your Fork Updated

```bash
# Fetch upstream changes
git fetch upstream

# Switch to your main branch
git checkout master

# Merge upstream changes
git merge upstream/master

# Push to your fork
git push origin master
```

### Working with Custom Changes

```bash
# Create a custom branch for your changes
# Create a custom branch for your work
git checkout -b client-customizations

# Make your changes
# ... edit files ...

# Commit your changes
git add .
git commit -m "Add custom feature for client"

# Push to your fork
git push origin client-customizations
```

### Maintaining Both Custom and Upstream

```bash
# Keep master clean (synced with upstream)
git checkout master
git fetch upstream
git merge upstream/master
git push origin master

# Rebase your custom changes on top of latest upstream
git checkout client-customizations
git rebase master

# If conflicts occur, resolve them and continue
# git add <resolved-files>
# git rebase --continue

# Force push your rebased custom branch
git push origin client-customizations --force
```

## Decision Matrix

| Your Goal | Use Fork? | Keep Synced? | Environments Needed |
|-----------|-----------|--------------|---------------------|
| Use Piwigo as-is | Optional | Optional | Just production |
| Customize via plugins/themes | Optional | Optional | Dev + Production |
| Develop custom features for client | Yes | Optional | Dev + Staging + Production |
| Make core modifications | Yes | If you want updates | Dev + Staging + Production |
| Contribute to Piwigo | Yes | Yes | Dev (staging optional) |
| Learn/experiment | Yes | Optional | Just dev |

## Common Scenarios

### Scenario: "I need to customize Piwigo for a client using Got Photo as inspiration"

**This is your situation!** Here's your workflow:

**Your Setup:**
- **Development environment**: Local machine with your IDE (Cursor)
- **Staging environment**: Test server for client review (set up when dev is far enough along)
- **GitHub fork**: Version control and backup
- **(Optional) Production**: Final deployment for client

**Recommended Workflow:**

1. **Set up development locally:**
   ```bash
   # Clone your fork
   git clone https://github.com/YOUR-USERNAME/piwigo-got
   cd piwigo-got
   
   # Set up local web server (XAMPP, MAMP, etc.)
   # Install Piwigo locally through web interface
   ```

2. **Develop features:**
   - Use Cursor IDE for coding with AI assistance
   - Create feature branches for each major feature
   - Study Got Photo for inspiration
   - Test changes locally as you develop

3. **Set up staging when ready:**
   - Deploy your fork to a staging server
   - Share with client for feedback
   - Iterate based on feedback

4. **Deploy to production when complete:**
   - Deploy tested code to production server
   - Document deployment process

**Key Points:**
- Your fork is NOT connected to any server - it's your code repository
- You develop locally using Cursor (or GitHub Copilot)
- You manually deploy from fork to staging/production
- Keep dev → staging → production workflow separate

### Scenario: "I just want to use Piwigo with some visual changes"

**Recommendation:** 
- Install Piwigo on your server
- Create a custom theme or use theme customization options
- Fork only if you want to experiment with changes or track your customizations

### Scenario: "I want to add a feature that doesn't exist"

**Recommendation:** 
1. Check if you can build it as a plugin (preferred method)
2. If it requires core changes:
   - Fork the repository
   - Create a feature branch
   - Develop and test locally
   - Deploy to staging for testing
   - Consider contributing it back via Pull Request

### Scenario: "I'm confused about fork vs server"

**Clarification:**
- **Your fork (GitHub)**: Source code repository, version control
- **Your dev environment (local)**: Where you code and test
- **Your staging environment (server)**: Where you test before going live
- **Your production (server)**: Client-facing live site

They're all separate. You edit code in fork, deploy code to servers.

## Version Control Strategies

### Strategy 1: Single Feature Branch (Simple - Recommended for Your Use Case)

```
master (synced with upstream)
└── client-customizations (all your custom work)
```

**Best for:** Client project with custom features
- Keep all client-specific customizations on one branch
- Easy to deploy: just pull this branch on staging/production
- Simple to maintain

### Strategy 2: Multiple Feature Branches (Advanced)

```
master (synced with upstream)
├── feature-custom-gallery (specific feature)
├── feature-got-photo-layout (specific feature)
└── feature-client-branding (specific feature)
```

**Best for:** Complex projects with many independent features
- Each feature on its own branch
- Can merge features into a deployment branch
- Easier to test features independently

### Strategy 3: Development Branch

```
master (synced with upstream)
└── develop (integration branch)
    ├── feature-gallery
    ├── feature-layout
    └── feature-search
```

**Best for:** Team projects or complex development
- Merge features into develop branch
- Deploy develop to staging
- Merge develop to master for production releases

## Troubleshooting

### "My changes on the dev server aren't showing up in my fork"

Your fork is on GitHub, your dev server is separate. To sync:
1. Make changes on your local machine (not directly on server)
2. Commit to your fork
3. Pull the changes on your dev server

**Correct workflow:**
```bash
# On your local machine
git add .
git commit -m "Add feature"
git push origin client-customizations

# On your dev/staging server
git pull origin client-customizations
```

### "I made changes directly on the server and need them in my fork"

If you edited files directly on a server:
```bash
# SSH into your server
ssh user@your-server
cd /path/to/piwigo

# Check what changed
git status
git diff

# Commit changes on server
git add .
git commit -m "Changes made on server"
git push origin client-customizations

# Pull changes to your local machine
# On local machine
git pull origin client-customizations
```

**Note:** Better practice is to develop locally, not on servers.

### "I want to contribute but I have custom changes"

Keep them separate:
```bash
# Your custom branch
git checkout client-customizations

# Create a contribution branch from clean master
git checkout master
git pull upstream master
git checkout -b feature-contribution
# Make your contribution here
# Submit PR from this branch
```

### "How do I sync my fork with Cursor or other IDE?"

Your IDE (Cursor, VS Code) works with your local clone:
1. **Open your fork locally** in Cursor: `File → Open → /path/to/piwigo-got`
2. **Make changes** using the IDE
3. **Commit** using IDE's Git panel or terminal
4. **Push** to GitHub (your fork updates automatically)

### "Should I work in Cursor or on GitHub?"

**Recommended:** Work in Cursor locally
- Faster development with AI assistance
- Full IDE features
- Test locally before committing
- Commit and push to GitHub when ready

**GitHub.com direct editing:**
- Good for quick documentation fixes
- Not ideal for code development
- Can't test changes before committing

### "How do I deploy from my fork to staging?"

**Method 1: Git Pull (Recommended)**
```bash
# SSH into staging server
ssh user@staging-server
cd /path/to/piwigo

# Pull from your fork
git remote add origin https://github.com/YOUR-USERNAME/piwigo-got
git pull origin client-customizations
```

**Method 2: SFTP/SCP**
```bash
# From your local machine
scp -r /path/to/modified/files user@staging-server:/path/to/piwigo/
```

**Method 3: Deployment Script**
Create a simple deployment script:
```bash
#!/bin/bash
# deploy-staging.sh
ssh user@staging-server << 'EOF'
cd /path/to/piwigo
git pull origin client-customizations
# Add any post-deployment commands
# php scripts/clear_cache.php
EOF
```

## Best Practices

### Documentation
- Keep a `CUSTOMIZATIONS.md` in your fork documenting what you've changed
- Note which features are inspired by Got Photo
- Document deployment steps specific to your setup

### Testing
- Always test locally first
- Deploy to staging for client review
- Only deploy to production after staging approval

### Backup
- Your fork on GitHub is your backup
- Consider keeping database backups separate
- Document your database schema changes if any

### Code Organization
- Use plugins where possible (easier to maintain)
- Keep customizations well-commented
- Follow Piwigo coding standards for easier maintenance

## Quick Reference

### Common Commands

```bash
# Clone your fork
git clone https://github.com/YOUR-USERNAME/piwigo-got

# Create feature branch
git checkout -b feature-name

# Save your work
git add .
git commit -m "Description"
git push origin feature-name

# Deploy to staging
ssh user@staging "cd /path/to/piwigo && git pull origin feature-name"

# Sync with upstream Piwigo
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

### Your Typical Day

1. Open Cursor IDE with your piwigo-got folder
2. Make changes to files
3. Test locally at `http://localhost/piwigo-got`
4. Commit changes: `git add . && git commit -m "message"`
5. Push to GitHub: `git push origin client-customizations`
6. When ready for client review: Deploy to staging
7. After approval: Deploy to production

## Resources

- [Official Piwigo Documentation](https://piwigo.org/doc)
- [Piwigo Forum](https://piwigo.org/forum)
- [Contributing Guide](CONTRIBUTING.md)
- [Plugin Development](https://piwigo.org/doc/doku.php?id=dev:plugins)
- [GitHub Forking Guide](https://docs.github.com/en/get-started/quickstart/fork-a-repo)
- [Cursor IDE](https://cursor.sh)
- [GitHub Copilot](https://github.com/features/copilot)

## Summary

**Key Takeaways:**

1. **Your fork and your servers are separate** - Fork = source code repository, Servers = running applications
2. **You don't need to "connect" them** - You deploy code from fork to servers
3. **Development workflow:** Develop locally → Test locally → Deploy to staging → Deploy to production
4. **Use the right tools:** Cursor/VS Code for local development, GitHub for version control, servers for deployment
5. **For client customizations:** 
   - Fork for version control
   - Develop locally with your IDE
   - Test locally first
   - Deploy to staging for client review
   - Deploy to production when approved
6. **For contributors:** Keep your fork synced with upstream and use separate branches for contributions
7. **Documentation is essential** - Document customizations, deployment steps, and inspiration sources
8. **Using reference models (like Got Photo):** Study features, implement locally, test in staging, deploy when ready

**Your Specific Workflow (No Cloudron):**

- **Local Development**: Clone fork → Edit in Cursor → Test at localhost
- **Staging Environment**: Set up when development is far enough along → Deploy for client review
- **Production**: Deploy when client approves
- **Version Control**: Your fork tracks all changes, serves as backup

**The Separation:**
- **Fork on GitHub** = Your code repository and version history
- **Local machine** = Where you develop and test with Cursor
- **Staging server** = Where client reviews your work
- **Production server** = Final deployment (optional, when ready)

Each serves a specific purpose. You work in Cursor locally, push to GitHub fork, deploy from fork to servers.

Remember: There's no single "right" way. Choose the approach that matches your technical skills, time availability, and customization needs.
