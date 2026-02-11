# Windows LibreOffice Docker

Docker images for running LibreOffice on Windows containers with .NET 10 runtime.

## Overview

This repository provides Windows container images that include:
- **LibreOffice** - Full LibreOffice suite for document processing
- **.NET 10.0.3 Runtime** - Latest .NET runtime for running .NET applications
- **Windows Server Base Images** - Choice of Nano Server or Windows Server Core

Perfect for server-side document conversion, generation, and processing in a containerized Windows environment.

## Available Images

Images are available on Docker Hub: [`surgicalcoder/dotnet-libreoffice`](https://hub.docker.com/r/surgicalcoder/dotnet-libreoffice)

### Variants

| Tag | Base Image | Size | Use Case |
|-----|------------|------|----------|
| `nanoserver-ltsc2022` | Nano Server LTSC 2022 | Smallest | Minimal footprint, headless operations |
| `nanoserver-ltsc2025` | Nano Server LTSC 2025 | Smallest | Latest Nano Server, minimal footprint |
| `windowsservercore-ltsc2022` | Windows Server Core LTSC 2022 | Larger | Full compatibility, GUI components |
| `windowsservercore-ltsc2025` | Windows Server Core LTSC 2025 | Larger | Latest Server Core, full compatibility |

## Usage

### Pull an Image

```powershell
docker pull surgicalcoder/dotnet-libreoffice:windowsservercore-ltsc2025
```

### Run a Container

```powershell
docker run -it surgicalcoder/dotnet-libreoffice:windowsservercore-ltsc2025
```

### Convert a Document

```powershell
# Mount a volume with your documents
docker run -v C:\Documents:C:\docs surgicalcoder/dotnet-libreoffice:windowsservercore-ltsc2025 `
  C:\apps\libreoffice\libreoffice\program\soffice.exe --headless --convert-to pdf C:\docs\input.docx --outdir C:\docs
```

### Use in a Dockerfile

```dockerfile
FROM surgicalcoder/dotnet-libreoffice:windowsservercore-ltsc2025

WORKDIR /app
COPY MyApp/ .

# Your application that uses LibreOffice
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

## Environment Variables

The following environment variables are set in all images:

- `LIBREOFFICE_PATH` - Full path to soffice.exe: `C:\apps\libreoffice\libreoffice\program\soffice.exe`
- `PATH` - Includes LibreOffice program directory for direct command access

## LibreOffice Location

LibreOffice is installed at: `C:\apps\libreoffice\`

Executable location: `C:\apps\libreoffice\libreoffice\program\soffice.exe`

## Common LibreOffice Commands

### Convert Documents

```powershell
# Convert DOCX to PDF
soffice.exe --headless --convert-to pdf document.docx

# Convert with output directory
soffice.exe --headless --convert-to pdf document.docx --outdir C:\output

# Convert multiple files
soffice.exe --headless --convert-to pdf *.docx --outdir C:\output

# Convert to different formats
soffice.exe --headless --convert-to html document.docx
soffice.exe --headless --convert-to odt document.docx
```

### Batch Processing

```powershell
# Process all Word documents in a directory
Get-ChildItem C:\input\*.docx | ForEach-Object {
  docker run -v C:\input:C:\input -v C:\output:C:\output `
    surgicalcoder/dotnet-libreoffice:windowsservercore-ltsc2025 `
    soffice.exe --headless --convert-to pdf $_.FullName --outdir C:\output
}
```

## Building from Source

### Prerequisites

- Windows with Docker Desktop configured for Windows containers
- Git
- LibreOffice installation source files in `source/` directory

### Build Steps

```powershell
# Clone the repository
git clone https://github.com/yourusername/WindowsLibreOfficeDocker.git
cd WindowsLibreOfficeDocker

# Place LibreOffice files in source/ directory
# Ensure source/libreoffice/ contains the LibreOffice installation

# Build a specific variant
docker build -f docker/base/Dockerfile.windowsservercore-ltsc2025 `
  --build-arg DOTNET_VERSION=10.0.3 `
  -t dotnet-libreoffice:windowsservercore-ltsc2025 `
  build-context
```

### Automated Builds

This repository includes GitHub Actions workflows that automatically build and publish all image variants when changes are pushed to the `main` branch.

## Project Structure

```
WindowsLibreOfficeDocker/
├── .github/
│   └── workflows/
│       └── build-docker.yml          # CI/CD pipeline
├── docker/
│   └── base/
│       ├── Dockerfile.nanoserver-ltsc2022
│       ├── Dockerfile.nanoserver-ltsc2025
│       ├── Dockerfile.windowsservercore-ltsc2022
│       └── Dockerfile.windowsservercore-ltsc2025
├── source/
│   └── libreoffice/                  # LibreOffice installation files
│       ├── program/
│       ├── share/
│       └── presets/
├── .dockerignore                     # Build context optimization
└── README.md
```

## System Requirements

### For Running Containers

- Windows Server 2022 or Windows 11 (for ltsc2022 images)
- Windows Server 2025 or compatible (for ltsc2025 images)
- Docker Desktop or Docker Engine with Windows container support
- Minimum 4 GB RAM recommended
- Sufficient disk space for image (~2-4 GB per variant)

### For Building Images

- Windows with Docker Desktop
- GitHub Actions runner: `windows-2025`
- Source LibreOffice files in `source/` directory

## Performance Notes

- **Nano Server** variants are smaller and start faster but have limited Windows API support
- **Windows Server Core** variants are larger but provide full Windows compatibility
- First container start may be slower; subsequent starts use cached layers
- Document conversion is CPU-intensive; allocate sufficient resources

## Troubleshooting

### LibreOffice fails to start

Ensure you're using the correct base image for your Windows version. Windows containers must match the host OS version.

### File access denied

Make sure mounted volumes have proper permissions. Use absolute paths when mounting volumes.

### Out of memory errors

Increase Docker memory limits in Docker Desktop settings. LibreOffice document processing can be memory-intensive.

## License

See [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please submit issues and pull requests on GitHub.

## Support

For issues related to:
- **Docker images**: Open an issue in this repository
- **LibreOffice functionality**: Consult [LibreOffice documentation](https://documentation.libreoffice.org/)
- **.NET runtime**: See [.NET documentation](https://docs.microsoft.com/dotnet/)

## Changelog

### v1.0.0
- Initial release with .NET 10.0.3
- Support for Nano Server and Windows Server Core
- LTSC 2022 and LTSC 2025 variants
- Optimized Docker layers for faster builds
- Automated CI/CD with GitHub Actions