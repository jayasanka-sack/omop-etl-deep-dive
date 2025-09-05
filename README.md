
## Prerequisites

### Installing Docker

<details>
<summary>mac OS</summary>


1. **Manual Installation:**
    - Download Docker Desktop from [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
    - Install and launch Docker Desktop
    - Ensure Docker is running (you should see the Docker icon in your menu bar)
2. Or ** Using Homebrew:**
   ```bash
   brew install --cask docker
   ```
   Then launch Docker Desktop from Applications.
</details>

<details>
<summary>Windows</summary>

1. Download Docker Desktop from [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Install and launch Docker Desktop
3. Ensure WSL 2 is enabled if prompted
</details>

<details>
<summary>Linux (Ubuntu/Debian)</summary>


```bash
# Update package index
sudo apt-get update

# Install prerequisites
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# Start Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Add your user to docker group (optional, to avoid sudo)
sudo usermod -aG docker $USER
```

</details>


## Development

### **Creating/Updating SQL Models**
Models are SQL files that define the transformations and logic for your ETL process. They are located in the `core/models` directory of the project.
For information on how to create SQL models, please refer to the [SQLMesh Models Overview documentation](https://sqlmesh.readthedocs.io/en/stable/concepts/models/overview/).


Once you have created or updated your SQL models, you need to apply these changes to the database.

   ```bash
   docker compose run --rm core run-pipeline
   ```
Rerun this after making changes to model files.

Once the commands complete, your OMOP postgress dayabase will be updated with the latest transformations defined in your SQL models.
You can access the database at `localhost:5433` with following credentials:
- **Database:** omop
- **User:** omop
- **Password:** omop
- **host:** localhost
- **Port:** 5433

### 🎉 _That's it! You now have a local OMOP CDM database populated with data from OpenMRS!!_

### Accessing the Database with cloudBeaver
If you want to explore the OMOP database using a graphical interface, you can use PgAdmin. It is included in the current setup so you don't need to install it separately.

Access CloudBeaver at [http://localhost:8978](http://localhost:8978) with:

- Then from the left sidebar, click on servers > OMOP Postgres to connect to the OMOP database.


# Create a PostgreSQL Connection in CloudBeaver

## 1. Create an Admin Account

1. Open CloudBeaver in your browser:  
   [http://localhost:8978](http://localhost:8978)

2. The **Setup Wizard** will appear the first time you run CloudBeaver.

3. Enter your desired **Admin username** (e.g., `cbadmin`).

4. Enter and confirm your **Admin password** (e.g., `Admin@123`).

5. Complete the wizard to create the admin account.

6. Use these credentials to log in to CloudBeaver.

## 2. Create a PostgreSQL Connection

1. Open CloudBeaver in your browser:  
   [http://localhost:8978](http://localhost:8978)

2. Log in with your **CloudBeaver admin credentials**.

3. From the top menu, click **New Connection**.

4. In the connection type list, select **PostgreSQL**.

5. Fill in the connection details:
   - **Host**: `omop-db` (or your PostgreSQL host)
   - **Port**: `5432` (default PostgreSQL port, update if different)
   - **Database**: (enter your database name, e.g., `omop`)
   - **User name**: `omop`
   - **Password**: (your PostgreSQL user password)

6. (Optional) Click **Test Connection** to verify the details.

7. Click **Create** to save the connection.

<img src="/docs/img/initiate-new-connection.png" alt="SQLMesh UI"> width="600"/>


Once your work is done, you can stop the services with:

```bash
docker compose stop
```

### Fancy a User Interface?

```bash
    docker compose run --rm --service-ports core sqlmesh-ui
```

Access the UI at [http://localhost:8000](http://localhost:8000)
   
<img src="/docs/img/sql_mesh.jpeg" alt="SQLMesh UI"> width="600"/>

-- 

<summary>Fancy a Data Quality Check? (This will cover on upcoming weeks)</summary>

<details>

### 1. **Run Achilles to generate data summaries** (Check What Achilles does below.)
   ```
   docker compose run achilles
   ``` 
### 2. **Run DQD to perform data quality checks**
This runs the [OHDSI Data Quality Dashboard (DQD)](https://github.com/OHDSI/DataQualityDashboard) on the OMOP database.
   ```bash
    docker compose run --rm dqd run 
   ```
### 3. **View the Data Quality Dashboard**
      This serves the DQD results on a local web server. Once it's running, open your browser and go to [http://localhost:3000](http://localhost:3000).
   ```
   docker compose run --rm --service-ports dqd view
   ``` 

## 🧪 What does Achilles do?
Achilles analyzes the OMOP CDM data and generates summary statistics, data quality metrics, and precomputed reports. These results are essential for visualizations in tools like Atlas.

When you run:

```
docker compose run achilles
```
- ✅ It connects to your omop-db
- ✅ Scans and summarizes data in the public schema
- ✅ Produces results in the Achilles_results and Achilles_analysis tables
- ✅ Prepares your OMOP CDM for use with the web-based Atlas UI

</details>




<summary>Setting Up Git LFS for This Repository</summary>

<details>

### Setting Up Git LFS for This Repository

This repository uses **Git Large File Storage (LFS)** to handle large files like `CONCEPT.csv`. If you're cloning or pulling the repository, make sure to set up Git LFS to download the actual files instead of pointers.

### Step 1: Install Git LFS
Before cloning, install Git LFS:

- **macOS (Homebrew)**  
  ```sh
  brew install git-lfs
  ```

- **Linux (Ubuntu/Debian)**
  ```sh
  sudo apt update && sudo apt install git-lfs
  ```

- **Windows**  
  Download and install Git LFS from [Git LFS official site](https://git-lfs.github.com/).

### Step 2: Clone the Repository
After installing Git LFS, clone the repository:

```sh
git clone https://github.com/jayasanka-sack/openmrs-to-omop.git
cd openmrs-to-omop
```

Git LFS will automatically download the large files.

### Step 3: Pulling Updates
If you have already cloned the repository before installing Git LFS, or if you are pulling new changes, run:

```sh
git lfs install
git lfs pull
```

This ensures all large files are properly downloaded.

### Troubleshooting
If you see pointer files instead of actual data when opening a large file (e.g., `CONCEPT.csv`), it means Git LFS is not set up correctly. Run:

```sh
git lfs pull
```

For more information, refer to the [Git LFS documentation](https://git-lfs.github.com/).
</details>
