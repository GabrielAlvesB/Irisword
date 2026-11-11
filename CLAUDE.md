# IrisWord — instructions for agents

## LEIA PRIMEIRO: o IrisWord é uma cópia modificada do WordCraft

**Esta seção vale mais que todo o resto do arquivo.** O resto veio do WordCraft; onde ele contradizer esta seção, esta seção vence.

O IrisWord é uma cópia (fork) do [WordCraft](https://github.com/storytold/wordcraft), da equipe ArtCraft, adaptada para o ecossistema Iris por **Gabriel Alves Tech**. O repositório é `GabrielAlvesB/Irisword` (remote `origin`); o WordCraft original é o remote `upstream`, usado **só para trazer atualizações**: nunca envie nada para ele.

Todos os ajustes listados abaixo foram feitos de propósito. **Nenhuma atualização vinda do WordCraft pode desfazê-los.** Ao trazer uma versão nova, o trabalho é incorporar as melhorias do WordCraft **mantendo o IrisWord como ele é**. Em qualquer conflito entre o código do WordCraft e um ajuste do IrisWord, fica o do IrisWord. Na dúvida, pergunte ao dono antes de mudar.

### O que é do IrisWord e não pode ser revertido

1. **Nome visível "IrisWord"** em tudo que o usuário vê: título da janela, Sobre, mensagens, README, instaladores, pacotes. "WordCraft" só aparece nos créditos e avisos de copyright (`LICENSE-MIT`, `NOTICE`, linha de copyright do Sobre, seção de licença do README e o texto "Based on WordCraft"); esses avisos são exigidos pela licença e também não podem ser removidos.
2. **ID do app `com.iris.irisword`** (nunca `ai.storyteller.wordcraft`): `apps/wordcraft/src/main.rs` (`with_app_id`), `apps/wordcraft/src/default_apps.rs`, `packaging/linux/com.iris.irisword.*`, `packaging/macos/Info.plist.in`, os ícones em `assets/app-icon/hicolor/*/apps/`.
3. **Ícone do IrisWord** (folha com dobra e "W", gradiente `#8a79ff` → `#bd44c0`): a fonte é `assets/app-icon/wordcraft.svg`; todo PNG, `.ico` e `.icns` de `assets/app-icon/` é gerado dela (`packaging/icons.sh`, viewBox 64). Nunca aceite ícones do WordCraft por cima destes.
4. **Sem marca ArtCraft**: sem `docs/brand/`, sem logos ArtCraft, sem links para `getartcraft.com` ou `discord.gg/artcraft`, sem botões de Discord (`show_discord: false` em `crates/ui-egui/src/lib.rs`; removidos da faixa Revisão, do menu Arquivo, das dicas de teclas e das Opções; o comando `ui.discord` abre o repositório do IrisWord). Se uma atualização trouxer menções novas à ArtCraft ou à suíte Crafting Apps na interface, retire-as. A licença de marca da ArtCraft exige isso de forks.
5. **Atualizador** (`UPDATE_APP` em `crates/ui-egui/src/lib.rs`): `name: "IrisWord"`, `repo: "GabrielAlvesB/Irisword"` (exatamente assim: o atualizador compara os links diferenciando maiúsculas), `developer: "Gabriel Alves Tech"`. Nunca volte para `craft_update::suite::WORDCRAFT` puro: o app passaria a baixar o WordCraft por cima do IrisWord. A verificação ao abrir fica ligada (`set_startup_update_check(true)` em `apps/wordcraft/src/main.rs`).
6. **Instalador do Windows** (`packaging/windows/`): `UpgradeCode` `04860637-0499-49FE-8614-D5547523F3DB` e GUIDs de componentes próprios (nunca os do WordCraft, senão um instalador substitui o outro), fabricante `Gabriel Alves Tech`, pasta `Programs\Iris\IrisWord`, chaves de registro `Software\IrisWord`. Em `apps/wordcraft/build.rs`, `CompanyName` é `Gabriel Alves Tech`.
7. **Traduções**: `crates/ui-egui/build.rs` (`rebranded`) copia o catálogo do craft-i18n trocando WordCraft por IrisWord, para os textos renomeados continuarem traduzidos (a interface sai em português). Não remova.
8. **CI próprio**: `.github/workflows/ci.yml` e `.github/workflows/package.yml` são autocontidos (sem segredos, token só de leitura, actions fixadas por SHA). **Nunca restaure** os workflows do WordCraft (`storytold/craft-common-actions@...`, SonarCloud, release, FreeBSD, nix, printing, windows-arm64), nem `sonar-project.properties` ou `.coderabbit.yaml`: eles rodam código de outra organização e repassam segredos. O Dependabot roda semanalmente (`America/Sao_Paulo`).
9. **Segurança**: `zip` na versão 9 ou mais nova (`Cargo.toml` do workspace). Não volte para versões antigas (RUSTSEC-2026-0367/0368/0369/0429).
10. **Nomes internos mantidos de propósito**: crates `wordcraft-*`, binários `wordcraft.exe` e `wordcraft-cli`, arquivos `assets/app-icon/wordcraft.*`, variáveis `WORDCRAFT_*` e os nomes dos arquivos de release (`wordcraft-<versão>-windows-<arq>.msi`, `...-portable.zip`). O atualizador e o Workspace do Iris (`../Iris/src/shared/types/workspace.types.ts`) procuram exatamente esses nomes. Não renomeie.

### Como trazer uma atualização do WordCraft

1. **Não use `git fetch upstream --tags`**: as tags do WordCraft têm os mesmos nomes das nossas (`v0.15.1` existe nos dois) e sobrescreveriam as do IrisWord. Liste com `git ls-remote --tags upstream`, busque só a escolhida com outro nome e faça o merge numa branch: `git fetch --no-tags upstream refs/tags/v0.18.0:refs/tags/upstream-v0.18.0 && git switch -c update-wordcraft-0.18.0 && git merge upstream-v0.18.0`. O remote `upstream` fica com `tagOpt --no-tags` e push desativado.
2. Resolva os conflitos mantendo **tudo** da lista acima. Arquivos removidos no IrisWord (`docs/brand/`, workflows antigos, `sonar-project.properties`, `.coderabbit.yaml`) continuam removidos.
3. Procure o que entrou de novo com a marca antiga e adapte: `git grep -n -e WordCraft -e ArtCraft -e getartcraft -e discord.gg -e ai.storyteller -e "storytold/wordcraft/releases"`. Texto visível novo vira IrisWord; avisos de copyright e créditos ficam como estão.
4. Arquivos novos em `.github/workflows/` vindos do WordCraft: apague. Ícones ou imagens novas de marca: não adote.
5. Se `craft-i18n.rev` mudou, atualize `../craft-i18n` para esse commit. `craft-fonts.rev` (fontes da interface: árabe, japonês…) é nosso: use o `craft-fonts-ref` do `release.yml` da tag do WordCraft e atualize `../craft-fonts`; sem `CRAFT_FONTS_DIR` os testes de glifos árabes/CJK falham. O `zip` 9 devolve nomes de entrada como `Result`: código novo do WordCraft que use `f.name()`/`file_names()` precisa ser adaptado (sem `unwrap` fora de testes). Se a revisão de `craft-libs` mudou, confira se `craft_update::app::App` ainda aceita os campos do `UPDATE_APP`.
6. Rode `cargo audit` e os testes com as traduções e as fontes (`CRAFT_I18N_DIR` e `CRAFT_FONTS_DIR` apontando para `../craft-i18n` e `../craft-fonts`, caminhos absolutos): `cargo test --no-fail-fast -p wordcraft-doc -p wordcraft-docx -p wordcraft-formats -p wordcraft-engine -p wordcraft-control-key -p wordcraft-ui-egui -p xtask`.
7. **Versão**: a versão do IrisWord (`[workspace.package]` no `Cargo.toml`) precisa ser **maior que a da última release do IrisWord**, senão o atualizador não oferece a nova. Rode `cargo update -w` depois de mudar.
8. Mostre ao dono o que mudou antes de fazer merge na `main` e de publicar release.

### Como publicar uma release

Os instaladores saem do workflow `.github/workflows/package.yml` (roda em push de branches `update-wordcraft-*`, de tags `v*` ou manualmente): artefato `irisword-release` com `.msi`, `-portable.zip`, `.AppImage`, `.deb`, `.rpm`, `.tar.gz` e `SHA256SUMS.txt`. Ele não publica nada; os passos abaixo continuam valendo para quem gera no Windows.

1. Versão nova no `Cargo.toml`, `cargo update -w`, commit e push na `main`.
2. Com `CRAFT_I18N_DIR` definido, rode `packaging/windows/package.ps1 -Arch x64` num processo `powershell.exe` separado, com a saída indo para arquivo (no PowerShell 5.1, redirecionar dentro do mesmo processo transforma avisos do cargo em erro fatal). Saída em `dist/release/`.
3. Gere `dist/release/SHA256SUMS.txt` no formato `hash  nome` (dois espaços, sem `*`). O Workspace do Iris não instala sem ele.
4. `gh release create v<versão> -R GabrielAlvesB/Irisword --title "IrisWord v<versão>" --notes-file <notas.md> --latest` com o `.msi`, o `-portable.zip` e o `SHA256SUMS.txt`. Notas em português.
5. Os builds não têm assinatura Authenticode, então o próprio IrisWord só avisa da versão nova e abre a página da release; a instalação automática acontece pelo Workspace do Iris.

IrisWord is a clean-room, open-source, Rust-native word processor targeting Microsoft Word parity — and going further on speed, openness and agent control. It runs natively on macOS, Windows, Linux and BSD, and on the web via WASM. Siblings with the same conventions: `../photocraft` (Photoshop), `../vectorcraft` (Illustrator), `../filmcraft` (Premiere), `../lightcraft` (Lightroom), `../pdfcraft` (Acrobat), `../effectcraft` (After Effects), `../designcraft` (InDesign).

## Start every session here
1. Read `plan/STATUS.md` (current milestone, next task), then the task in `plan/execution-plan.md` and the relevant `plan/architecture.md` section. `plan/` is gitignored (local only); if it's missing, start from `ROADMAP.md`, `docs/gaps.md` and `docs/roadmap.md` (Current focus).
2. Follow the autonomous operation protocol (`plan/execution-plan.md` §5). Don't stop to ask unless a decision is genuinely the owner's (licensing, publishing, pushing to new remotes, secrets).

## Never crash
People trust IrisWord with their writing; a crash loses their work. **This outranks feature work**: never ship a feature through a panic path, and fix a crash before building on top of it.
- **No panics in non-test code:** no `unwrap()`, `expect()`, `panic!`, `unreachable!`, `todo!`, `unimplemented!`; no `unsafe` (`unsafe_code = "forbid"`).
- **Errors are `Result<T, E>`** through the crate's error type and `?`. An unfinished feature returns an error or is disabled; it never panics.
- **Input-derived numbers are hostile** (files, commands, MCP/control params): `get()` instead of `[i]`, slice strings only at char boundaries, checked/saturating arithmetic, clamp sizes, cap allocations, bound recursion.
- **Last-resort guard:** `Session::run` turns an escaped panic into an error and restores the document. It's a safety net, not a licence.
- **Prove it:** every crash fix lands with a regression test. `crates/engine/src/tests.rs::hostile_params_never_panic` runs every command with junk params — keep it green.
- Production crate roots carry `#![deny(clippy::unwrap_used, clippy::expect_used, clippy::panic, clippy::unimplemented, clippy::todo, clippy::unreachable)]`; new crates start with it and add `[lints]` with `workspace = true` to their `Cargo.toml`. Tests may `unwrap` (`clippy.toml`).

## Assets: absolutely no Microsoft (or Adobe/Avid/Autodesk) iconography or images
**This rule is non-negotiable.** Breaking it is the worst mistake you can make in this repo.
- Never copy, trace, screenshot-crop or recreate icons, images, templates, themes, clip art, fonts, cursors, sounds or any other asset from Microsoft Word/Office or any Microsoft, Adobe, Avid or Autodesk product. Not even "temporarily".
- Every asset must be **original** (drawn in code — prefer this: `crates/ui-egui/src/icons.rs`), **public domain / CC0**, **OSI open source**, or **Creative Commons that allows redistribution**, or licensed open source by a contributor who made it.
- **Every asset file has a row in [`ATTRIBUTION.md`](ATTRIBUTION.md)** (author, source, licence). `cargo xtask assets` (part of `cargo xtask ci`) fails otherwise. Add the row in the same commit as the file.
- Fonts: new font files go to [`storytold/craft-fonts`](https://github.com/storytold/craft-fonts), never this repo; it's an optional build input (`CRAFT_FONTS_DIR`). Spelling word lists and hyphenation patterns live in [`storytold/craft-i18n`](https://github.com/storytold/craft-i18n) `language-data/`, never this repo; they are embedded from `CRAFT_I18N_DIR` (pinned by `craft-i18n.rev`, which CI checks out). Without it the app builds, but English spelling falls back to an installed Hunspell English or the built-in Moby word list (with no American/British variant table) and pattern hyphenation is off (Bulgarian, Turkish and Russian still hyphenate by rule), so proofing tests need it. Documents asking for proprietary fonts (Calibri, Aptos, Cambria…) are rendered with installed system fonts or metric-compatible open substitutes (`crates/fonts/src/word.rs`) — never bundle proprietary fonts.
- Templates, sample documents and themes are our own text and colours (`crates/engine/src/sample.rs`, `cmd/design.rs`). Demo images are public domain, with sources recorded.

## Clean room
- Microsoft Word is installed on the dev machine and may be **observed** black-box: run it, use its UI with synthetic documents, take screenshots **of its window only** (by window id — the machine runs other work; never capture the whole desktop) stored only under `plan/word/screenshots/` (gitignored, never committed).
- Never read, disassemble or copy anything inside the Word bundle (file names/listings only). Never commit files produced by Word. Never copy Microsoft wording beyond feature names.
- File formats come from public specs: ECMA-376 / ISO 29500 (OOXML), the published RTF spec, OASIS ODF 1.3. Never copy GPL/LGPL/AGPL code (LibreOffice, AbiWord, pandoc…).

## Architecture (see `plan/architecture.md`)
| Layer | Crate | Job |
|---|---|---|
| L0 | `geom` | units, rects, measurement parsing |
| L1 | `doc`, `fonts` | document model + edit primitives; font DB, shaping, Word font substitution |
| L2 | `layout`, `docx`, `formats` | line breaking, pagination, tables, headers/footers, hit testing; OOXML I/O; TXT/MD/HTML/RTF/ODT |
| L3 | `render`, `pdf` | vello_cpu rasteriser; krilla PDF |
| L4 | `engine` | Session, undo, **command registry**, Word feature catalog, samples, I/O dispatch |
| L5 | `mcp` | MCP server (headless or bridged to the app) |
| L6 | `ui-egui` | Word-style egui front end (swappable) |
| apps | `apps/wordcraft`, `apps/wordcraft-cli`, `apps/wordcraft-web` | binaries |
| external | `craft-proof` (craft-libs, used as `wordcraft-proof`, sits at L1) | proofing: spelling, grammar, hyphenation; its word lists and patterns are craft-i18n's `language-data/` (`CRAFT_I18N_DIR`) |

- **Everything is a command.** User-visible behaviour = a `CommandSpec` in `crates/engine/src/cmd/*.rs` (id, label, ribbon location, shortcut, params doc, `enabled`, `run`) + tests. UI-only commands (`ui.*`) live in `crates/ui-egui/src/lib.rs`. The ribbon, shortcuts, command search, CLI, control channel and MCP all dispatch by id. Programmatic calls never open dialogs.
- **Parity is measured:** `crates/engine/src/catalog.rs` lists Word's ribbon/menu features with command ids. `cargo xtask parity` writes `docs/parity-checklist.md`; a test enforces a floor that only rises. Implement missing ids to raise it.
- **Layering** is enforced by `cargo xtask layers`. Nothing below L6 depends on egui/eframe/winit/rfd. The UI is thin and reads `Session` state; colours come from `theme::Tokens`.
- **Rust only** (no handwritten JS/TS). **Never break wasm** (`cargo xtask wasm`).

## Quality gates
Before every commit: `cargo xtask ci` (fmt, clippy -D warnings, tests, assets, layers, wasm). Commit after every arc of work that builds, with a task id in the message (`M2.1: line spacing dialog`).

## Typing behaves like Word
Typing, Enter, Backspace, Tab, lists, AutoCorrect and AutoFormat follow Word's observed behaviour in [`docs/typing-parity.md`](docs/typing-parity.md), pinned by `crates/engine/src/tests/typing.rs` (keys go through the same commands as the keyboard). Change behaviour, doc and tests together; a typing fix lands with a test there. Compare headlessly (`ui_shot`); don't drive GUIs with keystrokes on the shared machine.

## Running and looking at the app
- `cargo run --release -p wordcraft -- --sample --control 7981` (sample document + control channel).
- Drive it with JSON lines on `127.0.0.1:7981`, each with the window's key from `~/.config/wordcraft/control-key.local-7981` (macOS: `~/Library/Application Support/IrisWord/`), e.g. `{"id":1,"key":"…","method":"engine.execute","params":{"command":"text.insert","params":{"text":"Hello"}}}` then `{"id":2,"key":"…","method":"ui.screenshot","params":{"path":"/tmp/shot.png"}}`. Methods: `crates/ui-egui/src/control.rs`; docs: `docs/control-protocol.md` (Keys).
- **Headless window screenshots** (no focus stealing, works with a locked screen): `cargo run --release -p wordcraft-ui-egui --example ui_shot -- script.jsonl` (see the example's header).
- **For UI work, look at the result** (screenshot, read the PNG) and compare with Word side by side.
- CLI: `wordcraft-cli convert in.docx out.pdf`, `wordcraft-cli run --template sample --cmd 'select.text={"text":"Studio"}' --cmd format.bold --save out.docx`, `wordcraft-cli commands`, `wordcraft-cli parity`.
- MCP: `wordcraft-cli mcp` (headless) or `wordcraft-cli mcp --connect 127.0.0.1:7981` (drives the running app). See `docs/mcp.md`.
- Shell gotcha: `mv`/`cp` may be aliased interactive — use `/bin/mv -f` / `/bin/cp -f`.
- Parallel agents: separate `CARGO_TARGET_DIR` per agent; edit only the crates you own; delete your target dir when done (disk).

## Roadmap
Progress docs: `ROADMAP.md` (stage, headline numbers, progress log), `docs/target-app-parity.md` (the assessment), `docs/gaps.md` (ranked work list), `docs/roadmap.md` (milestones, Current focus), `docs/architecture.md`, and the parity checklists `docs/file-format-parity.md`, `docs/layout-parity.md`, `docs/typing-parity.md`, `docs/ui-parity.md`, `docs/hardware-parity.md`, `docs/localization-parity.md`. When work lands, update the gap, the parity doc and the progress log, and bump each touched doc's status line and revision history.

## CI workflows
- The IrisWord CI is `.github/workflows/ci.yml` alone: self-contained, read-only token, no secrets, third-party actions pinned by SHA, craft-i18n fetched at the commit in `craft-i18n.rev`. Never call `storytold/craft-common-actions` or any other organisation's reusable workflow, and never pass secrets to one (see "LEIA PRIMEIRO", item 8).
- Don't add or change CI in a feature or fix PR; a CI change is its own commit.
