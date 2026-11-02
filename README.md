# Local AI Starter

A small, beginner-friendly Windows desktop app for setting up [LocalAI](https://localai.io/) with [Podman](https://podman.io/) and two local Qwen 2.5 chat models.

The app guides you through setup and opens LocalAI's built-in chat page when it is ready. After the initial downloads and model preparation are complete, you can chat without an internet connection.

This project is a setup helper, not a cloud AI service. The model runs on your
computer through LocalAI; the browser is simply the chat window.

## Contents

- [What it installs](#what-it-installs)
- [Quick start](#quick-start)
- [Use LocalAI](#use-localai)
- [How setup works](#how-setup-works)
- [Beginner tips](#beginner-tips)
- [Requirements and downloads](#requirements-and-downloads)
- [Privacy and offline use](#privacy-and-offline-use)
- [Models and attribution](#models-and-attribution)
- [Files and storage locations](#files-and-storage-locations)
- [Troubleshooting](#troubleshooting)
- [Uninstalling](#uninstalling)
- [Documentation](#documentation)

## What it installs

| Component | Details |
| --- | --- |
| Container runtime | Podman and its Linux virtual machine |
| Local AI server | LocalAI CPU container image |
| Regular chat model | Qwen 2.5 3B Instruct, GGUF Q4_K_M |
| Community fine-tune | Qwen 2.5 3B Abliterated, GGUF Q4_K_M |

Both models are downloaded, so plan for several gigabytes of internet data and free storage. The app does not check your PC's hardware or choose models based on disk space.

## Quick start

Choose the option that matches what you downloaded:

### Recommended: use the `.exe` (Python not required)

1. Open this project's GitHub page.
2. Select **Releases**.
3. Open the latest release, find **Assets**, and download `LocalAI-Starter.exe`.
   If you do not see a **Releases** section or an `.exe` asset, a release has
   not been published yet. You can use the Python option below if you already
   have Python installed, or wait until a downloadable app is published.
4. Run `LocalAI-Starter.exe`. Setup begins automatically; you do not need to
   install Python.
5. Keep your internet connection on while Podman, the LocalAI image, and both
   models are installed and prepared.
6. Approve Windows prompts if they appear. If Windows asks to enable WSL2 or
   restart, follow its instructions, restart if needed, and reopen the app.
7. When setup finishes, LocalAI opens at
   [http://localhost:8080](http://localhost:8080).
8. In the LocalAI chat page, choose `qwen25-instruct` or
   `qwen25-abliterated`.

The `.exe` includes the Python runtime needed by the app. It does **not** include
Podman, the LocalAI container, or model weights; those are downloaded during
setup.

### Run the `.py` source (Python required)

Choose this option if you have Python installed and want to run the source code:

1. Install Python 3.9 or later if it is not already installed.
2. Download the project source from GitHub using **Code → Download ZIP**, then
   extract the ZIP; or clone the repository.
3. Open Command Prompt or PowerShell in the extracted project folder.
4. Run `python localai_setup.py`. Setup begins automatically.
5. Keep your internet connection on during installation and follow the same
   setup prompts described above.

The **Set up LocalAI** button can be used to retry or resume setup. Podman is
installed with `winget` if it is missing. If WinGet is not already available,
the app downloads the official WinGet app package and its dependencies from
[microsoft/winget-cli](https://github.com/microsoft/winget-cli/releases/latest),
verifies the release SHA-256 digests, and installs it for the current Windows
account. This first-time bootstrap downloads roughly 300 MB. You do not need to
download WinGet yourself.

The app starts setup automatically on launch. **Set up LocalAI** is available
if you need to retry or continue after a restart. **Start LocalAI** starts the
already-installed container without repeating the initial downloads.

If you are unsure which file to use, choose `LocalAI-Starter.exe` from the
latest release's **Assets**. Use `localai_setup.py` only if you intentionally
want to run the Python source and already have Python installed. A GitHub
**Source code (zip)** download contains the source; it is not the ready-to-run
`.exe`.

## Use LocalAI

### Open the local chat

When setup finishes, the app opens the LocalAI web interface in your browser.
You can also open [http://localhost:8080](http://localhost:8080) yourself.
This address means "port 8080 on this computer"; it is not a public website.

In the LocalAI interface:

1. Open **Chat**.
2. Select a model from the model selector:
   - `qwen25-instruct` for the regular Qwen 2.5 Instruct model.
   - `qwen25-abliterated` for the community abliterated fine-tune.
3. Enter a message and send it.
4. Change the selected model when you want to use the other one.

If the model selector is empty immediately after setup, wait a little and
refresh the page. A CPU model can take time to load the first time.

### Everyday questions and coding

These are general-purpose text models. For example, you can ask for:

- An explanation of a programming error or a piece of code.
- Help writing or understanding a short script.
- A summary of text that you paste into the chat.
- Brainstorming, drafting, translation, or general questions.

The model does not automatically see your screen, files, browser, or editor.
Copy and paste the specific text or code you want it to work on. Review
generated code before running it: small local models can misunderstand a
request, invent details, or produce incorrect or insecure code.

The two entries are separate models, not a guarantee of different quality for
every task. Compare their responses and use whichever suits the conversation.
The "abliterated" model is a community fine-tune and may behave differently
from the regular instruction model.

## How setup works

On launch, Local AI Starter checks and prepares the local components in this
order:

1. **WinGet** — uses the copy already available on Windows, or downloads the
   official Microsoft WinGet release from GitHub if it is missing. The release
   package and its dependencies are checked against the SHA-256 values
   published by GitHub before installation.
2. **Podman** — uses an existing Podman installation or asks WinGet to install
   it. Windows may show an installer or permission prompt.
3. **Podman machine** — creates and starts Podman's Linux virtual machine, or
   starts an existing one.
4. **LocalAI container image** — downloads the CPU image once. Later starts
   reuse the locally stored image rather than pulling it again.
5. **Model files** — downloads both Qwen GGUF files from Hugging Face into the
   local models folder. An interrupted model download can be retried.
6. **Model setup** — creates a LocalAI configuration for each model and sends
   a small local request to initialize it.
7. **Chat page** — waits for LocalAI to report ready, then opens the browser.

The process can take a while, especially on a slower connection or CPU. Keep
the app open while it is installing or preparing the models. The setup log in
the window shows the commands and their output. If setup is interrupted, open
the app again and use **Set up LocalAI** to retry.

### After the first setup

The **Start LocalAI** button starts the existing Podman VM and container. The
model files and container image remain on disk, so normal local chat does not
need to download them again. To open the interface without the app, visit
[http://localhost:8080](http://localhost:8080) while LocalAI is running.

Closing the browser does not stop LocalAI. Closing the starter app also does
not explicitly stop the Podman container. To stop the service, open PowerShell
and run:

```powershell
podman stop localai-beginner
```

To start it from PowerShell later, first start the Podman machine if necessary,
then run:

```powershell
podman machine start
podman start localai-beginner
```

If Podman reports that the machine is already running, continue with the
container start command.

## Beginner tips

### Start with a clear request

Local models respond best when you say what you want and include relevant
context. Instead of asking "fix this", paste the error and nearby code, then
describe what you expected to happen. For example:

> I am learning Python. Explain this error in simple language, tell me which
> line causes it, and suggest one small fix. Do not rewrite the whole program.

For code tasks, state the programming language, the goal, and any constraints.
For writing tasks, tell the model the audience and the tone you want. If an
answer is too long or unclear, ask a follow-up question or request a shorter
explanation.

### Give the model only the context it needs

The chat model does not automatically inspect your computer. It cannot see a
file just because the file exists on your PC. Paste a small relevant excerpt,
the exact error message, or a short example. Remove passwords, access tokens,
private keys, personal information, and other secrets before pasting content.

If a document or code file is very large, start with the specific section
related to your question. You can provide more context in a follow-up if the
first answer is off target.

### Check answers before relying on them

Local language models generate likely responses; they do not guarantee that an
answer is correct. Verify facts that matter, run and review code in a safe
environment, and do not use model output as the only basis for medical, legal,
financial, safety, or security decisions. When asking for code, request an
explanation of the proposed change and test it before using it.

### Choose between the two models

Start with `qwen25-instruct` for ordinary questions and coding help. Try
`qwen25-abliterated` if you specifically want to compare the community
fine-tune's response style. Each has separate weights and may respond
differently to the same prompt. Neither model has perfect knowledge, and
neither automatically knows current events.

### Understand local chat and chat history

The browser is the interface to LocalAI running on your own PC. The setup app
does not provide an online account, hosted chat service, or synchronization
between devices. Conversations shown by the LocalAI interface are managed by
that application; this project does not define a separate account or backup
service for them.

Avoid putting sensitive information into chats unless you understand how the
LocalAI interface stores its conversations and how other users of your Windows
account can access your files.

### Internet and offline behavior

Internet is needed for the initial installers and model downloads. Once setup
has completed and both models are available, ordinary inference uses the local
server and downloaded weights. Disconnecting from the internet does not make
the app download missing files; finish setup first. Some optional LocalAI
features or websites may require network access, but this starter's text chat
does not need a remote AI account.

If you restart Windows, the Podman VM or container may need to be started
again. Open Local AI Starter and choose **Start LocalAI**, then use
`http://localhost:8080`.

## Requirements and downloads

- **Operating system:** Windows with WSL2 support. Podman uses a Linux VM on
  Windows. WinGet requires Windows 10 version 1809 (build 17763) or later.
- **Python:** not required when running the released `.exe`. Python 3.9 or later
  is needed only if you choose to run `localai_setup.py`.
- **Internet:** required for initial downloads. If WinGet is missing, its
  official release package and dependencies require roughly 300 MB. Podman,
  the LocalAI CPU image, and the two models require additional downloads.
- **Storage:** allow several gigabytes free. The two Q4_K_M model files total
  about 4 GB; the image, Podman VM, installer packages, and temporary files
  require additional space.
- **Permissions:** installing software or enabling WSL2 can trigger Windows
  prompts. Follow the prompts shown by Windows. The app does not silently
  bypass Windows security or elevation prompts.

This starter does not check your memory, processor, graphics card, or free
disk space and does not choose models based on hardware. The CPU image works
without a supported GPU, but inference speed depends on your computer. A model
may use several gigabytes of memory while it is running.

Download sizes vary. CPU speed depends on your computer. These compact models
can help with everyday questions and coding, but may make mistakes; verify
important information.

## Privacy and offline use

LocalAI serves its web interface and API on port `8080`. Chat requests are
handled by the LocalAI server running on your PC. During setup, the app may
contact:

- GitHub, to find and download the official WinGet release if WinGet is missing.
- WinGet and its configured package sources, to install Podman.
- The container registry, to download the LocalAI CPU image.
- Hugging Face, to download the selected model files.

The app does not send chat messages to these download sources as part of normal
local inference. LocalAI itself exposes an HTTP API on your machine, and no
authentication is configured by this starter.

Keep internet connected until setup and the first model startup finish. After the model files and runtime are downloaded and prepared, ordinary chat can work offline. Features that require online services are not included in this guarantee.

Podman publishes the LocalAI port on `127.0.0.1` only, so the chat interface is
intended to be reachable from this PC, not other devices on your network. This
starter does not configure LocalAI authentication. Do not change the port
mapping to listen on all network interfaces or expose it to the public internet
unless you have separately configured appropriate authentication and network
protections.

If you are upgrading from a version that created the container with a
non-loopback port mapping, setup removes and recreates only the
`localai-beginner` container with the localhost-only mapping. Your downloaded
model files in `%USERPROFILE%\.localai\models` are kept.

## Models and attribution

- **Regular model:** [Qwen2.5-3B-Instruct GGUF](https://huggingface.co/bartowski/Qwen2.5-3B-Instruct-GGUF),
  using `Qwen2.5-3B-Instruct-Q4_K_M.gguf`.
- **Community fine-tune:** [Qwen2.5-3B-Instruct-abliterated GGUF](https://huggingface.co/mradermacher/Qwen2.5-3B-Instruct-abliterated-GGUF),
  using `Qwen2.5-3B-Instruct-Abliterated.Q4_K_M.gguf`.

Both use the Q4_K_M GGUF quantization. Quantization reduces disk and memory
requirements compared with full-precision weights, but can affect response
quality. Model files are hosted by third parties; LocalAI does not create or
maintain these model weights.

Check each model card and its license before redistributing or using the models. LocalAI and Podman are separate open-source projects; see their official sites for their respective licenses and documentation.

## Files and storage locations

The app and its downloaded data are stored separately:

| Location | Contents |
| --- | --- |
| Project folder | `localai_setup.py`, `build_exe.bat`, and `README.md`. |
| `dist\LocalAI-Starter.exe` | The standalone Windows app generated by the build script. |
| `%USERPROFILE%\.localai\models` | Model weights and LocalAI model configuration files. |
| `%LOCALAPPDATA%\LocalAI-Starter\bootstrap` | WinGet release files and dependencies, if WinGet had to be bootstrapped. |
| Podman machine storage | Linux VM, LocalAI container image, and container data managed by Podman. |

Deleting the project folder or the `.exe` does not remove downloaded models,
the Podman VM, or the container image. See [Uninstalling](#uninstalling) if you
want to remove those separately.

## Troubleshooting

### WinGet bootstrap or installation failed

The app downloads WinGet from the official Microsoft GitHub release and
verifies its SHA-256 checksums before installing it. Check your internet
connection and Windows version (WinGet needs Windows 10 version 1809 or later),
then reopen the app and retry. If your organization blocks MSIX installation,
install or repair App Installer from the Microsoft Store, or install Podman
from the [official Podman installation guide](https://podman.io/docs/installation).

### Windows says the app is unrecognized

Windows SmartScreen may show a warning for an unsigned executable. Only run
the app if you trust the source you downloaded it from. For source builds,
review `localai_setup.py` and build the executable yourself using the steps
above.

### Podman could not be installed

Check that WinGet is available and that Windows allows the installer to run.
Some organizations restrict software installation by policy. You can install
Podman manually using the [official Podman instructions](https://podman.io/docs/installation),
then reopen Local AI Starter.

### Podman machine does not start

Podman on Windows depends on WSL2 and Windows virtualization features. Follow
any Windows prompts to enable required features and restart. In PowerShell,
you can inspect Podman's machine state with:

```powershell
podman machine list
```

If the machine exists but is stopped, try:

```powershell
podman machine start
```

If an error persists, copy the exact message from the setup log and consult
the [Podman for Windows documentation](https://podman.io/docs/installation).

### Port 8080 is already in use

LocalAI uses port `8080` by default. Close the other application using that
port, then retry setup. The starter currently expects the LocalAI chat page
at `http://localhost:8080`.

### Windows asks to enable WSL2 or restart

Follow the Windows prompt, restart if requested, then run **Set up LocalAI** again. Setup can continue using downloads that are already present.

### Setup cannot download a file

Check your internet connection, available storage, and whether a firewall or
proxy is blocking GitHub, the container registry, or Hugging Face. Try
**Set up LocalAI** again. Incomplete model downloads are kept so setup can try
to resume them. Verified WinGet release files are cached under
`%LOCALAPPDATA%\LocalAI-Starter\bootstrap`.

### Setup seems slow or stuck

The first run downloads multiple large files and then starts each model once.
The model initialization step can take several minutes on a CPU. Check the
setup log for recent output and keep the window open while commands are
running. Do not start multiple copies of setup at the same time.

### The model is missing from the chat selector

Wait for setup to complete and refresh the browser page. If it is still
missing, reopen the starter and use **Set up LocalAI** to retry. Check the
setup log for model download or initialization errors.

### Chat responses are slow

The app uses the CPU-only LocalAI image. Response speed depends on the CPU,
memory, and the amount of text in the conversation. Shorter prompts may be
faster. This setup does not configure GPU acceleration.

### The chat page does not open

Open [http://localhost:8080](http://localhost:8080) manually. If LocalAI is
stopped, reopen the app and click **Start LocalAI**. Port `8080` must be
available.

## Uninstalling

This starter does not include an uninstaller. Removing the app executable alone
does not delete Podman or downloaded models.

To remove only this project's model files, first stop the LocalAI container:

```powershell
podman stop localai-beginner
```

Then delete the specific model folder in File Explorer:

```text
%USERPROFILE%\.localai\models
```

This removes the downloaded model weights and their LocalAI configuration
files. To remove the LocalAI container, use:

```powershell
podman rm localai-beginner
```

Podman, its Linux machine, and the LocalAI container image are separate
installations. Remove those only through Podman's documented management and
uninstall instructions. Be sure you want to remove the Podman machine before
deleting it, because other Podman projects may also use it.

## Documentation

- [LocalAI quickstart](https://localai.io/docs/basics/getting_started/)
- [LocalAI containers and Podman](https://localai.io/docs/installation/containers/)
- [Podman installation for Windows](https://podman.io/docs/installation)
