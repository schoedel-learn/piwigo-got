# Self-Hosting and Fork Management Guide

This guide explains how to manage your self-hosted Piwigo instance, understand forking, and best practices for customization.

## Understanding Your Setup

### What is Self-Hosting?

Self-hosting means running Piwigo on your own infrastructure (like a Cloudron instance) rather than using piwigo.com's hosted service. This gives you:
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

## Connecting Self-Hosted Piwigo to Your Fork

### The Key Question: Should You?

**Short answer:** It depends on your goals.

### Scenario 1: Making Custom Changes

If you're customizing Piwigo for your own use:

✅ **DO:**
- Keep your self-hosted instance running the code you need
- Use your fork to track your customizations
- Document your changes for future reference

❌ **DON'T:**
- Try to "connect" your running instance to your fork in real-time
- Feel obligated to keep your fork in sync with upstream if you don't need updates

**Why:** Your self-hosted instance is a running application. Your fork is a code repository. They serve different purposes.

### Scenario 2: Contributing to Piwigo

If you want to contribute improvements back to Piwigo:

✅ **DO:**
- Keep your fork synced with upstream
- Create feature branches for each contribution
- Test changes thoroughly before submitting Pull Requests
- Follow the [CONTRIBUTING.md](CONTRIBUTING.md) guide

❌ **DON'T:**
- Mix your custom changes with contributions
- Make contributions from your main branch

## Best Practices for Cloudron Deployments

### Understanding Cloudron

Cloudron is a platform that simplifies application deployment. For Piwigo on Cloudron:

1. **Cloudron manages the installation** - It handles the web server, PHP, database, etc.
2. **You control the Piwigo code** - But Cloudron may have its own update mechanism
3. **Customization considerations** - Cloudron updates might overwrite custom changes

### Recommended Workflow for Cloudron

#### Option A: Use Cloudron's Standard Piwigo (Recommended for Most Users)

```
Cloudron → Standard Piwigo Installation → Your customization via plugins/themes
```

**Best for:** Users who want stability and easy updates

**How to customize:**
- Use Piwigo's plugin system for functionality changes
- Use themes for visual customization
- Avoid modifying core files

#### Option B: Deploy Your Fork to Cloudron (Advanced)

```
Your Fork → Build/Test → Deploy to Cloudron manually
```

**Best for:** Developers making core modifications

**Steps:**
1. Fork the Piwigo repository on GitHub
2. Clone your fork locally: `git clone https://github.com/YOUR-USERNAME/Piwigo`
3. Create a custom branch: `git checkout -b custom-changes`
4. Make your changes and test thoroughly
5. Deploy your customized version to Cloudron manually
6. Document your deployment process

**Caution:** This approach:
- Requires manual updates
- May conflict with Cloudron's update mechanism
- Requires technical expertise

#### Option C: Fork After Deployment (Development Workflow)

```
Cloudron Instance (running) → Fork for Development → Test Changes → Deploy to Cloudron
```

**Best for:** Users who already have a running Cloudron instance and want to develop customizations

**Your Situation:**
If you've already deployed Piwigo on Cloudron and then created a fork to develop customizations (perhaps using Got Photo or similar as inspiration), here's your workflow:

**Steps:**

1. **Your fork is your development environment:**
   ```bash
   # Clone your fork locally for development
   git clone https://github.com/YOUR-USERNAME/piwigo-got
   cd piwigo-got
   
   # Add upstream Piwigo for updates (optional)
   git remote add upstream https://github.com/Piwigo/Piwigo
   ```

2. **Develop customizations in your fork:**
   ```bash
   # Create a branch for your client customizations
   git checkout -b client-customizations
   
   # Make your changes
   # ... edit files, add features, customize for client needs ...
   
   # Commit your changes
   git add .
   git commit -m "Add custom feature for client"
   git push origin client-customizations
   ```

3. **Test locally before deploying:**
   - Set up a local development environment (LAMP/LEMP stack)
   - Test your changes thoroughly
   - Document what you changed and why

