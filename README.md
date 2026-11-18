# Wuriko

A Codex plugin marketplace for personal skills.

The marketplace catalog is `.agents/plugins/marketplace.json`. It contains
**Skills** (ID: `personal-skills`), under `plugins/personal-skills/`.

## Included skills

| Skill | Workflow |
| --- | --- |
| `yeet` | Commit and push changes, switch and sync branches, create branches, and open pull requests. |
| `git-conventions` | Name branches, write Conventional Commits, and format pull request titles, bases, and labels. |
| `personal-device-connections` | Connect to personal devices through Tailscale when available, with a clickable login link and Done confirmation for expired authentication. |

Yeet loads the bundled Git Conventions skill before deriving Git or GitHub
metadata. Plain `yeet` commits and pushes; `yeet br` also creates a branch,
`yeet pr` also opens a pull request, and `yeet br pr` does both.
Direct checkout, sync, and PR requests run only the requested actions.

## Add a skill

Ask Codex:

```text
$skill-creator Create a skill named <skill-name> under
plugins/personal-skills/skills in this repository.
It should <describe the workflow and when to use it>.
```

Each skill lives at `plugins/personal-skills/skills/<skill-name>/SKILL.md` and
starts with YAML frontmatter containing `name` and `description`, followed by
the workflow instructions. Add scripts, references, or assets only as needed.

## Use the marketplace

Add the marketplace from its Git URL:

```powershell
codex plugin marketplace add https://github.com/Wurielle/plugins.git
```

Restart the desktop app, open the Plugins directory,
select `Wuriko`, and install `Skills`. Local marketplace
support varies by client surface. Update the source files here and restart
the app to refresh the local installation after edits.

See the official [plugin packaging guide](https://developers.openai.com/plugins/build/plugins)
for marketplace discovery and installation details.
