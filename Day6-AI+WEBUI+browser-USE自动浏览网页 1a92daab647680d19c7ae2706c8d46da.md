# Day6-AI+WEBUI+browser-USE自动浏览网页

[https://github.com/browser-use/web-ui](https://github.com/browser-use/web-ui)

具体教程请看视频

https://www.douyin.com/root/search/%E5%AD%A6%E4%B9%A0ai1000%E5%A4%A9?aid=60e8480a-7a65-4a46-9896-797d1ea79f87&modal_id=7482010530354367794&type=general

![](https://github.com/browser-use/web-ui/raw/main/assets/web-ui.png)

[](https://camo.githubusercontent.com/39903d1681e88c34cf6c4b39b787ac788e843d81ed85238ad9aa86ba7563d6df/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f73746172732f62726f777365722d7573652f7765622d75693f7374796c653d736f6369616c)

[](https://camo.githubusercontent.com/3d2c750e6207adb7f3fe4b1ed35677bc0b7a12e67bbd3d5e6678a1de6df4c217/68747470733a2f2f696d672e736869656c64732e696f2f646973636f72642f313330333734393232303834323334303431323f636f6c6f723d373238394441266c6162656c3d446973636f7264266c6f676f3d646973636f7264266c6f676f436f6c6f723d7768697465)

[](https://camo.githubusercontent.com/7f6d88507e659f89eb75356e49b3e34237a3118de06ec289f5b8614eef0db6ce/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f63756d656e746174696f6e2d2546302539462539332539352d626c7565)

[](https://camo.githubusercontent.com/0570a31892a1cb9f5399dbbe8f3c282ed739c853dd40789c734224fab64e2c71/68747470733a2f2f696d672e736869656c64732e696f2f747769747465722f666f6c6c6f772f7761726d7368616f3f7374796c653d736f6369616c)

This project builds upon the foundation of the [browser-use](https://github.com/browser-use/browser-use), which is designed to make websites accessible for AI agents.

We would like to officially thank [WarmShao](https://github.com/warmshao) for his contribution to this project.

**WebUI:** is built on Gradio and supports most of `browser-use` functionalities. This UI is designed to be user-friendly and enables easy interaction with the browser agent.

**Expanded LLM Support:** We've integrated support for various Large Language Models (LLMs), including: Google, OpenAI, Azure OpenAI, Anthropic, DeepSeek, Ollama etc. And we plan to add support for even more models in the future.

**Custom Browser Support:** You can use your own browser with our tool, eliminating the need to re-login to sites or deal with other authentication challenges. This feature also supports high-definition screen recording.

**Persistent Browser Sessions:** You can choose to keep the browser window open between AI tasks, allowing you to see the complete history and state of AI interactions.

- bu-webui-demo.mp4

**Installation Guide**

**Prerequisites**

- Python 3.11 or higher
- Git (for cloning the repository)

**Option 1: Local Installation**

Read the [quickstart guide](https://docs.browser-use.com/quickstart#prepare-the-environment) or follow the steps below to get started.

**Step 1: Clone the Repository**

```
git clone https://github.com/browser-use/web-ui.git
cd web-ui
```

**Step 2: Set Up Python Environment**

We recommend using [uv](https://docs.astral.sh/uv/) for managing the Python environment.

Using uv (recommended):

```
pip install uv
python.exe -m pip install --upgrade pip
uv venv --python 3.11
```

Activate the virtual environment:

- Windows (Command Prompt):

```
python -m venv .venv
.venv\Scripts\activate
```

- Windows (PowerShell):

```
.\.venv\Scripts\Activate.ps1
```

- macOS/Linux:

```
source .venv/bin/activate
```

**Step 3: Install Dependencies**

Install Python packages:

```
uv pip install -r requirements.txt
```

Install Playwright:

```
playwright install
```

**Step 4: Configure Environment**

1. Create a copy of the example environment file:
- Windows (Command Prompt):

```
copy .env.example .env
```

- macOS/Linux/Windows (PowerShell):

```
cp .env.example .env
```

1. Open `.env` in your preferred text editor and add your API keys and other settings

**方法2**

**Option 2: Docker Installation**

**Prerequisites**

- Docker and Docker Compose installed
    - [Docker Desktop](https://www.docker.com/products/docker-desktop/) (For Windows/macOS)
    - [Docker Engine](https://docs.docker.com/engine/install/) and [Docker Compose](https://docs.docker.com/compose/install/) (For Linux)

**Installation Steps**

1. Clone the repository:

```
git clone https://github.com/browser-use/web-ui.git
cd web-ui
```

1. Create and configure environment file:
- Windows (Command Prompt):

```
copy .env.example .env
```

- macOS/Linux/Windows (PowerShell):

```
cp .env.example .env
```

Edit `.env` with your preferred text editor and add your API keys

1. Run with Docker:

```
# Build and start the container with default settings (browser closes after AI tasks)
docker compose up --build
```

```
# Or run with persistent browser (browser stays open between AI tasks)
CHROME_PERSISTENT_SESSION=true docker compose up --build
```

1. Access the Application:
- Web Interface: Open `http://localhost:7788` in your browser
- VNC Viewer (for watching browser interactions): Open `http://localhost:6080/vnc.html`
    - Default VNC password: "youvncpassword"
    - Can be changed by setting `VNC_PASSWORD` in your `.env` file

**Usage**

**Local Setup**

1. **Run the WebUI:** After completing the installation steps above, start the application:
    
    ```
    python webui.py --ip 127.0.0.1 --port 7788
    ```
    
2. WebUI options:
    - `-ip`: The IP address to bind the WebUI to. Default is `127.0.0.1`.
    - `-port`: The port to bind the WebUI to. Default is `7788`.
    - `-theme`: The theme for the user interface. Default is `Ocean`.
        - **Default**: The standard theme with a balanced design.
        - **Soft**: A gentle, muted color scheme for a relaxed viewing experience.
        - **Monochrome**: A grayscale theme with minimal color for simplicity and focus.
        - **Glass**: A sleek, semi-transparent design for a modern appearance.
        - **Origin**: A classic, retro-inspired theme for a nostalgic feel.
        - **Citrus**: A vibrant, citrus-inspired palette with bright and fresh colors.
        - **Ocean** (default): A blue, ocean-inspired theme providing a calming effect.
    - `-dark-mode`: Enables dark mode for the user interface.
3. **Access the WebUI:** Open your web browser and navigate to `http://127.0.0.1:7788`.
4. **Using Your Own Browser(Optional):**
    - Set `CHROME_PATH` to the executable path of your browser and `CHROME_USER_DATA` to the user data directory of your browser. Leave `CHROME_USER_DATA` empty if you want to use local user data.
        - Windows
            
            ```
             CHROME_PATH="C:\Program Files\Google\Chrome\Application\chrome.exe"CHROME_USER_DATA="C:\Users\YourUsername\AppData\Local\Google\Chrome\User Data"
            ```
            
            > Note: Replace YourUsername with your actual Windows username for Windows systems.
            > 
        - Mac
            
            ```
             CHROME_PATH="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"CHROME_USER_DATA="/Users/YourUsername/Library/Application Support/Google/Chrome"
            ```
            
    - Close all Chrome windows
    - Open the WebUI in a non-Chrome browser, such as Firefox or Edge. This is important because the persistent browser context will use the Chrome data when running the agent.
    - Check the "Use Own Browser" option within the Browser Settings.
5. **Keep Browser Open(Optional):**
    - Set `CHROME_PERSISTENT_SESSION=true` in the `.env` file.

**Docker Setup**

1. **Environment Variables:**
    - All configuration is done through the `.env` file
    - Available environment variables:
        
        ```
        # LLM API Keys
        OPENAI_API_KEY=your_key_here
        ANTHROPIC_API_KEY=your_key_here
        GOOGLE_API_KEY=your_key_here
        
        # Browser Settings
        CHROME_PERSISTENT_SESSION=true   # Set to true to keep browser open between AI tasks
        RESOLUTION=1920x1080x24         # Custom resolution format: WIDTHxHEIGHTxDEPTH
        RESOLUTION_WIDTH=1920           # Custom width in pixels
        RESOLUTION_HEIGHT=1080          # Custom height in pixels
        
        # VNC Settings
        VNC_PASSWORD=your_vnc_password  # Optional, defaults to "vncpassword"
        
        ```
        
2. **Platform Support:**
    - Supports both AMD64 and ARM64 architectures
    - For ARM64 systems (e.g., Apple Silicon Macs), the container will automatically use the appropriate image
3. **Browser Persistence Modes:**
    - **Default Mode (CHROME_PERSISTENT_SESSION=false):**
        - Browser opens and closes with each AI task
        - Clean state for each interaction
        - Lower resource usage
    - **Persistent Mode (CHROME_PERSISTENT_SESSION=true):**
        - Browser stays open between AI tasks
        - Maintains history and state
        - Allows viewing previous AI interactions
        - Set in `.env` file or via environment variable when starting container
4. **Viewing Browser Interactions:**
    - Access the noVNC viewer at `http://localhost:6080/vnc.html`
    - Enter the VNC password (default: "vncpassword" or what you set in VNC_PASSWORD)
    - Direct VNC access available on port 5900 (mapped to container port 5901)
    - You can now see all browser interactions in real-time
5. **Container Management:**
    
    ```
    # Start with persistent browser
    CHROME_PERSISTENT_SESSION=true docker compose up -d
    
    # Start with default mode (browser closes after tasks)
    docker compose up -d
    
    # View logs
    docker compose logs -f
    
    # Stop the container
    docker compose down
    ```
    

**Changelog**

- [x]  **2025/01/26:** Thanks to @vvincent1234. Now browser-use-webui can combine with DeepSeek-r1 to engage in deep thinking!
- [x]  **2025/01/10:** Thanks to @casistack. Now we have Docker Setup option and also Support keep browser open between tasks.[Video tutorial demo](https://github.com/browser-use/web-ui/issues/1#issuecomment-2582511750).
- [x]  **2025/01/06:** Thanks to @richard-devbot. A New and Well-Designed WebUI is released. [Video tutorial demo](https://github.com/warmshao/browser-use-webui/issues/1#issuecomment-2573393113).