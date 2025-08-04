# KDE Upstream Work - HTTP/2 Compatibility Fix

This repository contains a comprehensive submission package addressing HTTP/2 compatibility issues affecting KDE's "Get Hot New Stuff" (KNewStuff) framework, particularly with store.kde.org and related content delivery services.

## Problem Overview

KDE applications experienced connection failures when downloading themes, wallpapers, and other content through the "Get New..." functionality. The root cause was HTTP/2 protocol incompatibility between Qt applications and certain CDN configurations used by store.kde.org.

## Solution Summary

This package provides multiple approaches to resolve the HTTP/2 compatibility issues:

1. **Client-side patches** for automatic HTTP/2 fallback
2. **Configuration file updates** for KNewStuff resources
3. **Infrastructure documentation** for server-side fixes
4. **Distribution guidance** for package maintainers

## Repository Contents

### Core Submission Files

- **`submission-summary.md`** - Master coordination document and submission checklist
- **`store-kde-org-bug-report.md`** - Formal bug report for KDE infrastructure team
- **`distro-maintainer-wiki.md`** - Comprehensive guide for distribution maintainers
- **`knewstuff-comprehensive-fix.patch`** - Complete HTTP/2 fallback implementation

### Configuration Files

- **`wallpaper.knsrc`** - Updated wallpaper download configuration with HTTP/2 fallback
- **`lookandfeel.knsrc`** - Updated theme download configuration with fallback support

### Documentation

- **`ai_attribution.md`** - AI attribution template for transparency

## AI Attribution

### Important Notice

Parts of the technical analysis, proposed solutions, patches and documentation were generated with the assistance of AI-based tooling (OpenAI GPT-4) under human review. All content has been verified and curated by human maintainers.

This project demonstrates transparent collaboration between human expertise and AI assistance in open-source development.

## Submission Targets

### KDE Upstream Repositories

1. **KNewStuff Framework** - `knewstuff-comprehensive-fix.patch`
   - Target: https://invent.kde.org/frameworks/knewstuff
   - Branch: `feature/http2-fallback-compatibility`

2. **Plasma Desktop** - Configuration file updates
   - Target: https://invent.kde.org/plasma/plasma-desktop
   - Branch: `feature/knsrc-http2-compatibility`

### Infrastructure & Documentation

3. **KDE Infrastructure** - Bug report submission
   - Target: bugs.kde.org (Infrastructure category)
   - Priority: High - affects entire KDE ecosystem

4. **Community Wiki** - Distribution maintainer guide
   - Target: community.kde.org wiki
   - Audience: Linux distribution KDE teams

## Usage Instructions

### For KDE Developers

1. Review `submission-summary.md` for complete submission strategy
2. Apply `knewstuff-comprehensive-fix.patch` to KNewStuff repository
3. Update .knsrc files in plasma-desktop repository
4. Submit merge requests with references to this documentation

### For Distribution Maintainers

1. Consult `distro-maintainer-wiki.md` for distribution-specific guidance
2. Apply patches to affected packages:
   - kf6-knewstuff
   - plasma-discover
   - plasma-workspace
   - plasma-desktop
3. Update .knsrc configuration files as specified
4. Test "Get New..." functionality after applying fixes

### For Infrastructure Teams

1. Review `store-kde-org-bug-report.md` for technical analysis
2. Implement server-side HTTP/2 fixes as outlined
3. Coordinate with client-side workaround removal timeline

## Technical Details

### Root Cause Analysis

- Store.kde.org CDN sends RST_STREAM frames for HTTP/2 connections
- Qt applications fail with `QNetworkReply::NetworkError(ProtocolFailure)`
- HTTP/1.1 connections work correctly as fallback

### Solution Approach

- **Automatic Detection**: Identify problematic hosts (store.kde.org, api.kde-look.org, etc.)
- **Protocol Fallback**: Automatically retry failed connections with HTTP/1.1
- **Category Mapping**: Enable KF6→KF5 content discovery fallback
- **Environment Control**: `KNEWSTUFF_DISABLE_HTTP2` variable for system-wide control

### Affected Components

- plasma-discover (KNS backend)
- plasma-workspace (wallpaper downloader)
- plasma-desktop (theme management)
- All KNewStuff-enabled applications (~50+ applications)

## Testing Status

### Validation Completed

- ✅ Markdown rendering with `grip`
- ✅ Linting with `markdownlint` 
- ✅ Code fence syntax validation
- ✅ Heading hierarchy verification
- ✅ Link functionality testing

### Known Issues

- Long lines in some documentation files (technical content)
- Minor formatting inconsistencies resolved during submission preparation

## Project Timeline

- **Issue Discovery**: August 2025
- **Solution Development**: January 2025
- **AI-Assisted Analysis**: January 2025
- **Submission Preparation**: January 2025
- **Repository Creation**: January 2025

## Contributing

While this is a specific submission package, feedback on the approach and documentation is welcome:

1. Technical accuracy of the HTTP/2 analysis
2. Completeness of distribution-specific guidance
3. Clarity of submission documentation
4. Effectiveness of AI-human collaboration transparency

## License

This submission package is provided under the same licenses as the target KDE projects:
- Code patches: GPL-2.0+ (following KNewStuff licensing)
- Documentation: CC-BY-SA-4.0 (following KDE documentation standards)

## Contact

- **Primary Contact**: Distribution maintainer community
- **Technical Issues**: kde-devel@kde.org
- **Infrastructure**: KDE Infrastructure Team

---

*This README was prepared as part of comprehensive KDE upstream submission efforts with transparent AI collaboration.*
