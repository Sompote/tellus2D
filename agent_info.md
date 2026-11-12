# Tellus 2D: information for AI agents

Give this file to your AI assistant (Claude Code, Claude Desktop, Cursor or another agent)
before you ask it to work with Tellus. It tells the agent what Tellus is, how to drive it,
which rules to follow, and how to run an analysis on a remote computer. It describes
Tellus 0.1.4, the version in this repository.

---

## 1. What you are working with

Tellus 2D is a plane-strain geotechnical finite element program. A model is a cross-section
through the ground, one metre thick. It calculates deformations, stresses, pore pressures,
structural forces and factors of safety for footings, embankments, excavations with walls
and struts, and slopes.

You, the agent, drive Tellus through its MCP server, `tellus --mcp`. The server builds the
model, runs the solver and returns the results. **Every number you report must come from a
tool result.** Do not estimate, round from memory, or fill a gap with a typical value
without saying so.

The engineer you work for is responsible for the design. Your job is to build what was
asked, calculate it, report the results with their limits, and say clearly what you
assumed.

## 2. Units and conventions

| Quantity | Unit or rule |
|---|---|
| Length | m |
| Force | kN |
| Stress, pressure, stiffness | kPa (kN/m²). A stiffness given in MPa is entered times 1,000 |
| Time | days |
| Axes | x to the right, y upwards, so depths are negative levels |
| Loads | a negative value acts downwards |
| Stresses | negative in compression |
| Structural forces | per metre run of the section. One strut, anchor or pile carries the value times its spacing |
| Line load | kN/m/m (a pressure along a line). Point load: kN/m |

## 3. Connecting to Tellus

The program is `tellus` (`Tellus.exe` on Windows). Its full path is needed.

| System | Path of the program |
|---|---|
| macOS | `/Applications/Tellus.app/Contents/MacOS/tellus` |
| Windows | the folder that was unzipped, for example `C:\Tools\Tellus\Tellus.exe` |
| Linux | the folder that was unpacked, for example `/opt/tellus/tellus` |

**As an MCP server in Claude Code** (the user runs this once, then starts a new session):

```
claude mcp add -s user tellus -- /Applications/Tellus.app/Contents/MacOS/tellus --mcp
```

Other clients (Claude Desktop, Cursor) take the same command under `mcpServers`:

```json
{ "mcpServers": { "tellus": { "command": "/Applications/Tellus.app/Contents/MacOS/tellus",
                              "args": ["--mcp"] } } }
```

`--open <file.tellus>` after `--mcp` starts the server with a project loaded, and
`--autosave <file.tellus>` saves the model after every tool that changes it.

**If the Tellus tools are not in your tool list**, you can still drive the server yourself
from a shell: it speaks JSON-RPC 2.0 on stdin and stdout, one message per line. Section 7
has a small Python client.

**First step in every session:** list the tools (`tools/list`, or look at your tool list) and
read their descriptions. They are the authority on arguments. Do not assume a tool or an
argument that is not listed.

## 4. Licence and the free academic edition

Tellus needs a licence key, activated once per computer. Without a key it runs as the
**free academic edition**, and the server's instructions say so when it starts.

| Limit of the free academic edition | Value |
|---|---|
| Mesh | up to 1,000 elements |
| Phases | the initial phase and 2 more |
| Soil models | linear elastic, Mohr-Coulomb, Soft Soil |
| Structures | up to 2 plates, 2 geogrids, 2 struts or anchors, no embedded piles |
| PLAXIS export | no |
| Reports and plots | watermarked, not for commercial use |

A tool that hits a limit returns an error that names the limit. Tell the user which limit
stopped the work, and offer a smaller model if that still answers the question. Do not try
to work around a limit.

Licence commands, for a computer without a screen:

```
tellus --licence                                   # the licence state of this computer
tellus --activate TLS-XXXX-XXXX-XXXX-XXXX-XXXX     # use a seat (needs the internet once)
tellus --deactivate                                # free the seat
```

**Treat the licence key as a secret.** Use it only when the user gives it to you for an
activation. Never write it into a project file, a script, a report, a log or a commit, and
do not repeat it back in full.

## 5. The workflow

1. `new_project` with `kind: "profile"` (borehole layers that fill the model) or
   `kind: "drawn"` (soil only inside polygons you add).
2. `set_contour` for the model width and the water level.
3. Soil materials (`update_soil_material`, `add_soil_material`), then `set_soil_layers` or
   `add_soil_cluster`.
4. Structures and loads: `add_plate`, `add_spring`, `add_anchor`, `add_geogrid`,
   `add_embedded_pile`, `add_load`, `add_fixity`, `set_boundary_conditions`.
