# Agent Signer 0.2.0

Public distribution of the macOS Agent Signer CLI and its agent skill. The owner
approves credential use on their iPhone. This is a **staging prerelease** connected
only to https://agentcard-staging.alt.technology, not a production release.

Give your agent this versioned skill URL (or fetch it with curl):

```sh
curl --proto '=https' --tlsv1.2 -fLsS 'https://github.com/alt-research/agent-signer/releases/download/agent-signer-v0.2.0-setup.1/SKILL.md'
```

Read the returned instructions before following them. They install the pinned
archive and explain the owner's separate connection step. No private repository
checkout or login is required. The release assets include checksums and build
metadata. The package's original source links refer to a private repository;
they are provenance records, not installation prerequisites.

## Setup, once

This skill installs Agent Signer 0.2.0 from a pinned public GitHub
Release. It connects to https://agentcard-staging.alt.technology. macOS (Apple
silicon or Intel), Node.js 22+ and npm are required. No private repository access,
GitHub login or npm registry credentials are needed.

Choose a writable installation directory. The default below is `.agent-signer`
inside the current project; keep it out of Git. For an upgrade, set
`AGENT_SIGNER_PREFIX` to the absolute prefix of the existing installation.
Review these commands, then run them in one shell:

```sh
set -eu
[ "$(uname -s)" = Darwin ] || { echo 'Agent Signer requires macOS.' >&2; exit 1; }
node -e 'if (Number(process.versions.node.split(".")[0]) < 22) process.exit(1)'
prefix="${AGENT_SIGNER_PREFIX:-$PWD/.agent-signer}"
case "$prefix" in /*) ;; *) echo 'Choose an absolute installation prefix.' >&2; exit 1 ;; esac
(
  download=$(mktemp -d)
  trap 'rm -rf "$download"' EXIT
  cd "$download"
  curl --proto '=https' --tlsv1.2 -fLsS 'https://github.com/alt-research/agent-signer/releases/download/agent-signer-v0.2.0-setup.1/agent-signer-0.2.0-macos.tgz' -o 'agent-signer-0.2.0-macos.tgz'
  printf '%s  %s\n' '1979ff26435f0b94da4c80973668a3336d21cc5183ad015553d1146f91bba5f7' 'agent-signer-0.2.0-macos.tgz' | shasum -a 256 -c -
  npm install --global --prefix "$prefix" --ignore-scripts --offline --no-audit --no-fund './agent-signer-0.2.0-macos.tgz'
)
export PATH="$prefix/bin:$PATH"
"$prefix/bin/agent-signer" version
```

The archive checksum is pinned in this skill. Obtain the skill from the official
release. Installation runs no package scripts and uses no registry dependencies.
Keep the printed version and staging environment when reporting installation.
Prepend the same prefix's `bin` directory to PATH in later shells, or use the
absolute command path. An older portal-downloaded command may otherwise win PATH.

Save **this public skill** in the directory your client uses for skills. Inspect
an existing copy before replacing it, then reload skills as that client requires.
CLI installation does not update copied skills. The unchanged archive also
contains the original skill; its old portal bootstrap is superseded by this setup.

The client must permit network access to AgentCard and local credential access.
If its settings prevent that, ask the owner to configure the client; do not change
permissions automatically or treat a blocked check as a missing connection.

Connecting is the owner's step. Ask the owner to create a dedicated staging agent
key at https://cards-staging.8004scan.io/keys/ and run
`agent-signer --profile NAME connect` in their own terminal, entering the key at
the hidden prompt. Never run connect or disconnect yourself or request a key in
chat. Use the owner-selected profile; each client needs its own key. Keep the
existing iPhone wallet, pairing and grants. Installation does not change them.
For an existing profile, check status before asking the owner to reconnect.


## Release contents

- `agent-signer-0.2.0-macos.tgz`: unchanged dependency-free npm-format archive.
- `SHA256SUMS` and `release-metadata.json`: original package evidence.
- `SKILL.md`: the bundled skill with only its setup section replaced above.
- `README.md`, `distribution.json` and `DISTRIBUTION_SHA256SUMS`: public
  instructions, source identity and hashes covering the complete release assets.

The archive is JavaScript, not a bundled Node runtime or notarized app. Its
manifest supports macOS arm64/x64; actual installation acceptance must record the
tested architecture separately. A checksum does not prove phone or provider
acceptance. Publishing a CLI does not deploy its API or install an iPhone app.
Resumable requests require the matching API and migration 046; verify availability
before using them. Use the skill's read-only first check after setup. A successful
phone handover alone does not establish a successful provider response.
