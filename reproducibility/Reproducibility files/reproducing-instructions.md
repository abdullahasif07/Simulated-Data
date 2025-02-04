
# NodLink - Simulated Data Pipeline

This file provides the necessary steps to set up and run the **NodLink** project using the **Simulated Data** repository. You can set up the environment using **Conda** commands or use the provided **Dockerfile** and `environment.yml` to create a containerized environment.

---

## 🚀 Environment Setup

### **Option 1: Using Conda**
To set up the environment manually using Conda, follow these steps:

```sh
# Step 1: Create Conda Environment
conda create --name nodlink_env python=3.12.4

# Step 2: Activate Conda Environment
conda activate nodlink_env

# Step 3: Install Required Packages
conda install -c conda-forge streamz=0.6.5 nearpy=1.0.0 networkx=3.4.2 scikit-learn=1.5.2

# Step 4: Install Nostril
pip install git+https://github.com/casics/nostril.git
```

---

### **Option 2: Using Docker (Recommended)**
To set up the environment inside a **Docker container**, follow these steps:

```sh
# Step 1: Build the Docker image
docker build -t nodlink_env .

# Step 2: Run the Docker container
docker run -it nodlink_env
```

📌 **Note:** The `Dockerfile` uses HTTPS to clone repositories. If you encounter issues while cloning, try using SSH:
```sh
git clone git@github.com:PKU-ASAL/Simulated-Data.git
```

If using **SSH for cloning**, ensure your SSH keys are set up on GitHub.

---

## 📂 Step 1: Clone the Repository

To get started, clone the required repository:

```sh
git clone https://github.com/PKU-ASAL/Simulated-Data.git
```
(*If this fails, try using the SSH method mentioned above.*)

---

## 📦 Step 2: Prepare Datasets

The repository contains three dataset folders:

- `SimulatedUbuntu`
- `SimulatedW10`
- `Simulated12`

### **📌 Unzip the Datasets**
Unzip all three dataset folders **on your local machine**.

### **📌 Move Datasets into the `datasets` Folder**
1. Navigate to the `src` folder in the **Simulated-Data-Nodlink** repository.

   ```sh
   cd Simulated-Data-Nodlink/src
   ```

2. Create a new folder named `datasets`:

   ```sh
   mkdir datasets
   ```

3. Move the previously unzipped dataset folders into the `datasets` folder:

   ```sh
   mv ../SimulatedUbuntu ../SimulatedW10 ../Simulated12 datasets/
   ```

---

## 📁 Step 3: Copy ETW and Sysdig Folders

1. Inside the `src` folder, you will find:
   - `ETW` folder
   - `Sysdig` folder

2. **Copy the ETW folder** and paste it into:
   - `SimulatedW10/`
   - `Simulated12/`

   ```sh
   cp -r ETW SimulatedW10/
   cp -r ETW Simulated12/
   ```

3. **Copy the Sysdig folder** and paste it **only in** `SimulatedUbuntu/`:

   ```sh
   cp -r Sysdig SimulatedUbuntu/
   ```

---

# 🚀 Running NodLink - Step by Step

## 📌 1. Preprocess the Data for `SimulatedW10`

### **Navigate to the `SimulatedW10` Folder**
```sh
cd datasets/SimulatedW10/ETW/
```

### **Run Preprocessing Commands**
```sh
# Preprocess benign data
python process_behavior.py --file benign.json --d SimulatedW10/ETW/

# Preprocess anomaly data
python process_behavior.py --file anomaly.json --d SimulatedW10/ETW/
```

---

## 📌 2. Offline Model Training for `SimulatedW10`

### **Train the Embeddings**
```sh
python filename-embedding.py --d SimulatedW10/ETW/
python cmdline-embedding.py --d SimulatedW10/ETW/
```

### **Calculate Weights**
```sh
python caculate-weight.py --d SimulatedW10/ETW/
```

### **Train the Model (Set Epochs = 50)**
```sh
python train.py --epoch 50 --d SimulatedW10/ETW/
```

### **Obtain Anomaly Threshold**
After training, check the **log file** for:
```
anomaly threshold: xxx
```

---

## 📌 3. Online Detection for `SimulatedW10`

### **Navigate to the `real-time` Folder**
```sh
cd datasets/SimulatedW10/real-time/
```

### **Ensure Correct Dataset Paths**
Before running `main.py`, **edit `ProvGraph.py`** and update the dataset path:

- **For ETW dataset (SimulatedW10 and Simulated12)**:
  Update **line 67**:
  ```python
  dataset_path = "datasets/SimulatedW10/realAPTWin10/win10"
  ```

- **For Sysdig dataset (SimulatedUbuntu)**:
  Update **line 51**:
  ```python
  dataset_path = "datasets/SimulatedUbuntu/realAPTlinux/hw17"
  ```

### **Run Online Detection**
```sh
python main.py --t xxx
```
*(Replace `xxx` with the anomaly threshold from training logs.)*

📌 **Note:** The recall score will be printed in the terminal.

---

## 🔁 Running NodLink for Other Datasets (`SimulatedUbuntu`, `Simulated12`)

Repeat the **same steps** for other datasets:

- **Replace `SimulatedW10`** with `SimulatedUbuntu` or `Simulated12` in all commands.

For example, **preprocessing `SimulatedUbuntu`**:

```sh
python process_behavior.py --file benign.json --d SimulatedUbuntu/Sysdig/
python process_behavior.py --file anomaly.json --d SimulatedUbuntu/Sysdig/
```

For **online detection on `SimulatedUbuntu`**:

```sh
python main.py --t xxx --d SimulatedUbuntu/real-time/
```

---

## ⚠ Important Notes

✅ The `--d` flag is **crucial** in all commands (except `main.py`) to ensure the correct dataset is used.  
✅ The **threshold value from `train.py`** is needed for online detection.  
✅ If using **Docker**, ensure that volumes are mounted correctly to access datasets inside the container.  
✅ If cloning fails using **HTTPS**, try using SSH:  
   ```sh
   git clone git@github.com:PKU-ASAL/Simulated-Data.git
   ```

---

## 🐳 Using Docker for Running NodLink

If using Docker, follow these steps:

1️⃣ **Build the Docker Image**:
   ```sh
   docker build -t nodlink_env .
   ```

2️⃣ **Run the Docker Container**:
   ```sh
   docker run -it --mount type=bind,source=$(pwd)/datasets,target=/app/datasets nodlink_env
   ```
   *(This mounts your local datasets inside the container.)*

3️⃣ **Inside Docker, Activate Conda**:
   ```sh
   conda activate nodlink_env
   ```

4️⃣ **Navigate to `datasets/SimulatedW10/ETW/` and Run NodLink**:
   ```sh
   cd datasets/SimulatedW10/ETW/
   python process_behavior.py --file benign.json --d SimulatedW10/ETW/
   ```
The remaining steps are the same as mentioned in the section for running NodLink step by step
---

##  EOF

