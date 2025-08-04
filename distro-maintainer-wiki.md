# KNewStuff HTTP/2 Compatibility Issues - Distribution Maintainer Guide

## Overview
This guide helps distribution maintainers address HTTP/2 compatibility issues affecting KNewStuff functionality in KDE applications, particularly with store.kde.org and related content delivery services.

## Problem Summary
KDE's "Get Hot New Stuff" (KNewStuff) framework experiences connection failures when using HTTP/2 with certain content delivery networks, specifically:
- store.kde.org
- api.kde-look.org  
- api.opendesktop.org
- api.pling.com and related services

## Impact on Users
- **Broken "Get New..." buttons** in System Settings
- **Failed wallpaper downloads** in Desktop settings
- **Non-functional theme downloads** in Plasma customization
- **Discover app content issues** for KDE content
- **Widget/applet installation failures**

## Technical Root Cause
The affected servers send RST_STREAM frames when handling HTTP/2 connections from Qt applications, causing immediate connection termination. HTTP/1.1 connections work correctly.

## Solutions for Distributors

### 1. Patch-Based Solution (Recommended)
Apply patches to disable HTTP/2 for problematic hosts:

#### Packages to Patch
- **kf6-knewstuff** - Core KNewStuff framework
- **plasma-discover** - App store backend  
- **plasma-workspace** - Wallpaper/theme downloaders
- **plasma-desktop** - Desktop customization tools

#### Patch Locations
```bash
# KNewStuff core patches
SOURCES/0001-knewstuff-disable-http2.patch

# Discover patches  
SOURCES/0001-discover-disable-http2.patch

# Plasma Workspace patches
SOURCES/0001-plasma-workspace-disable-http2.patch
```

### 2. Configuration File Updates
Update .knsrc files to include HTTP/2 fallback configuration:

#### Files to Modify
```bash
/usr/share/knsrcfiles/wallpaper.knsrc
/usr/share/knsrcfiles/lookandfeel.knsrc
/usr/share/knsrcfiles/*.knsrc  # All KNewStuff configuration files
```

#### Configuration Additions
```ini
# HTTP/2 fallback configuration
DisableHTTP2=true
DisableHTTP2Hosts=store.kde.org,api.kde-look.org,api.opendesktop.org,api.pling.com,www.pling.com,cdn.kde.org

# Enhanced timeout settings
Timeout=30000
MaxRetries=3
RetryDelay=2000
```

### 3. Environment Variable Solution
For quick deployment, set system-wide environment variable:
```bash
# In /etc/environment or distribution equivalent
KNEWSTUFF_DISABLE_HTTP2=1
```

### 4. KF6 Content Fallback
Many store.kde.org packages only have KF5 tags but work with KF6. Enable automatic fallback:

```ini
# In .knsrc files
CategoryFallbacks=KDE Plasma 6 Add-Ons:KDE Plasma 5 Add-Ons,Plasma 6 Add-Ons:Plasma 5 Add-Ons
```

## Package Dependencies
Ensure proper version dependencies when applying patches:

```spec
# RPM example
BuildRequires: kf6-knewstuff-devel >= 6.0.0
Requires: qt6-qtnetwork >= 6.2.0

# Debian example  
Build-Depends: libkf6newstuff-dev (>= 6.0.0)
Depends: libqt6network6 (>= 6.2.0)
```

## Testing Verification

### 1. Basic Functionality Test
```bash
# Test wallpaper downloading
plasma-apply-wallpaperimage --help
# Should not show HTTP/2 errors in logs

# Test theme downloading via GUI
systemsettings kcm_desktoptheme
# "Get New..." should work without connection errors
```

### 2. Debug Mode Testing
```bash
# Enable KNewStuff debugging
export QT_LOGGING_RULES="kf.newstuff.core.debug=true"
systemsettings

# Check for HTTP/2 disabled messages in logs
journalctl --user -f | grep -i "disabling http"
```

### 3. Network Verification
```bash
# Test store.kde.org connectivity
curl -v https://store.kde.org/api/v1/providers.xml
# Should return XML content without errors
```

## Upstream Status

### Patches Submitted
- **KNewStuff**: Comprehensive HTTP/2 fallback patch submitted
- **Plasma Desktop**: .knsrc file updates in review
- **Infrastructure**: Bug filed against store.kde.org

### Expected Timeline
- **Short-term**: Client-side patches (distributions)
- **Medium-term**: Upstream patches merged  
- **Long-term**: Server-side fixes deployed

## Distribution-Specific Notes

### Fedora/RHEL/CentOS
```spec
# In plasma-workspace.spec
Patch1001: 0001-plasma-workspace-disable-http2.patch

%prep
%autosetup -p1
```

### Debian/Ubuntu
```debian
# In debian/patches/series
0001-knewstuff-disable-http2.patch
0001-discover-disable-http2.patch
```

### Arch Linux
```bash
# In PKGBUILD
prepare() {
  cd $srcdir/$pkgname-$pkgver
  patch -p1 < ../0001-knewstuff-disable-http2.patch
}
```

### openSUSE
```spec
# In _service file or spec
Patch1: knewstuff-http2-fallback.patch
```

## User Communication
Inform users about the temporary nature of these patches:

### Release Notes Example
> **KDE Content Downloads Fixed**: Resolved issues with "Get New..." functionality in KDE applications. Wallpaper and theme downloads now work correctly. This is a temporary fix until upstream servers resolve HTTP/2 compatibility issues.

### Known Issues Documentation
- Note that HTTP/2 is temporarily disabled for KDE content downloads
- Explain that functionality is fully restored
- Mention that server-side fixes are in progress

## Support and Troubleshooting

### Common Issues
1. **Still seeing connection errors**: Verify all packages are patched
2. **Slow downloads**: Expected with HTTP/1.1 fallback
3. **Missing content**: May need KF6→KF5 category fallback

### Debug Information Collection
```bash
# For bug reports, collect:
export QT_LOGGING_RULES="kf.newstuff.*=true"
# Run failing application and capture logs
```

### Contact Points
- **KDE Development**: kde-devel@kde.org
- **Distribution Issues**: Your distribution's KDE team
- **Upstream Bugs**: bugs.kde.org

## Future Considerations
- Monitor upstream patches for integration
- Plan removal of workarounds when servers are fixed  
- Consider user notification about temporary nature
- Test new KDE releases for continued necessity

### AI Attribution

Parts of the technical analysis, proposed solutions, patches and documentation were generated with the assistance of AI-based tooling (OpenAI GPT-4) under human review. All content has been verified and curated by human maintainers.

---
*Last Updated: January 2025*  
*Maintainers: KDE Distribution Teams*
