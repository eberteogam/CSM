# Orchard 2.1.7 Local Environment Setup on Windows - NuGet-based - No Admin Privilege

- This guide provides step-by-step instructions to set up a local development environment for Orchard 2.1.7 on a Windows system using NuGet Packages. Note that while the software can be installed without admin privileges, certain steps may require admin access.

### Development guidelines
- The main branch of the repository is the `dev` branch. Always start your development branch from there and open your pull requests targeting this branch, unless instructed otherwise (e.g., it's part of a larger feature development).
- For smaller developments and bugfixes the branch name should be `issue/<issue-code>` (for Lombiq-developers, e.g. `issue/SANTA-123`) or `issue/<short-description>` (e.g., `issue/adding-sponsors-widget`).
- For a large features (that involves through testing and involves other "issue branches") the branch name should be `feature/<short-description>` (e.g., `feature/upgrade-to-1-8`).
- Force push is disabled.

### Sections
1. [Prerequisite Software](#1-prerequisite-software)
2. [Tested Environment](#2-tested-environment)
3. [Installation Steps](#3-installation-steps)
4. [Setting Production Data and building the Project](#4-setting-production-data-and-building-the-project.)
5. [Importing data](#5-importing-data)

### 1. Prerequisite Software
- [Microsoft Visual Studio 2022](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Enterprise&channel=Release&version=VS2022&source=VSLandingPage&cid=2030&passive=false)
- [Git for Windows < v.?](https://gitforwindows.org/)
- [.NET SDK < v8.0](https://dotnet.microsoft.com/en-us/download)
- [Node Version Manager (NVM) for Windows < v1.2.2](https://github.com/coreybutler/nvm-windows/releases)
  - node.js < v? (installed via NVM)
  - npm < v? (installed via NVM)
  - pnpm < v? (installed via Corepack)

### 2. Tested Environment
The current setup versions have been used to develop and test the project successfully.
- Microsoft Visual Studio 2022 v17.14.7
- .NET SDK `v9.0.301`
- NVM `1.2.2`
  - Node.js `v14.17.0` (installed via NVM)
  - NPM `v6.14.16` (installed via NVM)
  - PNPM `v7.15.0` (installed via Corepack with NVM)

### 3. Installation Steps
- Download and install git for Windows from [git](https://gitforwindows.org/).
  <br><br>
- Download and install .NET SDK `< v8.0` from [dotnet-sdk.exe](https://dotnet.microsoft.com/en-us/download).
<br><br>
- Before installing nvm, ensure there are no existing Node.js or npm installations on your system . If you previously installed Node.js (especially in C:\Program Files), uninstall it and remove any related paths from your system environment variables to avoid conflicts.
- Download and install`nvm-setup.exe` (Node Version Manager) from [nvm-windows releases](https://github.com/coreybutler/nvm-windows/releases).
  - Execute the `nvm-setup.exe` installer. [^1]
  - **License Agreement:** Accept the license agreement and click `Next`.
  - **Select Destination Location:** Select the installation path as `C:\Users\$User\AppData\Roaming\nvm` (where `$User` is your non-admin Windows username). 
    - **Do not use the admin user's AppData folder, as this will cause permission issues.** 
  - **Active Version Location:** Leave the symlink path as default `C:\nvm4w\nodejs`.
  - **Desktop Notification:** Select whether you want a desktop notifications or not, this is optional, it won't affect the installation.
  - **Author Progress Email:** You can leave this field empty or fill it with your email address, this is optional, it won't affect the installation.
  - **Ready to Install:** Verify your destination path is pointing to `C:\Users\$User\AppData\Roaming\nvm` and click `Install`.
    - **Do not use the admin user's AppData folder, as this will cause permission issues.**
  - **Completion:** Once the installation is complete, unselect the "Open with Powershell" checkbox, as this will open a PowerShell window with admin privileges, which is not what we're looking for this setup. Click `Finish` to complete the installation.
  - Restart your computer to ensure the environment variables are updated otherwise environment variables won't be recognized in your non-admin user account.
  - After the reboot, open a new command prompt or PowerShell window as a non-admin user and run the following command to verify the installation:
     ```bash
       nvm version
     ```
- Install node.js, npm and pnpm using NVM.
  - In a non-admin command prompt (cmd) run the following command to install the latest LTS version of node.js and npm.
    ```bash
    nvm install lts
    ```
  -  After the lts installation is complete, use the following command to set the installed version as the active version:[^1]
    ```bash
    nvm use lts
    ```
  - At this point, Node.js, npm, pnpm should be installed. You can verify the installation by running the following commands in a command prompt (cmd) terminal, avoid using PowerShell for this step since running `nvm` commands are disabled in PowerShell due to a organization policy.
    ```bash
    node -v
    ```
    ```bash
    npm -v
    ```
    - If npm is not installed automatically, you can install it by running:
      ```bash
      nvm install-latest-npm
      ```
  - Now enable PNPM by Corepack running the following command. PNPM is triggered by Corepack in every build process, so it needs to be prepared and activated.
     ```bash
     corepack enable && corepack prepare pnpm@latest --activate
     ```
  - Verify the PNPM installation by running:
    ```bash
    pnpm -v
     ```

## 4. Setting Production Data and building the Project
- ### Exporting data
  - For this test, we will simulate the bare minimum production data that is required to run the santamonica.gov website. From there on, you can add more data as you need it.
  - Go to `https://santamonica.gov/admin` website, within the modules go to `> Configuration > Import/Export > Deployment Plan`.
- Search for the `Top Menu items and Footer` deployment package, once found it, click on `Manage Steps` > `Execute` > `Select`. This will download the deployment package to your local machine.

- ### Clone & Build SMC Repository
  - Before importing the production data, we need to clone the santamonica.gov.core repository and build the project, since the deployment package is imported using the admin panel of the application.
  - Clone the [santamonica.gov.core repository](https://github.com/CityofSantaMonica/santamonica.gov.core) in your local environment. Having git installed, run the following command.
      ```bash
      git clone https://github.com/CityofSantaMonica/santamonica.gov.core.git
      ```
  - Open Visual Studio 2022, click on `Open a project or new folder`. Navigate where you cloned the repository and select the folder to open the project.
  - Before building the project, let's verify the database connection and ensure the database is set up correctly. For this documentation, we have used a SQLite approach due to the automatic setup of the database connection.
    - Within Visual Studio, open the file `src/Orchard.Web/appsettings.Development.json`. This file contains the auto setup database connection step for the local development environment 
    - This file also contains the database credentials for the admin user that will be later used to log in to the admin panel of the application to import the production data.
    - Within the file, look for the `DatabaseProvider` key and ensure it is set to `Sqlite`. This indicates that the project is configured to use SQLite as the database provider for local development. Orchard supports database providers like `MySql`, `PostgreSql`, or `Sqlite` by changing the value of this key accordingly, however, the setup might vary based on the db provider you choose.
  - Now open a terminal within Visual Studio or use a command prompt (cmd) in the project directory and run the following command. Don't run the following command in PowerShell, as pnpm is not supported in PowerShell due to an organization policy.

- ```bash
  dotnet run --project src/Orchard.Web/Orchard.Web.csproj
  ```
 
- The application will start running on the default ports, which are typically:
  - `http://localhost:8080`
  - `https://localhost:44300`
  
- At this point, the application should be running, and the database should have been set up automatically using SQLite. Tables and data seeding should have been performed automatically by the application during the build process and the legacy views should be available in the database.
  
## 5. Importing data
- With the application running in localhost, you can now import the latest production data that you exported earlier.
- Navigate to `https://localhost:44300/admin` in your web browser and within the modules go to `> Configuration > Import/Export > Package Import`.
- Click on `Import` and select the deployment package (.zip folder) you downloaded earlier.

- ### Preview data using SQL Server Object Explorer
- To preview the data in the database, you can use SQL Server Object Explorer in Visual Studio. Follow these steps:
- Open Visual Studio and go to `View > SQL Server Object Explorer`.
- In the SQL Server Object Explorer, right-click on `SQL Server` and select `Add SQL Server`.
- In the `Connect to Server` dialog, enter the following details:
- `Server Name:` `(localdb)\MSSQLLocalDB`
- `Authentication:` `Windows Authentication`
- Click `Connect`.
- Once connected, you should see the SQL Server LocalDB instance in the SQL Server Object Explorer. You can expand the `Databases` node to see the databases available on your local instance.

*Notes*: 
- PNPM is triggered by Corepack during every build. 
  - PNPM is a package manager that helps manage project dependencies efficiently.
  - Corepack is a tool that allows you to use package managers like PNPM, Yarn, and NPM without needing to install them globally.
- NVM allows you to manage multiple Node.js, npm, and PNPM versions on your system.
  - If you need to switch Node.js versions in the future, you can use `nvm use <version>`.

[^1]: This step requires admin privileges