![QIDI Studio logo](/resources/images/QIDIStudio.png?raw=true)

# QIDI Studio MCP Edition

QIDI Studio MCP Edition is a Windows community fork of
[QIDI Studio](https://github.com/QIDITECH/QIDIStudio) that adds a native Model
Context Protocol server. It lets ChatGPT inspect and operate the slicer through
explicit, structured tools instead of mouse and keyboard automation.

This project is not an official QIDI Technology product. For printer warranty,
hardware, account, or official QIDI Studio support, use
[QIDI Support](https://qidi3d.com/pages/warranty-policy-after-sales-support).

## Current release

- MCP integration: **v1.9.0**
- Advertised tools: **117**
- QIDI Studio target: **2.7.2.10**
- Validated platform: **Windows x64, RelWithDebInfo**
- Local MCP endpoint: `http://127.0.0.1:8765/mcp`

See [CHANGELOG.md](CHANGELOG.md) for release history and validation details.

## What the MCP edition adds

| Area | Capabilities |
| --- | --- |
| Project and models | Create, load, inspect, transform, arrange, orient, cut, split, merge, repair, and export projects and models. |
| Settings | Read and update print, filament, printer, object, and volume settings; inspect native metadata, inheritance, limits, and enum choices; preview proposed updates without mutation. |
| Slicing | Validate configurations, slice plates, inspect warnings and toolpaths, analyze the first layer, check filament quantity, and export G-code. |
| Advanced preparation | Adaptive layer height, support and seam facet painting, print-by-object ordering and clearance validation, orientation candidates, and printability analysis. |
| Printers | Discover printers, inspect capabilities and readiness, monitor jobs, capture camera frames, control the case light, and pause, resume, or cancel jobs. |
| Print workflow | Run preflight checks and use a confirmation-gated local/LAN print-start workflow. |
| Recovery and visibility | Resolve QIDI Studio recovery prompts, inspect UI state, and capture the visible application window. |
| Connectivity | Manage the Secure MCP Tunnel from QIDI Studio, including setup, status, start, stop, restart, diagnostics, logs, and dashboard access. |

## Connect QIDI Studio to ChatGPT

### Requirements

- Windows x64
- QIDI Studio MCP Edition v1.8.0 or later
- A ChatGPT account or workspace that permits developer mode and custom MCP
  connections
- An OpenAI Secure MCP Tunnel ID and Runtime API key

### 1. Configure the tunnel

1. Start QIDI Studio MCP Edition.
2. Open **MCP > Tunnel Manager**.
3. Select **Open Tunnels Page** and create or choose a tunnel.
4. Select **Open Runtime Keys Page** and create a Runtime API key.
5. Paste the `tunnel_id` and Runtime API key into the manager.
6. Select **Save / Repair and Connect**.
7. Select **Run Diagnostics** and confirm the tunnel is healthy.

The tunnel runs as a per-user Windows scheduled task and can remain available
when QIDI Studio restarts. Use the same dialog to start, stop, restart, repair,
or inspect it. Detailed companion documentation is in
[tools/mcp-tunnel/README.md](tools/mcp-tunnel/README.md).

### 2. Add the connection in ChatGPT

1. In ChatGPT, open **Settings > Security and login** and enable **Developer
   mode** if your account or workspace requires it.
2. Open [ChatGPT Plugins](https://chatgpt.com/plugins) and select the plus button.
3. Enter a name such as `Qidi Studio MCP` and a short description.
4. Under **Connection**, choose **Tunnel**, then select the available tunnel or
   enter its `tunnel_id`.
5. Create the connection and confirm that 117 tools are discovered.
6. Start a new conversation and enable **Qidi Studio MCP** from the tools menu.

Example checks:

```text
@Qidi Studio MCP call get_suite_capabilities
@Qidi Studio MCP call get_project_state
@Qidi Studio MCP call list_setting_definitions with scope print and query layer height
```

If a release changes tool names, descriptions, or schemas, open the connection
at [ChatGPT Plugins](https://chatgpt.com/plugins), select **Refresh**, confirm the
new tool count, and start a new conversation. This follows the
[official OpenAI connection workflow](https://developers.openai.com/plugins/deploy/connect-chatgpt).

## Security and operational boundaries

- The MCP server listens only on `127.0.0.1`; remote access is provided by the
  separate Secure MCP Tunnel companion.
- The Runtime API key is protected for the current Windows user with DPAPI. It
  is not stored in QIDI Studio preferences, tunnel JSON, command lines, logs, or
  source control.
- Credential values and tunnel command lines are never returned by MCP tools.
- Preview tools are read-only and explicitly report `mutated: false`.
- Model and settings mutations use QIDI Studio's native data structures and
  undo/reslice paths where applicable.
- Print start uses a prepared confirmation token plus an explicit confirmation.
  Cancel and destructive operations also require confirmation where applicable.
- Software checks cannot verify physical conditions such as plate cleanliness,
  correct filament loading, an empty build plate, or safe machine access. The
  operator remains responsible for the printer and surrounding area.

Intentionally unsupported or deferred capabilities include embedded tunnel
credentials, automatic visual failure detection and cancellation, cloud-only
print start, and operations that cannot be exposed safely through stable native
QIDI Studio state.

## Build from source

This fork follows QIDI Studio's Windows build system. From a Visual Studio 2022
developer environment, run:

```powershell
.\build_win.bat -s app -c RelWithDebInfo
```

The application is produced under `build\src\RelWithDebInfo`. Development-only
incremental rebuilds can use `-s app-dirty` after the initial dependency and
application build.

## Versioning

The MCP integration uses semantic versions independently of the upstream QIDI
Studio application version. `get_suite_capabilities` reports both the MCP suite
version and its QIDI Studio target. Tool counts in the changelog refer to the
registry advertised by the source at that release.

## Upstream QIDI Studio

QIDI Studio provides the slicer, printer profiles, device integration, and user
interface on which this fork is built. It descends from
[Bambu Studio](https://github.com/bambulab/BambuStudio),
[PrusaSlicer](https://github.com/prusa3d/PrusaSlicer), and
[Slic3r](https://github.com/Slic3r/Slic3r), with contributions from the broader
3D-printing community, including OrcaSlicer.

- [Official QIDI Studio repository](https://github.com/QIDITECH/QIDIStudio)
- [QIDI Studio wiki](https://wiki.qidi3d.com/en/software/qidi-studio)
- [QIDI homepage](https://qidi3d.com)

## License

QIDI Studio MCP Edition is licensed under the
[GNU Affero General Public License, version 3](LICENSE), consistent with
QIDI Studio and its upstream projects. Modified source distributed or operated
as a network service remains subject to the AGPL-3.0 requirements.
