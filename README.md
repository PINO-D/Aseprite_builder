# Aseprite Automated Build Workflow
[中文版本](./README_CN.MD) | English

This is a GitHub Actions workflow that automatically builds and releases a specified version of Aseprite.

## Overview

This workflow allows you to automatically build Aseprite by specifying a version tag and generate executable files and release versions. Key features include:

- **Manual Build Trigger**: Manually input the Aseprite version to build via workflow_dispatch
- **Fetch Release Information**: Automatically retrieve release notes for that version from the official Aseprite repository
- **Compile Source Code**: Compile Aseprite using MSVC compiler and Ninja build system
- **Package Files**: Package the compiled executable and resource files into ZIP format
- **Create Release**: Create a corresponding release version in your repository and upload build artifacts

## How to Use

### Prerequisites

- A GitHub repository (to host the workflow file)
- Necessary permissions in the repository to create releases

### Starting a Build

1. Go to the **Actions** tab in your repository
2. Select **"Build a specified version"** workflow on the left sidebar
3. Click the **"Run workflow"** button
4. Enter the Aseprite version tag you want to build (e.g., `v1.2.29`)
5. Click **"Run workflow"** to confirm

### Monitoring Progress

While the workflow is running, you can:
- View the real-time execution of each step
- Check detailed log output
- Get error messages if any step fails

## Workflow Steps Explained

### 1. Fetch Release Information
```bash
Step Name: Get Release Body and Repository Name
Function: Retrieve release notes for the specified version from official Aseprite repository API
Outputs:
  - body-content: Release notes text
  - repo-name: Current repository name
```

### 2. Checkout Source Code
```bash
Step Name: Checkout Aseprite Source Code
Function: Clone the specified version of Aseprite source code and all submodules from the official repository
```

### 3. Setup Build Tools
```bash
Step Name: Setup Ninja Build System & Setup MSVC Compiler
Function:
  - Install Ninja build system
  - Configure MSVC compiler environment
```

### 4. Download Skia and Generate Build Configuration
```bash
Step Name: Download Skia and Generate Makefile
Function:
  - Download Skia graphics library (Windows Release x64 version)
  - Use CMake to generate Ninja build files
Configuration Parameters:
  - CMAKE_BUILD_TYPE: Release (optimized build)
  - LAF_BACKEND: skia (graphics backend)
  - Disable updater, OpenSSL, use Schannel for SSL/TLS
```

### 5. Compile
```bash
Step Name: Compile
Function: Compile Aseprite source code using Ninja
```

### 6. Package
```bash
Step Name: Package
Function:
  - Package aseprite.exe and data folder into a ZIP file
  - File naming format: aseprite.{version}.zip
```

### 7. Create Release
```bash
Step Name: Create Draft Release
Function:
  - Create a release for the corresponding version in your repository
  - Upload the packaged ZIP file
  - Release notes automatically filled with official version notes
  - Status: Draft (requires manual publishing)
```

## System Requirements

- **Operating System**: Windows (runner: windows-latest)
- **Disk Space**: At least 5GB (for source code, Skia library, and build files)
- **Network**: Must be able to access GitHub and download Skia library

## Important Notes

⚠️ **EULA License Compliance**

According to Aseprite's End User License Agreement (EULA), releases are set to **draft status**. This means:

- Draft releases are not visible to the public
- Manual publishing is required to make them public
- Private repositories can publish directly
- Public repositories should ensure compliance with license requirements

To publish, change `draft: true` to `draft: false` and ensure you have the legal right to do so.

## Output Files

After successful workflow completion, you will get:

- **aseprite.{version}.zip**: Contains the following
  - `aseprite.exe`: Executable program
  - `data/`: Resource folder (includes images, scripts, etc.)

## Troubleshooting

### Build Fails
- Verify that the input version tag is correct
- Check detailed logs to find the specific error
- Ensure network connectivity is working and can download Skia library

### Release Creation Fails
- Ensure `GITHUB_TOKEN` has sufficient permissions
- Check if a tag with the same name already exists
- Verify that the ZIP file was generated successfully

## Customization

### Change to Auto-Publish (Not Recommended)
Modify the release step:
```yaml
draft: false  # Change from true to false for automatic publishing
```

### Add Additional Files to Release
Add more files to the ZIP package in the packaging step.

### Support Other Operating Systems
Extend the matrix strategy to add macOS or Linux configurations.

## License

This workflow complies with Aseprite's official license terms. When using this workflow, ensure your usage complies with Aseprite's license requirements.

## References

- [Aseprite Official Repository](https://github.com/aseprite/aseprite)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [CMake Build Configuration](https://cmake.org/)
