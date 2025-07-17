# Orchard 2.1.7 Local Environment Setup on Windows - NuGet-based - No Admin Privilege Access

## Sections
1. Install Prerequisite Software
    - [With Admin Privileges](#1-install-prerequisite-software)
    - [With NO Admin Privileges](#1-install-prerequisite-software)
   
2. [Verify Installed Versions](#2-verify-installed-versions)
3. [Building and Running the Solution](#3-building-and-running-the-solution)

This guide provides step-by-step instructions to set up a local development environment for 
Orchard 2.1.7 on a Windows system using NuGet. Note that while the software can be installed without admin privileges,
certain steps, such as adding System Variables may require admin access.

## Tested Environment
The local environment has been tested with the following versions:
- .NET SDK v7.0.301
- NVM 1.2.2
- Node.js v14.17.0
- PNPM v7.15.0

## Development guidelines
 - The main branch of the repository is the `dev` branch. Always start your development branch from there and open your pull requests targeting this branch, unless instructed otherwise (e.g., it's part of a larger feature development). 
 - For smaller developments and bugfixes the branch name should be `issue/<issue-code>` (for Lombiq-developers, e.g. `issue/SANTA-123`) or `issue/<short-description>` (e.g., `issue/adding-sponsors-widget`). 
 - For a large features (that involves thorough testing and involves other "issue branches") the branch name should be `feature/<short-description>` (e.g., `feature/upgrade-to-1-8`). 
 - Force push is disabled.

# 1. Install Prerequisite Software
- ## With Admin Privileges
  - ### Requirements
    - [.NET SDK < v8.0](https://dotnet.microsoft.com/en-us/download)
    - [Node.js < v16.0](https://nodejs.org/en/download)

  - ### Installation Steps
    - Download and install Node.js Windows Installer (.msi) (https://nodejs.org/en/download)
    - Download and install .NET SDK < v.8.0  (dotnet-sdk.exe) (https://dotnet.microsoft.com/en-us/download)
    - Once Node.js installation has finished, verify system environment variables are set correctly. Open a command prompt and run the following commands:
        ```bash
        node -v
        npm -v
        ```
    - If the commands return the version numbers, Node.js and npm are installed correctly. Otherwise, you may need to add the Node.js installation path to your system environment variables.
    - After adding the system environment variables, enable PNPM running the following command in an elevated command prompt (run as administrator):
      ```bash
      - Corepack enable && corepack prepare pnpm@latest --activate (root access is required)

- ## With NO Admin Privileges
  - ### Requirements
    - [.NET SDK < v8.0](https://dotnet.microsoft.com/en-us/download)
    - [NVM (Node Version Manager) for Windows](https://github.com/coreybutler/nvm-windows/releases).
      - Node.js < v16.0 (install via NVM)
      - NPM < v8.0.0 (install via NVM)
      - PNPM < v8.0.0 (install via Corepack)

  - ### Installation Steps
    - **Ensure there are no existing Node.js or npm installations on your system.**  If you previously installed Node.js (especially in C:\Program Files), 
    uninstall it and remove any related paths from your system environment variables to avoid conflicts.
    - Download and install nvm (Node Version Manager) `nvm-setup.exe` from [nvm-windows releases](https://github.com/coreybutler/nvm-windows/releases).
    - During the installation process ensure that the nvm installation path aims at `C:\Users\$User\AppData\Roaming\nvm` and keep the same symlink!
     <br><br>
      - Install Node.js and npm using NVM:
        - Open a new command prompt (cmd) and navigate to the directory where you installed NVM, `C:\Users\$User\AppData\Roaming\nvm`.
        - Run the following command to install Node.js `< v16.0`:
          ```bash
          nvm install 14.17.0
          ```
        - Use the installed version:
          ```bash
          nvm use 14.17.0
          ```
       - This will also install the corresponding version of npm. 
         - Note: NVM allows you to manage multiple Node.js versions easily.
         - If you need to switch Node.js versions in the future, you can use `nvm use <version>`.
       - If npm is not installed automatically, you can install it by running:
         ```bash
         nvm install-latest-npm
         ```
    - **Enable PNPM running the following command**:
        ```bash
        corepack enable && corepack prepare pnpm@7.15.0 --activate
        ```
 
    - **Install .NET SDK**:
       - Download and install .NET SDK `< v8.0` from [Microsoft .NET](https://dotnet.microsoft.com/en-us/download).

## 2. Verify Installed Versions
- Run the following commands to confirm the correct versions are installed:
```bash
dotnet --version
node -v
pnpm -v
```

## 3.Setting the Database

- ### Option 1: Using the default Orchard Core Credentials for admin access
  - The repository is configured to automatically map to a local SQL Server instance through the `appsettings.Development.json` file.
  - In your local environment, ensure you have SQL Server Management Studio (SSMS) or any other SQL client installed.
    Open SSMS and connect to your local SQL Server instance using the following credentials, after it press `Connect`:
    - `Server Type:` Database Engine
    - `Server Name:`(localdb)\MSSQLLocalDB
    - `Authentication:` Windows Authentication`

    <br><br><br>

    ![img.png](img.png)
    - Once connected, create a new database named `santamonica.gov`(See Iron Mountain ScreenShots for naming conventions).

- ### Option 2: Using the Orchard Core Setup Database
  - Delete the `appsettings.Development.json` file in the `src/Orchard.Web` directory. This will force the application to use the default Orchard Core setup database.

## 4. Cloning, Building and Running the Project
- ### Cloning
  - Start cloning santamonica.gov.core Github repository to your local environment. From santamonica.gov.core repository,
    select `<> Code` dropdown and copy the HTTPS URL address. In your local environment, open a command prompt in the directory
    where you want to clone the repository and run the following command:
```bash
git clone https://github.com/CityofSantaMonica/santamonica.gov.core.git
```

- ### Building and Running
  -   ### Option 1: Build using VS IDE
- Open Visual Studio start window, under Get started select Clone a repository. Paste the previously copied git location under Repository Location field. Specify a path and then click on Clone.
- Navigate to the parent directory of the project and build/run it. The application will be hosted on:
  - `https://localhost:44300`
  - `http://localhost:8080`

### Option 2: Build using CLI
- Navigate to the parent directory of the project. And run the following command:
  ```bash
  dotnet run --project src/Orchard.Web/Orchard.Web.csproj
  ```
- The application will be hosted on:
  - `https://localhost:5000`
  - `http://localhost:5001`

*Note*: PNPM is triggered by Corepack during the build