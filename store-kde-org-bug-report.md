# Bug Report: HTTP/2 Misconfiguration Causing RST_STREAM Errors

## Summary
store.kde.org and related CDN/API endpoints are experiencing HTTP/2 protocol issues that cause RST_STREAM errors, preventing KNewStuff from functioning properly across KDE applications.

## Affected Services
- store.kde.org
- api.kde-look.org  
- api.opendesktop.org
- api.pling.com
- www.pling.com
- cdn.kde.org

## Problem Description
When KNewStuff clients (including Plasma Discover, wallpaper downloaders, theme managers, etc.) attempt to connect using HTTP/2, the connections are terminated with RST_STREAM errors. This affects:

1. **Content Discovery**: Cannot retrieve lists of available content
2. **Downloads**: Cannot download wallpapers, themes, add-ons
3. **Metadata**: Cannot fetch content descriptions, screenshots, ratings
4. **User Experience**: Complete failure of "Get New..." functionality

## Technical Details

### Error Symptoms
```
QNetworkReply::NetworkError(ProtocolFailure) "The remote server refused the connection (the server is not accepting connections)"
RST_STREAM frame received with error code: 2 (INTERNAL_ERROR)
HTTP/2 connection terminated unexpectedly
```

### Network Analysis
- HTTP/1.1 connections work correctly
- HTTP/2 connections fail consistently  
- Issue appears to be server-side CDN/proxy configuration
- Affects all Qt/KDE applications using QNetworkAccessManager with HTTP/2

### Affected KDE Components
- plasma-discover (KNS backend)
- plasma-workspace (wallpaper downloader)
- plasma-desktop (theme management)
- All applications using KNewStuff framework
- Approximately affects 50+ KDE applications

## Reproduction Steps
1. Enable HTTP/2 in Qt application (default behavior)
2. Attempt to connect to any store.kde.org API endpoint
3. Connection fails with RST_STREAM error
4. Same request succeeds when forced to HTTP/1.1

## Workaround
Force HTTP/1.1 by setting:
```cpp
request.setAttribute(QNetworkRequest::HTTP2AllowedAttribute, false);
```

## Impact Assessment
- **Severity**: High - Complete feature failure
- **Scope**: All KDE users attempting to download content
- **User Experience**: Critical functionality broken
- **Distributions**: Affects all Linux distributions shipping KDE

## Proposed Solutions

### Server-Side (Preferred)
1. **Fix HTTP/2 Configuration**: Update CDN/proxy settings to properly handle HTTP/2
2. **Test HTTP/2 Compliance**: Verify with standard HTTP/2 clients
3. **Monitor Connection Health**: Add metrics for connection failures

### Client-Side (Temporary)
1. **Automatic Fallback**: Detect RST_STREAM and retry with HTTP/1.1
2. **Host-Based Disabling**: Maintain list of problematic hosts
3. **Configuration Option**: Allow users to disable HTTP/2

## Testing Recommendations
```bash
# Test HTTP/2 compliance
curl --http2 -v https://store.kde.org/api/v1/providers.xml

# Test with various HTTP/2 clients
nghttp -v https://store.kde.org/api/v1/providers.xml
h2load -n1 -c1 https://store.kde.org/api/v1/providers.xml
```

## Timeline
- **Issue Discovered**: August 2025
- **User Reports**: Multiple distributions affected
- **Workaround Deployed**: January 2025 (client-side patches)
- **Server Fix Needed**: High priority

## Contact Information
- **Reporter**: KDE Distribution Maintainers
- **Technical Contact**: KDE Infrastructure Team
- **Affected Projects**: KNewStuff, Plasma, Discover

## Additional Notes
This issue affects the entire KDE ecosystem's content delivery mechanism. A server-side fix is strongly preferred over forcing all client applications to implement HTTP/2 fallbacks.

The problem appears to be related to CDN configuration rather than the store.kde.org application itself, suggesting the issue may be in the infrastructure layer.

### AI Attribution

Parts of the technical analysis, proposed solutions, patches and documentation were generated with the assistance of AI-based tooling (OpenAI GPT-4) under human review. All content has been verified and curated by human maintainers.

## Related Issues
- Similar reports from other Qt-based applications
- HTTP/2 compliance issues with certain CDN configurations
- Impact on KDE's Get Hot New Stuff functionality ecosystem-wide
