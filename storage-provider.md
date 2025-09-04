You are an expert .NET architect.  
Generate a .NET Standard 2.0 solution for a pluggable cloud storage library.  

## Requirements

1. Define a common interface `ICloudStorageProvider` in `CloudStorage.Abstractions` with:
   - Task<string> UploadFileAsync(string localPath, string remotePath);
   - Task DownloadFileAsync(string remotePath, string localPath);
   - Task<bool> DeleteFileAsync(string remotePath);
   - Task<bool> FileExistsAsync(string remotePath);

2. Implement providers in separate projects:
   - Google Drive (OAuth2 or Service Account)
   - Azure Blob Storage
   - Amazon S3 (works also for MinIO since it is S3 compatible)
   - FTP (basic username/password auth)
   - Local File Storage (writes/reads from a local folder)

3. Each provider must:
   - Implement `ICloudStorageProvider`
   - Use async/await best practices
   - Return meaningful error messages
   - Support configuration via constructor (no hardcoded secrets)
   - Be in its own project so it can be published as a NuGet package

4. Project structure:
```

CloudStorage/
├── CloudStorage.Abstractions/      # Interfaces
├── CloudStorage.GoogleDrive/       # Google Drive impl
├── CloudStorage.AzureBlob/         # Azure Blob impl
├── CloudStorage.S3/                # Amazon S3 & MinIO impl
├── CloudStorage.Ftp/               # FTP impl
├── CloudStorage.Local/             # Local file system impl
└── Samples/                        # Example console app

````

5. Provide `csproj` files for each project with correct dependencies.

6. Provide **full implementation** for at least two providers:
- Local File Storage  
- FTP  

For the others, provide stubs or partial code if too long.

7. Include a **console app** in `Samples` that demonstrates usage with different providers:
```csharp
ICloudStorageProvider storage = new LocalStorageProvider("C:\\MyAppData");
await storage.UploadFileAsync("C:\\data\\report.pdf", "uploads/report.pdf");

ICloudStorageProvider ftp = new FtpStorageProvider("ftp://server", "user", "pass");
await ftp.UploadFileAsync("C:\\data\\report.pdf", "/remote/report.pdf");
````

8. Best practices:

   * No hardcoded secrets
   * Credentials from JSON/config/env vars
   * Exceptions wrapped in custom storage exceptions if needed
   * Follow .NET naming conventions

## Output

* Provide the full solution folder structure
* Include `csproj` definitions
* Show full implementation of FTP and Local providers
* Provide stubs or partial code for Google Drive, Azure Blob, and S3/MinIO
* Include a sample console program demonstrating swapping providers

```

---

✅ Now your library supports:  
- **Google Drive**  
- **Azure Blob**  
- **Amazon S3/MinIO**  
- **FTP**  
- **Local file system**  

This makes it a **universal abstraction layer** for almost any storage need 🚀.  
