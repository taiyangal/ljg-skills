# ljg-skills

My custom skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Skills

| Skill | Description |
|-------|-------------|
| **ljg-card** | Content caster — transforms content into PNG visuals (long card, infograph, poster) |
| **ljg-paper** | Paper reader — academic paper analysis pipeline |
| **ljg-plain** | Plain language rewriter — makes complex content accessible |
| **ljg-skill-map** | Skill map viewer — visual overview of all installed skills |
| **ljg-word** | English word mastery — deep-dive word deconstruction |
| **ljg-writes** | Writing engine — think through an idea by writing it out |

## Install

Copy any skill directory into `~/.claude/skills/`, then restart Claude Code.

```bash
# Example: install a single skill
cp -r ljg-plain ~/.claude/skills/

# Or clone the whole repo
git clone https://github.com/lijigang/ljg-skills.git /tmp/ljg-skills
cp -r /tmp/ljg-skills/ljg-* ~/.claude/skills/
```