5. Water: `add_water_level`, `set_drainage`.
6. Phases: `set_initial_phase`, `update_phase` for the first phase, `add_phase` for the next.
7. `validate`, then `generate_mesh`, then `calculate`.
8. Results: `get_results`, `get_plate_forces`, `get_curve`, `show_picture`, `write_report`.
9. `save_project`, so the user can open the model in the Tellus window.

`get_model` returns the whole model as JSON, which is also the `.tellus` file format, and
`set_model` changes any part of it. Use them for settings that have no tool of their own,
such as `mesh_size`.

### Things that go wrong, and how to avoid them

- **A new profile project is not empty.** It has two materials (Sand, Clay), two layers,
  and a strip load with index 0 that is active in the first phase. Rename and change the
  materials with `update_soil_material`, and move, reuse or switch off the load. A load
  left outside a narrower model stops meshing.
- **Phase numbers differ between input and results.** `update_phase` counts from 0 for the
  first phase after the initial phase. Results count the initial phase as 0, so the first
  phase is result phase 1.
- **A phase starts as a copy of the one before.** Give only what changes. The lists
  `plates`, `springs`, `geogrids`, `piles`, `clusters_on` and `layers_on` have one entry
  per object, in the order the objects were added.
- **Excavation and fill are soil switched off and on.** Soil to dig out must be its own
  cluster, drawn with `add_soil_cluster` and switched off in the phase with `clusters_on`.
  A fill is a cluster that is off in the initial phase (`set_initial_phase`) and on later.
- **Structures act from the phase that switches them on.** Add them with
  `active_in: "none"` and switch them on in the right phase, so a strut installed after an
  excavation stage is not loaded by that stage.
- **A failed phase stops the phases after it.** `calculate` returns `converged: false`
  with a message and the load fraction reached. Report it as a failure of the model at
  that stage, look at the deformed mesh, and do not report results of later phases.
- **A Safety phase may not reach failure.** A factor shown as "at least" or with
  `failure_reached: false` is a lower bound from the steps taken. Say so.
- **A consolidation phase takes at most 250 time steps.** Split a very long consolidation
  into several phases.
- **`get_results` at a point returns the nearest node.** Check the coordinates it reports.

### Modelling rules to follow

- **Model size.** Keep each side boundary at least 3 times the excavation depth or the
  slope height away from the structure. An embankment model is at least 3 times the base
  width wide, or 1.5 times for a half model on the centreline.
- **Initial stresses.** K0 procedure for level ground and level layers, gravity loading
  for slopes and sloping layers.
- **Undrained soil.** Undrained (A) uses effective strength. Undrained (B) and (C) use the
  undrained strength entered as `c`, with `phi` = 0. Say which one you used and why.
- **Mesh.** Start with a coarse mesh to find mistakes, then refine. Repeat an important
  result on a finer mesh and report both. A factor of safety changes by up to a tenth with
  the mesh.
- **Structures are per metre run.** Divide the stiffness and strength of a strut, anchor
  or pile by its spacing where the tool asks for a value per metre, and multiply reported
  forces by the spacing for one member.

## 6. How to report

- Give each result with its unit, its location and its phase.
- List every value you assumed that the user did not give, separately from the given data.
- State the limits of the result: model type, drainage assumption, mesh, and for a Safety
  phase whether failure was reached.
- Offer the saved `.tellus` file and a picture of the deformed mesh, because a wrong
  support or load shows there at once.
- If the request cannot be modelled in plane strain, or needs something Tellus 0.1.4 does
  not have (Section 9), say so instead of approximating silently.

## 7. Driving Tellus from a script

A script is the reliable way to run a case again, to vary a parameter in a loop, or to run
on a remote computer. This client starts `tellus --mcp` and returns each tool's answer as
a Python dictionary. Set `TELLUS_BIN` to the program's path.

```python
import json, os, subprocess

class Tellus:
    def __init__(self, log="solver.log"):
        self.p = subprocess.Popen([os.environ["TELLUS_BIN"], "--mcp"], stdin=subprocess.PIPE,
                                  stdout=subprocess.PIPE, stderr=open(log, "w"), text=True)
        self.n = 0
        self.call("initialize", {"protocolVersion": "2025-06-18", "capabilities": {},
                                 "clientInfo": {"name": "script", "version": "0"}})

    def call(self, method, params=None):
        self.n += 1
        self.p.stdin.write(json.dumps({"jsonrpc": "2.0", "id": self.n, "method": method,
                                       "params": params or {}}) + "\n")
        self.p.stdin.flush()
        while True:
            msg = json.loads(self.p.stdout.readline())
            if msg.get("id") == self.n:
                return msg

    def tool(self, name, args=None):
        res = self.call("tools/call", {"name": name, "arguments": args or {}})["result"]
        text = res["content"][0]["text"]
        if res.get("isError"):
            raise RuntimeError(f"{name}: {text}")
        return res.get("structuredContent") or json.loads(text)

t = Tellus()
t.tool("new_project", {"kind": "profile"})                 # sand over clay, a 2 m strip load
t.tool("set_contour", {"x_min": 0, "x_max": 20, "water_level": -2.0})
t.tool("update_soil_material", {"material": 0, "name": "Sand", "model": "mohr_coulomb",
                                "E": 30000, "nu": 0.3, "c": 1, "phi": 32})
t.tool("update_phase", {"phase": 0, "name": "Footing 80 kPa",
                        "loads": [{"index": 0, "active": True, "value": -80.0}]})
t.tool("add_phase", {"name": "Safety", "calc": "safety"})
print(t.tool("generate_mesh")["elements"], "elements")
for ph in t.tool("calculate")["phases"]:
    print(ph["name"], ph["converged"], ph.get("factor_of_safety"))
print(t.tool("get_results", {"phase": 1, "quantity": "uy", "points": [[10, 0]]})["values"])
t.tool("save_project", {"path": os.path.abspath("footing.tellus")})
```

