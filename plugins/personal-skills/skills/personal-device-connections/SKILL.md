---
name: personal-device-connections
description: Connect to personal computers, servers, NAS devices, or other personal devices for remote access, file transfer, or private services. Require Tailscale when available on the target and handle expired authentication with a fresh clickable login link and a Done confirmation before continuing.
---

# Personal Device Connections

## Choose the connection

- Identify the target device and requested task from the conversation and existing device or connection configuration. Ask for a device name or account only when it cannot be resolved unambiguously.
- Check Tailscale availability before selecting a connection route. Discover the local client through PATH or its installed application location; a missing PATH entry alone does not establish that Tailscale is absent.
- Inspect `tailscale status --json` when available. Match the target using its device identity, hostname, MagicDNS name, and Tailscale addresses. An absent peer is not proof that the target has no Tailscale client: authentication, a different tailnet, or access policy may hide it.
- **You MUST use Tailscale when it is installed or configured on the targeted device.** Use the target's verified MagicDNS name or Tailscale address for SSH, RDP, file transfer, or service access. An existing authorized Tailscale subnet route is also a Tailscale connection.
- Expired login, an offline target, a stopped client, or denied access does not make Tailscale unavailable. Resolve that condition or report the blocker; do not silently fall back to a LAN address, public IP, port forwarding, or another VPN.
- If the target has no Tailscale installation or configured route, use an existing authorized connection method appropriate to the task. If availability is unknown, resolve it from existing evidence or ask the user before choosing an alternative.

## Inspect authentication and reachability

- Distinguish the initiating device's login state from the target's state. A local login URL authenticates the local device, not an unreachable target.
- Check client state and authentication diagnostics before retrying a failed connection. Do not treat a daemon failure, an offline peer, or a service refusing connections as an expired login without evidence.
- Consult the installed client's help before selecting login or reauthentication flags. Use `tailscale up` or the client's supported login flow for the existing account and control server; preserve current settings.
- Capture login command output incrementally so the authentication URL can be shown immediately instead of waiting for the login command to finish. Use bounded tool waits while the process remains active.
- After authentication, use `tailscale ping <target>` when supported to check the Tailscale path, then check the requested service. A successful Tailscale ping does not establish that SSH, RDP, or another service is ready.

## Expired login or login timeout: required handoff

When a Tailscale session has expired, a login attempt has timed out, or the client requires sign-in:

1. Start or renew the supported login flow **on the device that needs authentication**. Obtain the current device-specific login URL from actual client output, its authentication state, or its UI. If a link has expired, generate a fresh one rather than reusing it. Do not fabricate a URL or substitute a generic login homepage.
2. Paste the complete URL in a user-facing chat message as a clickable Markdown link, outside a code block, using the message template below. Preserve the exact URL returned by the client, including any query parameters. Do not add laptop or device wording to the link text or prompt. Do not save authentication URLs in repository files or persistent notes.
3. **Provide a clickable choice labeled `Done` and wait for the user's submitted response.** Prefer the available `request_user_input_async` tool or the host's equivalent interactive input tool. Use the exact prompt below with `options: ["Done"]`. Show the login message in chat before invoking the input tool.
4. An asynchronous input tool returns immediately: keep the question pending and wait through the host's supported waiting mechanism. Do not continue dependent connection work, dismiss the question, or report completion while waiting. A preselected choice, an elapsed timeout, a running login command, or background authentication success is not the user's submitted confirmation. An explicit text response that login is complete also satisfies the handoff.
5. After the user submits Done or explicitly confirms login, recheck authentication on the affected device, resolve the target again, and verify the Tailscale path and requested service before resuming the original task. Done is permission to recheck, not proof of successful authentication.
6. If the link expired while the user was signing in, generate a new URL, paste the new clickable link, and present a fresh Done choice. Retry in response to observed authentication state or user input; do not repeatedly generate links while the user is still completing a pending login.

### Login message and prompt

For expired-session or timeout recovery, use this exact message, replacing only
`LOGIN_URL` with the actual fresh authentication URL. Render the message normally
in chat; the code fence here only documents the template.

```markdown
The Tailscale connection timed out.

[Log in to Tailscale](LOGIN_URL)

Complete sign-in, then select **Done** below.
```

Use this exact interactive prompt:

```json
{
  "questions": [
    {
      "title": "Finish Tailscale sign-in using the link above, then select Done.",
      "options": ["Done"]
    }
  ]
}
```

For first-time login or an explicit logout without a timeout, change only the
opening sentence to accurately describe why sign-in is required. Keep the link
text, completion instruction, and interactive prompt unchanged.

If the host has no supported interactive input tool, explain that a Done button
cannot be rendered there, ask the user to reply `done`, and wait for that response.
Do not imitate a button with HTML or claim that plain text is interactive. Use a
mode-restricted input tool only when it is available in the current mode.

If the target's login has expired and it cannot be reached through an existing
authorized management channel, explain that its login flow must be started from
that device or its local console. Ask the user to provide the URL emitted there,
then present that actual URL as a clickable link and use the same Done handoff.
Never present a URL generated on the initiating device as a login for the target.

## Preserve the existing setup

- Use the connection method already configured for the device. Standard SSH over a Tailscale address satisfies the requirement; enabling Tailscale SSH is not required.
- Do not reset settings, log out a healthy session, switch accounts, change DNS or routing, enable an exit node, publish a service, or alter access policy merely to connect.
- Forced reauthentication can interrupt an active Tailscale connection. Do not run it over the only remote access path to the affected device; use its local console or an existing independent management path.
- Keep passwords, one-time codes, and identity-provider authentication with the user. The chat handoff needs only the device-specific login link and completion response.
- Report the device and connection path used, the verification result, and any remaining service or authentication blocker. Do not claim a connection based solely on a successful login.

## Command references

Use installed CLI help first for platform and version-specific behavior. If it is
unavailable, consult the relevant official documentation:

- [Tailscale CLI](https://tailscale.com/docs/reference/tailscale-cli)
- [Connect and authenticate with tailscale up](https://tailscale.com/docs/reference/tailscale-cli/up)
- [Expired device keys and reauthentication](https://tailscale.com/docs/features/access-control/key-expiry)
