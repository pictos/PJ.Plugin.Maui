# Title

## CI and releases

Replace the `LIB_CSPROJ`, `SAMPLE_CSPROJ`, and `PACKAGE_NAME` placeholders in
`.github/workflows/` with your library project path, sample project path, and NuGet
package ID. These are templates: this repository does not contain projects to build.

The workflows use .NET SDK 10.0.402 and MAUI workload set 10.0.402.1, with MAUI
10.0.110 configured in `Directory.Build.props`. The macOS workflow uses Xcode 27.1
on GitHub's `xcode-27` runner, which is currently a preview image. Keep the SDK,
workload set, MAUI package, and Xcode versions compatible when updating them; see
the [.NET Apple SDK release notes](https://github.com/dotnet/macios/releases/tag/dotnet-10.0.1xx-xcode27.1-10018)
and [runner image inventory](https://github.com/actions/runner-images/blob/main/images/macos/xcode-27-arm64-Readme.md).
Sample apps targeting iOS or Mac Catalyst must adopt the UIScene lifecycle for
Xcode 27, as described in those release notes.

### NuGet trusted publishing

Releases use [NuGet trusted publishing](https://learn.microsoft.com/en-us/nuget/nuget-org/trusted-publishing)
instead of a stored, long-lived API key.

1. On nuget.org, create a trusted publishing policy for the account or organization
   that owns your packages. Set the repository owner and repository name to your
   repository, and set **Workflow File** to `release-nuget.yml` (filename only).
   Leave **Environment** empty, since the workflow does not declare one. Scope
   the policy to your package IDs and allow the required publishing operations.
2. Add the GitHub Actions repository secret `NUGET_USER` containing the nuget.org
   username (profile name, not email) authorized by that policy. The old
   `NUGET_KEY` secret is no longer used.
3. Push a version tag such as `v1.2.3` whose commit is already on `main`.
   The release workflow packs the configured library and authenticates with
   `NuGet/login@v1` immediately before publishing. GitHub's OIDC token is exchanged
   for a short-lived API key; no long-lived NuGet API key is stored in GitHub.

The release job requires `contents: read` and `id-token: write`. It produces
`.nupkg` and `.snupkg` files; pushing the primary package also uploads the matching
symbol package to nuget.org.

## Support

This project is open source and maintained by one person. If you need urgent fixes or custom features, you can support the development through [github sponsor](https://github.com/sponsors/pictos/sponsorships?sponsor=pictos&tier_id=485056&preview=false).
