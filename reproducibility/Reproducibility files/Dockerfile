# Step 1: Use Ubuntu 20.04 as the base image
FROM ubuntu:20.04

# Step 2: Set up the working directory inside the container
WORKDIR /app

# Step 3: Install system dependencies required for Conda and Python
RUN apt-get update && apt-get install -y \
    wget bzip2 git curl ca-certificates && \
    apt-get clean

# Step 4: Install Miniconda (Lightweight Anaconda)
RUN wget -O miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    bash miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh

# Step 5: Add Conda to the system PATH
ENV PATH="/opt/conda/bin:$PATH"

# Step 6: Copy the environment.yml file into the container
COPY environment.yml /app/environment.yml

# Step 7: Create the Conda environment
RUN conda env create -f /app/environment.yml

# Step 8: Activate Conda environment and install `nostril` from GitHub
RUN /opt/conda/bin/conda run -n nodlink_environment pip install --no-cache-dir git+https://github.com/casics/nostril.git

# Step 9: Ensure the Conda environment activates correctly inside the container
SHELL ["/bin/bash", "-c"]
RUN echo "source /opt/conda/bin/activate nodlink_environment" >> ~/.bashrc
ENV CONDA_DEFAULT_ENV=nodlink_environment
ENV PATH="/opt/conda/envs/nodlink_environment/bin:$PATH"

# Step 10: Copy the project files into the container
COPY . /app

# Step 11: Set the default command to use bash inside the container
CMD ["bash"]
