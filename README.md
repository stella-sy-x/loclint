# loclint

**loclint** is a linter for game localization files. It reads your source and target strings, puts them back into the context they came from, and flags what a spreadsheet can't see: lost placeholders, drifting terminology, genders the source never assigned, and translations that make sense for the words but not for the screen they appear on.

loclint doesn't translate and doesn't replace your CAT tool. Translators keep working in Trados, memoQ or Phrase; loclint runs on the files they deliver, after the CAT tool's own QA, and catches what rule‑based QA can't before you ship.

---

## Who it's for

For loc leads receiving deliveries from several vendors. Run a delivery through loclint's local review app, work through the issue list with source and target side by side, **accept or reject** each suggestion, and have accepted fixes written back to the files, with a diff and a backup.

(The engine also runs headless from the command line for scripting and evals.)

---

## What it checks

### 1. Placeholders and markup
`{0}`, `%s`, `{playerName}`, `<color>` tags, `\n`: present in source, present in target, same count, plausible order. Pure rules, instant, zero API cost.

### 2. Terminology consistency
Classic term checks match strings. loclint clusters **meaning**: it finds the five different ways your source text names the same item, proposes a canonical glossary, lints the *source* for drift, and then checks that every target string pointing at that entity uses one term. Works across vendors.

### 3. Context mistranslation
A string key, a source string, a target string, and a little game knowledge. `NPC_TravellingMerchant_Departed` → "已死去" gets flagged, because the merchant leaves; he doesn't die. This is the class of error no string‑matching tool can catch, and the class LQA testers used to spend their days hunting with a spreadsheet in one window and the game in the other.

### 4. Gender assumptions
When players choose their character's gender, the source is often written gender‑neutral on purpose. Translations quietly undo it: *sibling* becomes "兄弟", the achievement *Great Parent* becomes "厉害了，我的爹", and a line with no gender in it at all ("You're so sharp you'll cut yourself") comes back as "你这么刻薄会嫁不出去的".

loclint first shortlists candidates by rule: gender‑neutral terms in the source (*sibling*, *parent*, *partner*, singular *they*…) paired with gendered terms in the target (兄弟, 爹, 他/她, 嫁…), plus target strings that add gendered wording the source doesn't have. The model then reads each candidate in context and works out who the line is about: the player, an NPC whose gender is never stated, or a character whose gender is fixed. Only the first two get flagged.

The suggested fix is a neutral rewording (手足, 家长) or, if your engine supports it, gendered variants keyed to the player's choice. Tell loclint how your game handles it:

```yaml
player_gender: selectable   # selectable | male | female
```

---

## Bring your own model

loclint doesn't ship with a model and never will. You point it at one:

- Any **OpenAI‑compatible endpoint** (OpenAI, most hosted providers, your company's gateway)
- **Anthropic**
- **Local models** via Ollama or any local server speaking the same protocol

Your key stays in your config. Your strings go from your machine to the provider you chose and nowhere else. loclint has no server of its own and nothing passes through us. Rule‑based checks need no model at all.

Switching models is one line of config, which also makes it easy to compare them on the same files.

---

## Status

🚧 **Pre‑alpha, design stage.** Nothing runs yet.

Roadmap:

1. Core engine (headless, `loclint run <dir>`) with the four checks
2. Local review app (browser‑based): issue list, side‑by‑side view, accept / reject, write‑back with backup
3. First testbed: **Terraria** (official zh‑Hans localization) → published fixes as a Steam Workshop resource pack
4. Stretch test: a very large RPG script

---

## License

TBD.
