# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Validation and Testing

### 1. Run Unit Tests

Execute the test suite for `Bookstore.Domain.Tests` to verify that domain logic behaves correctly after the migration.

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release
```

Review the test output for any failures or unexpected results. If tests fail, compare the test expectations against the migrated domain logic in `Bookstore.Domain`.

### 2. Build All Projects in Release Mode

Confirm that all projects build cleanly under the `Release` configuration, not just `Debug`.

```bash
dotnet build --configuration Release
```

Address any warnings that appear during this build, particularly those related to nullable reference types, deprecated APIs, or platform compatibility, as these can indicate latent issues.

### 3. Verify NuGet Package Compatibility

Check that all NuGet packages referenced across the solution are compatible with the target .NET version. Run the following to inspect outdated or vulnerable packages:

```bash
dotnet list package --outdated
dotnet list package --vulnerable
```

Update any packages that are outdated or have known vulnerabilities.

### 4. Run the Web Application Locally

Start the `Bookstore.Web` project and manually verify that core functionality works as expected.

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Test the following areas at minimum:
- Application startup and routing
- Data access through `Bookstore.Data` (database reads and writes)
- Any authentication or authorization flows if present

### 5. Verify Database Connectivity and Migrations

If the project uses Entity Framework Core, confirm that migrations are up to date and that the database schema is consistent with the current model.

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

If migrations are missing or out of sync, generate a new migration and apply it to a test database before targeting any production environment.

```bash
dotnet ef migrations add <MigrationName> --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

### 6. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Verify that the infrastructure definitions are still accurate for the target environment and that any environment-specific configuration values (connection strings, endpoints, secrets) are correctly set for the new platform.

### 7. Check Configuration Files

Review `appsettings.json` and any environment-specific variants (`appsettings.Development.json`, `appsettings.Production.json`) in `Bookstore.Web` to ensure that:
- Connection strings point to the correct databases
- Any legacy configuration keys specific to the old .NET Framework platform have been replaced with their .NET equivalents
- Logging configuration is appropriate

## Deployment

### 8. Publish the Application

Once validation is complete, publish the application to confirm the output is correct before deploying to a target environment.

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Inspect the `./publish` directory to confirm all expected files, static assets, and dependencies are present.

### 9. Smoke Test the Published Output

Run the published output directly to perform a final check before deploying to the target environment.

```bash
dotnet ./publish/Bookstore.Web.dll
```

Verify that the application starts without errors and that basic functionality is accessible.