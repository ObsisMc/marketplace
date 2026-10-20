# ora-space.workflow-examples

Official example workflows for Ora, delivered as a `kind = "workflow"` plugin
package. The package starts no process: installing it imports each document in
`assets/workflows/` into the workflow library as an editable workflow with a
published snapshot. The imported workflows are user data and survive
uninstalling the package.

## Documents

| Document | Shape | What it needs |
| --- | --- | --- |
| `hello-agent.json` | Start → Agent → Output | A personal default executor. The agent uses the implicit `default` slot. |
| `named-slot.json` | Start → Agent → Output | A personal `writer` slot mapping. The agent declares and references the slot. |

Both documents take one run input and pass it to the agent through a prompt
template variable; the Output node selects the agent's result. Neither document
carries credentials, deployment-specific configuration, model IDs, or
MCP/skill dependencies — executor needs are expressed only as slot
declarations. The package is universal: it ships no platform-specific
artifact.

## Installing

- **Cloud.** A space administrator explicitly installs the package from its
  plugin source. Cloud downloads the `.orax` itself, verifies its SHA-256, and
  imports every document into the tenant as editable workflows with published
  snapshots. No live workspace is required, and nothing is installed
  automatically for new spaces. Documents already imported from the same source
  are skipped on a repeat install or upgrade.
- **Desktop.** Importing the `.orax` locally installs the plugin and imports
  the documents into the local workflow library.

## Requirements and failure feedback

The first document needs only the runner's personal default executor. The
second declares the `writer` slot with a description and a display-only hint; a
runner without a mapping for it is refused at start with the slot name, the
description the author wrote, and the nodes that depend on it. Map the slot to
one of the runner's own connections and models, then start the same snapshot
again.

Running either document requires a Project: Cloud refuses a run without one
(`workflow_run_project_required`). The Project's space must resolve an OpenCode
plugin version when the run is created; a run that could not freeze one fails
its first agent node with `agent_plugin_version_unfrozen`.

## Current limitations

Cloud currently sends an agent prompt verbatim and does not interpolate
`{{#...#}}` template variables, and it does not yet write an agent's result
back into the variable pool. These documents are written against the final
contract, so end-to-end execution waits for variable passing and agent output
to land in the cloud session runtime. Until then the Output node yields an
empty result. The examples use the cloud executor-slot schema; Desktop can
import and edit them, but the intended runtime is Cloud.

## License

Apache-2.0.
