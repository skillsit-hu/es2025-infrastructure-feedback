# EuroSkills Herning 2025 S17 Infrastructure Feedback and Questions

## Project Templates

### React

#### Pull Requests

##### [Configure Nginx for frontend routing, add missing dependencies, and fix Tailwind setup in Vite #1](https://github.com/skill-setup/react-vite-js-base/pull/1)

**Issue**: Missing dependencies and incorrect Tailwind configuration in the Vite setup. Nginx does not properly redirect all requests to index.html, causing hard refresh functionality to fail.

- Added a custom nginx.conf file to configure Nginx for serving the frontend, including support for client-side routing with try_files.
- Updated the Dockerfile to copy the new nginx.conf into the Nginx container, ensuring the server uses the custom configuration.
- Added the missing dependencies to package.json, including @tailwindcss/vite, date-fns, and react-toast. These dependencies were in the IL, but were not installed in this repo yet.
- Fixed vite.config.js to include the @tailwindcss/vite plugin alongside React, enabling Tailwind CSS integration with Vite.

### Laravel

#### Pull Requests

##### [Feat/improve dockerfile performance #1](https://github.com/skill-setup/laravel-base/pull/1)

**Issue**: Performance issue with the Dockerfile.

- Added a comprehensive .dockerignore file to exclude dependencies, build artifacts, IDE/editor files, OS-generated files, test files, documentation, and temporary files from the Docker build context. This helps reduce build size and improves build speed.
- Refactored the Dockerfile to combine system dependency installation, Node.js setup, and Composer installation into a single layer, minimizing the number of image layers and improving build efficiency.
- Improved caching in the Dockerfile by copying composer.json/composer.lock and package.json/package-lock.json before the application code, allowing Docker to cache dependency installation steps when source code changes.
- Streamlined asset building and npm dependency handling by switching to npm ci for clean installs, pruning dev dependencies after build, and cleaning the npm cache to keep the image smaller.
- Consolidated PHP configuration steps and set proper permissions for the application directory in a single layer, further reducing image size and complexity.

**Note**: According to our tests, the new setup reduced the build time from 4.5 minutes to 1 minute in our environment.

##### [feat: add environment configuration #2](https://github.com/skill-setup/laravel-base/pull/1)

**Issue**: The template does not include a .env file, and .env is listed in .gitignore. Competitors are required to commit and push their .env file to the Git server for proper deployment.

- Add .env file with application environment variables
- Remove .env from .gitignore for version control

## Infra (Competition Scripts)

#### Pull Requests

##### [Automate user secrets setup and add .env file support with updated documentation #8](https://github.com/skill-setup/competition-scripts/pull/8)

**Issue**: The initialization script (`init.sh`) does not create user-level secrets automatically; this must be done manually by competitors.

- The initialization script (`init.sh`) now automatically creates user-level secrets (USER and PASS) for each user using the Gitea API, eliminating the need for manual configuration.
- The script writes all relevant environment variables to a new .env file, making it easier to manage and reference configuration values. The script now strips carriage returns and newlines from values read from config/main to prevent formatting issues.
- The README has been updated to remove the step instructing users to manually set up action secrets, reflecting the new automated approach.

##### [Feat/add verdaccio #9](https://github.com/skill-setup/competition-scripts/pull/9)

**Issue**: No npm cache is implemented in the competition environment.

- Added a new `verdaccio.yaml` file that defines the Verdaccio service, including its image, ports, volumes, Traefik labels for routing, and network configuration.
- Updated `init.sh` to start Verdaccio using Docker Compose, and set storage permissions to allow package uploads.
- Updated `start.sh` to include Verdaccio in the list of services started via Docker Compose.
- Updated `stop.sh` to include Verdaccio in the list of services stopped via Docker Compose.

## Other Issues and Questions

### Issue #1

Some of the containers do not restart automatically.

### Issue #2

The Laravel template does not include the `vendor` folder. In Gdansk, the competitors' development environment and development server environment were separated. The competitors' development environment was on Windows, while the development server was running in a PHP Docker container. In this setup, both the competitors' development environment and the development server environment should contain the `vendor` folder (without this folder, autocomplete does not work properly in the development environment). It is not a good solution to map the entire Laravel folder because mapping the vendor folder causes serious performance issues.

**Possible solutions:**

**Option 1:** If we do not have a local Composer cache (similar to the Verdaccio npm cache system), the template should contain the vendor folder (we use this solution during our training competition).

**Option 2:** If we use a PHP Docker container for running the development server, we need a sophisticated mapping system where the entire Laravel folder is mapped to the development server's Laravel folder, except for the `vendor` folder.

**Option 3:** Install PHP (and Composer if we will have a local Composer cache) on Windows to enable competitors to run their development server on Windows.