This example stays inside the limits of the free academic edition.

## 8. Running on a remote computer

Use a remote Linux computer when a calculation is too long or too large for the user's own
machine, or when many variants should run unattended. Tellus 0.1.4 has a Linux build in
this repository (`linux/Tellus-0.1.4-linux-x64.tar.gz`) that runs without a screen.

**Ask the user before you start.** A remote run uses their server or rents one, may cost
money, and uses a licence seat. Get a clear yes for the server, for any cost, and for the
use of the key. Do not rent, start or stop machines on your own initiative.

### Setting up the remote computer

```
scp Tellus-0.1.4-linux-x64.tar.gz user@server:~/
ssh user@server
tar xzf Tellus-0.1.4-linux-x64.tar.gz && cd Tellus-0.1.4-linux-x64
./tellus --licence                          # shows whether this computer is activated
```

Without a key the server runs the free academic edition, which is enough for small models.
For the full program the user gives you the key, and you activate it there:

```
./tellus --activate TLS-XXXX-XXXX-XXXX-XXXX-XXXX
```

- Activation needs the internet and the system certificates (`ca-certificates`). Behind a
  firewall that inspects HTTPS it fails with "invalid peer certificate".
- "All seats of this licence are in use" means the key is active on its full number of
  computers. Tell the user. They can deactivate another computer or ask for more seats.
  Do not deactivate the user's own computer to free a seat unless they tell you to.
- **Before the remote computer is shut down or destroyed, run `./tellus --deactivate`**, or
  the seat stays taken.

### Way 1: run a script on the remote computer

This is the right way for long runs. Copy the script of Section 7 and its input files to
the server, and start it so it survives the end of the SSH session:

```
export TELLUS_BIN=$HOME/Tellus-0.1.4-linux-x64/tellus
nohup python3 case.py > case.log 2>&1 &
tail -f case.log                             # progress; solver.log has the solver's lines
```

When it has finished, copy the results back (`scp user@server:~/results.json .`) and the
saved `.tellus` file, which the user can open in the Tellus window on their own computer.
Write results to files in the script, because a long run should not depend on your
session staying open.

### Way 2: use the remote Tellus as an MCP server over SSH

The MCP server talks over stdin and stdout, so SSH can carry it. The user registers it
once, with key-based SSH login so no password prompt interrupts the stream:

```
claude mcp add -s user tellus-remote -- ssh user@server /home/user/Tellus-0.1.4-linux-x64/tellus --mcp
```

You then call the same tools, and the calculation runs on the server. Check the connection
with one cheap call, such as `new_project`, before you build a model. Files that tools
write (`save_project`, `write_report`, pictures) land on the server, so give them paths
there and copy them back. The session ends when the SSH connection drops, so use Way 1 for
anything long.

### Rules for remote work

- Keep the user informed: what is running, where, and since when.
- Do not leave a paid machine running after the work is done. Tell the user it can be
  stopped, and stop it only if they asked you to manage it.
- Deactivate the licence on the remote computer before it is destroyed.
- Do not copy the licence key, API keys or the user's project files to any other place.

## 9. What Tellus 0.1.4 does not have

Say so plainly when a request needs one of these, instead of approximating:

- Dynamic and earthquake analysis.
- Large displacements (updated mesh).
- A built-in calculation server for a cloud GPU. Remote work in 0.1.4 is done as in
  Section 8.
- Axisymmetric and three-dimensional models.
- Time-dependent groundwater flow. Steady-state flow and consolidation are available.

## 10. Where to find more

- `README.md` in this repository: installation, the licence, the assistant's API key, MCP
  setup for other clients.
- `docs/Tellus2D_user_manual.pdf`: the window step by step, structures, water and mesh,
  worked case studies, and the cases the program is calibrated on. It was written with a
  later development version, so its chapters on dynamics, updated mesh and the cloud GPU do
  not apply to 0.1.4.
- Help: tellusfem@gmail.com.
