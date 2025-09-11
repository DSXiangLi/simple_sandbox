# LLM Python Code Sandbox 🚀
> Self-Hosting a E2B like coding sandbox

| Logo                                       | Description   |
|--------------------------------------------|---------------|
| <img src="./asset/logo.png" width="500px"> | A Python code sandbox HTTP service designed for LLMs, allowing creation of isolated Python execution environments with support for code execution, file upload/download, and MCP integration. 🎛️
|


## 🌟 Features

1. **Create Sandbox** 🆕: Initialize a Jupyter kernel and return a unique ID
2. **Execute Code** ⚡: Run code in a specified Jupyter kernel and return stdout, stderr, error, traceback, and binary files like images
3. **File Operations** 📁: Upload files to the sandbox working directory and download files from the sandbox
4. **Close Sandbox** 🗑️: Safely shut down sandboxes and clean up resources
5. **Sandbox Isolation** 🔒: Each sandbox has its own Python virtual environment and working directory to avoid package conflicts
6. **Auto-Cleanup** 🧹: Sandboxes automatically close and clean up resources after 24 hours, with hourly cleanup of expired sandboxes
7. **Virtual Environment Mirror** 🪞: Service automatically creates a base virtual environment image with common packages on startup, enabling fast initialization of new sandboxes by copying the image
8. **MCP Support** 🤖: Integrated with FastAPI-MCP, allowing the service to be directly called by AI models

## 🚀 Getting Started

### Install Dependencies
Execute the following command in the project directory to install dependencies:
```bash
cd sandbox
pip install -r requirements.txt
```

### Start the Service
Run the following command to start the sandbox service:
```bash
python main.py --host 0.0.0.0 --port 8000
```

The service will start at http://0.0.0.0:8000 🌐

## 📱 Create Sandbox（E2B like client）
You can directly use the Client to call the sandbox service
```python
# Create client instance
client = SandboxClient(base_url='http://0.0.0.0:8000')

# Create sandbox
sandbox_id = client.create_sandbox()

# Execute code
client.execute_code("print('Hello, Sandbox!')")

# Install required Python packages in the sandbox's virtual environment
client.install_package("numpy")

# View generated files
files = client.list_files()

# Download generated CSV file
csv_file = next((f for f in files if f['path'].endswith('.csv')), None)
client.download_file(csv_file['path'])

# Upload local file to sandbox
client.upload_file('test_upload.txt')

# Close sandbox
client.close_sandbox()

# Check if all sandboxes are closed
client.list_all_sandboxes()
```