4. **Deploy to your Cloudron instance:**
   - Option A: Use SFTP/SCP to copy modified files to your Cloudron instance
   - Option B: Use git on your Cloudron server (if you have SSH access)
   - Option C: Create a custom Cloudron app package with your changes

5. **Keep a deployment log:**
   - Document which files you deployed and when
   - Keep notes on any Cloudron-specific configurations
   - Track any issues that arise

**Important Notes:**
- Your fork on GitHub is separate from your running Cloudron instance
- Changes in your fork won't automatically appear on your Cloudron site
- You manually deploy changes from your fork to Cloudron
- Cloudron may overwrite your changes during updates - disable auto-updates or redeploy after updates
- Consider using plugins/themes where possible to minimize core file changes

**Using Got Photo as a Model:**
If you're using Got Photo or another Piwigo installation as inspiration:
1. Study the features you want to replicate
2. Implement similar functionality in your fork
3. Test in a local environment first
4. Deploy tested changes to your Cloudron instance
5. Document your customizations for future reference

## Managing Your Fork

### Initial Setup

```bash
# Clone your fork
git clone https://github.com/YOUR-USERNAME/Piwigo
cd Piwigo

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
git checkout -b custom-cloudron

# Make your changes
# ... edit files ...

# Commit your changes
git add .
git commit -m "Custom: Add Cloudron-specific configuration"

# Push to your fork
git push origin custom-cloudron
```

### Maintaining Both Custom and Upstream

```bash
# Keep master clean (synced with upstream)
git checkout master
git fetch upstream
git merge upstream/master
git push origin master

# Rebase your custom changes on top of latest upstream
git checkout custom-cloudron
git rebase master

# If conflicts occur, resolve them and continue
# git add <resolved-files>
# git rebase --continue

# Force push your rebased custom branch
git push origin custom-cloudron --force
```

## Decision Matrix

| Your Goal | Use Fork? | Keep Synced? | Deploy to Cloudron |
|-----------|-----------|--------------|-------------------|
| Use Piwigo as-is | Optional | Optional | Use Cloudron's version |
| Customize via plugins/themes | Optional | Optional | Use Cloudron's version |
| Develop custom features for client | Yes | Optional | Manual deployment from fork |
| Make small core modifications | Yes | If you want updates | Deploy your fork manually |
| Contribute to Piwigo | Yes | Yes | Use Cloudron's version for production |
| Learn/experiment | Yes | Optional | Either way |

## Common Scenarios

### Scenario: "I already deployed Piwigo on Cloudron, then forked it for customization"

**This is your situation!** Here's what you should know:

**Your Setup:**
- Cloudron instance: Your production/client-facing site (running)
- GitHub fork: Your development workspace for creating customizations

**Recommended Workflow:**
1. Use your fork as the development environment
2. Test changes locally before deploying
3. Manually deploy tested changes to Cloudron
4. Document your customizations
5. Consider building features as plugins when possible to minimize core changes

**Key Understanding:**
- Your fork is NOT connected to your Cloudron instance
- The fork is where you develop and track code changes
- You manually transfer changes from fork to Cloudron
- Your fork serves as version control and backup of your customizations

**Next Steps:**
1. Clone your fork locally: `git clone https://github.com/YOUR-USERNAME/piwigo-got`
2. Set up a local development environment to test changes
3. Create a branch for your customizations: `git checkout -b client-project`
4. Develop features inspired by Got Photo or other sources
5. Test locally, then deploy to Cloudron when ready

### Scenario: "I just want to use Piwigo with some visual changes"

**Recommendation:** Don't use a fork for your production site.
- Install Piwigo via Cloudron
- Create a custom theme or use theme customization options
- Fork only if you want to experiment with changes

### Scenario: "I want to add a feature that doesn't exist"

**Recommendation:** 
1. Check if you can build it as a plugin
2. If it requires core changes:
   - Fork the repository
   - Create a feature branch
   - Develop and test your feature
   - Consider contributing it back via Pull Request
   - For production, evaluate if the benefit justifies manual deployment

### Scenario: "I forked Piwigo but now I'm confused"

