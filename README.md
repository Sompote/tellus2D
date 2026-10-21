# Tellus: download and licence

Tellus is a plane-strain geotechnical finite element program for engineers. It builds and
calculates excavations, embankments, slopes and foundations with staged construction,
consolidation and safety analysis, and its chat assistant can build a model from a
description in English or Thai. This repository holds the ready-built program for macOS
and Windows. Tellus runs on a subscription, so it needs a licence key, which you activate
once on each computer.

**Quick start.** Download the program for your system from the [Download](#download)
section, e-mail [tellusfem@gmail.com](mailto:tellusfem@gmail.com) for a licence key, and
paste the key into the activation screen the first time Tellus starts. Activation needs
the internet once, and after that Tellus runs on your computer like any desktop program.

![Tellus with the chat agent: for a slope under normal water and drawdown the agent ran two Safety phases, wrote the PDF report and showed the failure mechanisms, while Output shows the drawdown mechanism](docs/images/tellus-agent-report.png)

## What Tellus does

Tellus covers the everyday finite element work of a geotechnical engineer in one program,
and it runs entirely on your own computer, so a calculation needs no other FE software
or network service once the licence is active. You build a model in the **Input**
workspace, calculate it with the built-in solver, and read the results in **Output**,
whereas the **Chat assistant** can do the same steps from a description in words. The
same model can also be driven by AI assistants such as Claude and ChatGPT through Tellus's
MCP server ([Step 4](#step-4-optional-connect-claude-or-chatgpt-through-mcp)), so an
assistant and an engineer can work on one project file.

**Modelling.** The Input workspace follows six steps, each a tab across the top: Soil,
Geometry, Structures, Mesh, Flow conditions and Staged construction. The soil comes from
one or more boreholes, which give sloping strata between them, or from soil polygons drawn
with the mouse for embankments and irregular ground. Anything in the drawing can be
picked to edit its properties, and the Model explorer shows the whole model as an
editable tree.

**Soil models.** Tellus offers linear elastic, Mohr-Coulomb with drained and Undrained A, B
and C behaviour, Hardening Soil and HS small for stiffness that depends on stress and
strain, Soft Soil and Soft Soil Creep for soft clays, and a hypoplastic model for sand
that follows its density.

<img src="docs/images/tellus-material-model.png" alt="The Soil materials card with the list of material models open" width="420">

**Structures and loads.** Plates model walls and sheet piles, either elastic or
elastoplastic with plastic hinges, with interfaces on either side. Spring supports model
struts and anchors, geogrids model reinforcement, and embedded piles carry load by skin
friction and base resistance. Line and point loads complete the model.

**Construction stages and groundwater.** Each phase can excavate or place soil, switch
structures and loads on or off, and change the water. Phases are plastic, consolidation
or Safety (φ-c reduction), and the initial stresses come from the K0 procedure or gravity
loading. Groundwater is set per phase as phreatic levels or as steady-state flow, with
the drainage of each model side chosen for flow and consolidation.

**Mesh and solver.** The mesh uses 15-node triangles, or structured 25-node rectangles,
with local refinement. The solver was checked against analytical solutions, a reference FE
code and published examples.

**Results.** Output plots any phase as a deformed mesh or as contours of displacement,
stress and pore pressure, and hovering the plot reads the values at the nodes. Plates show
axial force, shear force and bending moment diagrams along their length, and the log gives
the forces in struts, geogrids and piles. A Safety phase reports its factor of safety
ΣMsf, while the incremental displacements show the failure mechanism. The Curves manager
plots displacement or excess pore pressure at chosen points against step, time or ΣMsf,
for example settlement against time during consolidation.

![Tellus Output: bending moment diagram along a 6 m plate under a line load](docs/images/tellus-plate-moment.png)

![A slope stability analysis in Output: factor of safety ΣMsf = 1.530 with the displacement contours showing the failure mechanism, beside the chat panel](docs/images/tellus-agent-slope.png)

**Chat assistant.** The Chat assistant opens beside the model, so the model stays in view
while you describe a problem, for example "a 3 m wide strip footing on 5 m of dense sand
over stiff clay carries 150 kPa, the water table is 2 m down; what is the settlement and
the factor of safety?". Tellus then builds the model, calculates it and answers with the
solver's numbers. Every change goes into the model on screen as an ordinary edit, so you
can check, correct and undo it. The assistant works in Thai as well as English, and it can
write a PDF report with pictures of the results. It has two engines: **Jev**, which is
fast and lets Tellus code choose from a fixed set of actions, and the **TigrimOS agent**,
an LLM agent that plans its own steps with all of Tellus's tools. Each engine needs its
own API key, which you enter under **Settings** in the chat panel, and this key is
separate from the Tellus licence. [Step 3](#step-3-optional-set-up-the-chat-agent) explains
how to set up the agent.

**Files.** Projects are saved as small `.tellus` files, a few kB each, because the results
are recalculated rather than stored. Tellus can also read PLAXIS 2D results files
(`.p2dx`) and export a model as PLAXIS 2D commands.

## Download

| System | File | Notes |
|---|---|---|
| macOS 11 or later, Intel or Apple Silicon | [`macos/Tellus-0.1.1-macos-universal.dmg`](macos/Tellus-0.1.1-macos-universal.dmg) | Open the DMG and drag **Tellus** into **Applications**. |
| Windows 10 or 11, 64-bit | [`windows/Tellus-0.1.1-windows-x64.zip`](windows/Tellus-0.1.1-windows-x64.zip) | Unzip the folder anywhere and run `Tellus.exe`. Keep `tigrim.exe` beside it, because the chat assistant uses it. |

The programs are not signed by a registered developer, so the system warns you the first
time. On macOS, right-click **Tellus** in Applications, choose **Open**, and confirm with
**Open**. On Windows, choose **More info** and then **Run anyway** when SmartScreen
appears. Tellus needs about 90 MB of disk, and 4 GB of memory is enough for ordinary
models, whereas 8 GB is comfortable for fine meshes.

## Step 1: get a licence key

Ask for a key by e-mail to **[tellusfem@gmail.com](mailto:tellusfem@gmail.com)** and give
these details, because the key is issued in your name and limited to a number of
computers:

- your full name,
- your affiliation (university or company),
- your e-mail address,
- how many computers will run Tellus,
- the plan you need (academic, standard or trial).

You will receive a key of the form `TLS-XXXX-XXXX-XXXX-XXXX-XXXX`. Keep it private,
because anyone with the key could use one of your seats.

## Step 2: activate Tellus

Start Tellus while connected to the internet. The first screen asks for the key, so paste
it into the box and click **Activate**. Upper or lower case and spaces do not matter.

![The activation screen of Tellus, with a key pasted into the box](docs/images/activation.png)

Tellus checks the key with the licence server and opens the normal workspace. The top bar
then shows who the copy is licensed to, which confirms that activation worked.

![Tellus after activation, with "Licensed to" shown at the right of the top bar](docs/images/activated.png)

## Step 3 (optional): set up the chat agent

The chat assistant is optional, because every model can also be built by hand in Input.
We recommend the **TigrimOS agent** with a model from **OpenRouter**, a service that gives
one API key for many AI models and charges per use. Its recommended model is
**DeepSeek V4.1 Flash** (`deepseek/deepseek-v4.1-flash-1017`), the model Tellus was tested
with, which is fast and cheap, at US$0.15 per million input tokens and US$0.60 per
million output tokens (OpenRouter's prices on 18 October 2026). Payment for the model
goes to OpenRouter and is separate from the Tellus subscription.

**Get an OpenRouter key.**

1. Sign up at [openrouter.ai](https://openrouter.ai) with an e-mail, Google or GitHub
   account.
2. Add credit under **Settings → Credits**, because paid models need a balance. A few
   dollars lasts a long time with this model.
3. Open [openrouter.ai/keys](https://openrouter.ai/keys), click **Create Key**, give it a
   name such as "Tellus", and copy the key, which starts with `sk-or-v1-`. OpenRouter
   shows it only once, so paste it into Tellus straight away or keep it somewhere safe.

**Enter it in Tellus.**

1. Click **Chat assistant** at the top right, choose **TigrimOS agent** in the list next to
   **Assistant**, and click **Settings**.
2. Leave **tigrim** empty, because Tellus finds the agent program that comes with it.
3. Fill in the three fields:

   | Field | Value |
   |---|---|
   | API URL | `https://openrouter.ai/api/v1` |
   | Model | `deepseek/deepseek-v4.1-flash-1017` |
   | API key | your OpenRouter key (`sk-or-v1-…`) |

4. Click **Save key** and then **Done**. The key stays on your computer, in your user
   settings folder, and Tellus gives it only to the agent.

![The chat assistant's settings for the TigrimOS agent, with the OpenRouter API URL, the model deepseek/deepseek-v4.1-flash-1017 and a saved API key](docs/images/agent-settings.png)

**Python for the agent** is optional. When Python with numpy is installed, Tellus finds it
and the agent may use it for numbers, such as a hand check of a result, and without it the
agent still works. The **Agent workspace** is the only folder the agent may write to, and
on macOS its code runs in the system sandbox.

Now describe a problem in the chat, for example "a 3 m wide strip footing on 5 m of dense
sand over stiff clay carries 150 kPa, the water table is 2 m down; what is the settlement
and the factor of safety?". The agent builds the model on screen, calculates it and
answers with the solver's numbers, and each of its changes can be checked and undone.
Other OpenRouter models also work, as do other providers with an OpenAI-compatible API,
when you change the URL and the model. The model must support tool calling, and a weak
model makes more mistakes in building the model, so check its results as you would a
junior engineer's.

## Step 4 (optional): connect Claude or ChatGPT through MCP

Tellus can also be driven from an AI assistant outside the program, such as Claude Desktop,
Claude Code, the ChatGPT desktop app or Codex, through the Model Context Protocol (MCP).
The command `tellus --mcp` starts Tellus without a window as an MCP server that the
assistant launches on your computer and talks to over stdin and stdout. It gives the
assistant 29 tools to build a model, calculate it and read the results, so a request such as
"model a 2 m strip footing on 4 m of sand over soft clay, apply 80 kPa, and give me the
settlement and the factor of safety" is answered with the solver's numbers. The server uses
the same licence as the program, so activate Tellus first ([Step 2](#step-2-activate-tellus)),
and it works on ordinary `.tellus` files, so a project the assistant saves opens in Tellus
for checking.

Each assistant needs the full path of the Tellus program:

| System | Path of the program |
|---|---|
| macOS | `/Applications/Tellus.app/Contents/MacOS/tellus` |
| Windows | the full path of `Tellus.exe`, for example `C:\Tools\Tellus\Tellus.exe` (written `C:\\Tools\\Tellus\\Tellus.exe` inside JSON) |

**Claude Desktop.** Open **Settings → Developer → Edit Config**, which opens
`claude_desktop_config.json` (in `~/Library/Application Support/Claude` on macOS and
`%APPDATA%\Claude` on Windows). Add Tellus under `mcpServers`, save the file and restart
Claude Desktop:

```json
{
  "mcpServers": {
    "tellus": {
      "command": "/Applications/Tellus.app/Contents/MacOS/tellus",
      "args": ["--mcp"]
    }
  }
}
```

The Tellus tools then appear under the tools button in a new chat, and Claude asks before it
uses each one unless you allow it for the conversation.

**Claude Code.** Register the server once for your user account, and the tools load in
the next session, where `/mcp` lists them:

```sh
claude mcp add -s user tellus -- /Applications/Tellus.app/Contents/MacOS/tellus --mcp
```

**ChatGPT desktop app and Codex.** The ChatGPT desktop app, the Codex command line and the
Codex IDE extension start local MCP servers from one shared file, `~/.codex/config.toml`
(`%USERPROFILE%\.codex\config.toml` on Windows). Add Tellus with one command,

```sh
codex mcp add tellus -- /Applications/Tellus.app/Contents/MacOS/tellus --mcp
```

or write the entry into the file by hand:

```toml
[mcp_servers.tellus]
command = "/Applications/Tellus.app/Contents/MacOS/tellus"
args = ["--mcp"]
```

`codex mcp list` shows the server, `/mcp` lists it inside Codex, and the desktop app shows
it under **Settings → MCP servers**. ChatGPT in the web browser and on the phone is
different, because it reaches MCP servers only over HTTPS on the internet and cannot start a
program on your computer. Tellus has no web server of its own, so use the desktop app or
Codex. OpenAI's Secure MCP Tunnel can pass a local server through to ChatGPT on the web, but
we have not tried it with Tellus.

**Options and checks.** To start with a project loaded, add it to the arguments, for
example `["--mcp", "--open", "/path/to/slope.tellus"]`, and add `"--autosave",
"/path/to/slope.tellus"` to save the model there after every change, so you can open the
file in Tellus to see what the assistant built. Ask the assistant to save the project
before you open it otherwise. If the tools do not appear, run the program path with
`--licence` in a terminal to confirm that the computer is activated, and check that the path
in the configuration is complete. The model you choose in Claude or ChatGPT is paid for
under your own Anthropic or OpenAI account, separately from the Tellus subscription. The
Windows configurations follow the same format but have not yet been tried on a Windows PC,
so please tell us at [tellusfem@gmail.com](mailto:tellusfem@gmail.com) if one fails.

## Licence details and moving to another computer

Click the **Licensed to …** label in the top bar to see the key, the plan, the end of the
subscription and how long Tellus may run offline. **Check now** asks the server again,
for example after your subscription was renewed. **Deactivate this computer** frees the
seat, so you can activate the same key on a new computer. Deactivate before you replace
or reinstall a computer, because a seat stays taken until it is released.

![The licence window, with the key, plan, subscription end and the deactivate button](docs/images/licence-window.png)

## Working offline

Activation needs the internet once. After that Tellus runs without a connection for up
to 30 days, and it checks the key with the server about every 7 days whenever it can. If
Tellus has been offline for longer than 30 days, it asks for the key again, and
connecting to the internet and clicking **Activate** restores it. When the subscription
ends or the key is withdrawn, Tellus shows the activation screen at its next check, but
your project files stay yours and open again once the licence is renewed.

## Activating from the command line

IT staff who install Tellus on many computers can activate without opening the program:

```sh
# macOS
/Applications/Tellus.app/Contents/MacOS/tellus --activate TLS-XXXX-XXXX-XXXX-XXXX-XXXX
/Applications/Tellus.app/Contents/MacOS/tellus --licence      # shows the holder, plan and expiry
/Applications/Tellus.app/Contents/MacOS/tellus --deactivate   # frees this computer's seat

# Windows (Command Prompt, in the Tellus folder)
Tellus.exe --activate TLS-XXXX-XXXX-XXXX-XXXX-XXXX
Tellus.exe --licence
Tellus.exe --deactivate
```

On Windows the result appears just after the prompt returns, because `Tellus.exe` is a
windowed program, so press **Enter** once to get a clean prompt again.

The headless MCP server for AI agents (`tellus --mcp`) uses the same licence, so it works
once the computer is activated, and [Step 4](#step-4-optional-connect-claude-or-chatgpt-through-mcp)
shows how to connect it to Claude or ChatGPT.

## When activation fails

| Message | What it means and what to do |
|---|---|
| This licence key is not known. | The key was mistyped. Paste it again from the e-mail. |
| All N seat(s) of this licence are in use. | The key is active on as many computers as it allows. Deactivate one of them, or ask for more seats. |
| This subscription expired on … | The subscription has ended. Ask to renew it, then click **Activate** again. |
| This licence key has been revoked. | The key was withdrawn. Write to tellusfem@gmail.com. |
| Cannot reach the licence server | The computer is offline, or a firewall or proxy blocks the connection. Connect to the internet and try again, or ask IT to allow the licence server (below). |
| The computer's clock is earlier than when Tellus last ran | The system date was set back. Correct the date and time. |

The licence is stored in your user folder (`~/Library/Application Support/Tellus` on
macOS and `%APPDATA%\Tellus` on Windows), so each user account on a shared computer
activates separately. The key is tied to the computer, so copying the licence file to
another computer does not work.

## For IT staff

Tellus contacts only its licence server over HTTPS (port 443), and it does so at
activation, at deactivation and for the check about every 7 days. The calculations
themselves run on the computer and send nothing over the network. The chat assistant is
the one exception, because its engines call the API providers whose keys the user enters
under **Settings**. A university or company network that filters outgoing traffic
therefore needs to allow the licence server, whose address we give on request at
[tellusfem@gmail.com](mailto:tellusfem@gmail.com), and, if the chat assistant is used, the
chosen API provider.

## Help

Write to [tellusfem@gmail.com](mailto:tellusfem@gmail.com) for a key, a renewal, more
seats, or any problem with activation. Please include the message Tellus shows and, for
activation problems, the output of `tellus --licence`, which names the computer and its
licence state.