**Don't worry!** Your fork is just a copy on GitHub. Your Cloudron installation is separate.
- Your fork doesn't affect your running Piwigo instance
- You can delete your fork without affecting your site
- You can keep your fork as a backup or for experiments

## Version Control Strategies

### Strategy 1: Two Branches (Simple)

```
master (synced with upstream)
└── custom-cloudron (your modifications)
```

### Strategy 2: Multiple Feature Branches (Advanced)

```
master (synced with upstream)
├── custom-cloudron-base (base customizations)
├── feature-new-gallery (specific feature)
└── bugfix-image-resize (specific fix)
```

### Strategy 3: Fork for Learning Only

```
master (synced with upstream)
└── experiment/* (various experimental branches)
```

## Troubleshooting

### "I made changes on Cloudron, but they're not in my fork"

Your fork is on GitHub. Your Cloudron instance is separate. To sync:
1. SSH into your Cloudron instance (if possible)
2. Copy the modified files
3. Apply them to your local fork clone
4. Commit and push to your fork

### "Cloudron updated Piwigo and my changes are gone"

This is expected. Cloudron manages updates. Solutions:
- Use plugins/themes instead of core modifications
- Deploy your own fork manually (disabling Cloudron auto-updates)
- Re-apply changes after each Cloudron update (not recommended)

### "I want to contribute but I have custom changes"

Keep them separate:
```bash
# Your custom branch
git checkout custom-cloudron

# Create a contribution branch from clean master
git checkout master
git pull upstream master
git checkout -b feature-contribution
# Make your contribution here
```

### "How do I use Got Photo or another installation as a model?"

When developing customizations inspired by another Piwigo installation:

1. **Study the features you want:**
   - Visit the Got Photo site or other reference installation
   - Document the features/functionality you want to replicate
   - Take screenshots or notes about the user experience

2. **Research the implementation:**
   - Check if the features are from plugins (easier to identify and potentially reuse)
   - Look for similar plugins in the Piwigo plugin repository
   - Decide if you need custom code or can use/modify existing plugins

3. **Develop in your fork:**
   - Create a feature branch: `git checkout -b feature-got-photo-gallery`
   - Implement the functionality
   - Test thoroughly in your local environment

4. **Deploy incrementally:**
   - Deploy one feature at a time to your Cloudron instance
   - Test each feature in production before adding the next
   - Keep detailed notes on what you deployed

5. **Maintain your customizations:**
   - Keep a README or CHANGELOG in your fork documenting what you've customized
   - Note which features are inspired by Got Photo vs original work
   - Document any dependencies or special configurations

## Resources

- [Official Piwigo Documentation](https://piwigo.org/doc)
- [Piwigo Forum](https://piwigo.org/forum)
- [Contributing Guide](CONTRIBUTING.md)
- [Plugin Development](https://piwigo.org/doc/doku.php?id=dev:plugins)
- [GitHub Forking Guide](https://docs.github.com/en/get-started/quickstart/fork-a-repo)
- [Cloudron Documentation](https://docs.cloudron.io/)

## Summary

**Key Takeaways:**

1. **Your self-hosted instance and your fork are separate** - One is a running application, the other is source code on GitHub
2. **You don't need to "connect" them** - They serve different purposes
3. **Common workflow: Deploy first, fork later** - If you deployed Piwigo on Cloudron first and then forked for customization, use your fork as a development workspace and manually deploy changes to Cloudron
4. **For most users:** Use Cloudron's standard Piwigo + plugins/themes
5. **For client customizations:** Fork for development, test locally, deploy manually to Cloudron
6. **For contributors:** Keep your fork synced with upstream and use feature branches
7. **Documentation is your friend** - Document your customizations and deployment process
8. **Using reference models (like Got Photo):** Study features, implement in your fork, test, then deploy

**Your Specific Workflow (Cloudron → Fork → Customize):**
- Your Cloudron instance is your production environment (running for client)
- Your fork is your development environment (for creating customizations)
- You develop in the fork, test locally, then manually deploy to Cloudron
- This separation keeps your production stable while you experiment and develop

Remember: There's no single "right" way. Choose the approach that matches your technical skills, time availability, and customization needs.
