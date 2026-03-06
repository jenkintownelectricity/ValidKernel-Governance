━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

L0 — GOVERNANCE CONTEXT

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━



Authority: Armand Lefebvre

Organization: Lefebvre Design Solutions LLC

Document ID: L0-CMD-VKG-INSTALLER-HARDENING-001

Date: 2026-03-06

Scope: Harden the VKG installer so target repositories receive the

governance kernel without inheriting source repository command history,

receipts, or active registry state by default.



Command Format: ValidKernel Command Protocol v0.1



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RING 1 — MISSION DIRECTIVE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━



Objective



Refine the VKG installer so installation remains deterministic, portable,

and governance-clean.



The installer must preserve kernel functionality while preventing source

repository history contamination in target repositories.



By default, a target repository must receive:



• governance specifications

• governance tools

• governance templates

• governance examples

• CI governance enforcement

• authority scaffold if requested

• target-native registry if requested

• target-native install receipt if requested



By default, a target repository must NOT inherit:



• source repository receipt history

• source repository active registry entries

• source repository commit lineage

• source repository governance execution state



Required Outcomes



1\. Harden Installer History Isolation



Update:



.validkernel/tools/install-vkg.py



Default installer behavior must exclude source governance history from

target repositories.



Specifically:



• do not copy .validkernel/receipts/\*.json from source

• preserve .validkernel/receipts/.gitkeep if present

• do not copy source active registry entries into target

• do not copy source repo receipt\_path references into target

• do not copy source repo command execution history into target



2\. Clean Target Registry Initialization



When installing into a target repository, default registry behavior must be:



• if target registry already exists and --force is not provided → preserve it

• if target registry does not exist and --initialize-registry is provided →

&nbsp; create a clean target-native registry

• initialized registry must contain zero prior command history unless

&nbsp; explicitly created during install



Default initialized registry content must be:



{

&nbsp; "registry\_version": "0.1",

&nbsp; "repository": "<target repository name>",

&nbsp; "last\_updated": "<install timestamp>",

&nbsp; "commands": \[]

}



3\. Clean Target Receipts Behavior



Default receipt behavior must be:



• target receipts directory created if missing

• .gitkeep preserved or created

• no source receipt JSON files copied

• if --create-install-receipt is provided, create only:



.validkernel/receipts/L0-CMD-VKG-INSTALL-001.receipt.json



No other receipt files should appear in the target repository unless they

were already present before install.



4\. Install Receipt Behavior



If --create-install-receipt is provided, the installer must create a

target-native install receipt with:



command\_id: L0-CMD-VKG-INSTALL-001

repository: <target repository name>

branch: <target git branch if available>

commit\_hash: <target git commit if available, otherwise empty or pre-commit state marker>

status: EXECUTED

gate\_result: PASS



The receipt must describe the installation event only.



It must not reference source repo command history.



5\. Target Registry Install Entry



If registry exists or is initialized, the installer must create or update

a matching target-native registry entry for:



L0-CMD-VKG-INSTALL-001



The registry entry must reflect only the install event in the target

repository.



6\. Optional Source History Mode



Add optional installer flag:



--include-source-history



Behavior:



If explicitly provided, source governance history may be copied into

clearly labeled legacy files only.



Allowed legacy artifacts:



.validkernel/registry/command-registry.source-legacy.json

.validkernel/receipts/source-history/



Default behavior must remain history-isolated.



This mode must be off by default.



7\. Deterministic Kernel Copy Scope



By default, the installer must copy only:



docs/validkernel/

.validkernel/tools/

.github/workflows/vkg-governance-check.yml



If --initialize-authority is provided and target lacks authority,

create:



.validkernel/authority/authority.json



If --initialize-registry is provided and target lacks registry,

create:



.validkernel/registry/command-registry.json



If receipts directory is missing, create:



.validkernel/receipts/



with .gitkeep only unless install receipt is requested.



8\. Post-Install Verification Expansion



After install, verify:



• docs/validkernel/vkg-spec.md exists

• docs/validkernel/command-protocol.md exists

• docs/validkernel/examples/sample-command.md exists

• .validkernel/tools/runtime-gate.py exists

• .validkernel/tools/validate-receipt.py exists

• .validkernel/tools/validate-all-receipts.py exists

• .github/workflows/vkg-governance-check.yml exists

• target receipts directory contains no inherited source receipt JSON files

&nbsp; unless --include-source-history was explicitly used

• target registry is parseable and target-native



If any required post-install condition fails, install must FAIL.



9\. Windows-Safe and Cross-Platform Encoding



Ensure installer writes all generated JSON and text files using UTF-8

without BOM when possible.



Ensure installer reads text files using UTF-8-safe behavior.



The installer must not create registry or receipt files that break

Python JSON parsing on Windows.



10\. Documentation Updates



Update:



docs/validkernel/governance-tools.md



Add or revise installer documentation to include:



• default history-isolated behavior

• source receipt exclusion

• clean registry initialization

• install receipt behavior

• optional --include-source-history mode

• overwrite rules

• example usage for clean install



11\. README Update



Update README.md to state clearly:



The VKG installer deploys the governance kernel into target repositories

without inheriting source repository governance history by default.



12\. Governance State Docs Update



Update:



CLAUDE\_LOG.md

HANDOFF.md

PROGRESS.md



to reflect installer hardening and history isolation behavior.



13\. Regression Test Requirement



Test installer behavior on a fresh git repository.



Verify:



• install succeeds

• target repo gets kernel files

• target repo gets only the install receipt

• no source receipts are copied by default

• registry is target-native

• validate-all-receipts.py passes

• runtime-gate.py passes on sample-command.md



14\. Update Runtime State



Update:



.validkernel/state/runtime-state.json



Change:



"installer": "READY"



to remain READY after hardening, and add installer hardening completion

timestamp if appropriate.



15\. Commit Changes



Create one commit containing:



• installer hardening changes

• documentation updates

• any minimal fixture or .gitkeep adjustments needed



Commit message:



feat(governance): harden VKG installer with target-native history isolation



16\. Branch



Use the current appropriate branch in the ValidKernel-Governance

repository.



Do not create a new branch unless required by repository policy.



Push to origin.



Constraints



• Python only

• standard library only

• no external dependencies

• no network fetches

• no remote cloning

• no application code changes

• no source history carryover by default

• fail closed on overwrite or invalid state



Non-goals



• no command generator implementation in this command

• no cryptographic signing yet

• no package publishing yet

• no cross-repo command execution

• no governance spec redesign



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RING 2 — DETERMINISTIC COMMIT GATE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━



Validation Checklist



\[ ] install-vkg.py updated

\[ ] installer excludes source receipt JSON files by default

\[ ] installer excludes source active registry history by default

\[ ] installer creates clean target-native registry when initialized

\[ ] installer creates only target-native install receipt when requested

\[ ] installer supports --include-source-history as optional mode

\[ ] installer preserves deterministic kernel copy scope

\[ ] installer writes UTF-8-safe files

\[ ] post-install verification expanded

\[ ] governance-tools.md updated with hardened installer behavior

\[ ] README updated

\[ ] CLAUDE\_LOG updated

\[ ] HANDOFF updated

\[ ] PROGRESS updated

\[ ] runtime-state.json updated if needed

\[ ] fresh-repo regression test completed

\[ ] regression test confirms no inherited source receipts by default

\[ ] validate-all-receipts.py passes in target repo

\[ ] runtime-gate.py passes in target repo

\[ ] commit created with required message

\[ ] branch pushed to origin

\[ ] working tree clean

\[ ] no unrelated application code changed



Gate Rule



If any item fails → HALT and report blocker.



Completion requires all checklist items satisfied.



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

RING 3 — CAPABILITY BOUND

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━



TOUCH-ALLOWED



• .validkernel/tools/\*

• .validkernel/receipts/.gitkeep

• .validkernel/state/\*

• canon/commands/\*

• docs/validkernel/\*

• README.md

• CLAUDE\_LOG.md

• HANDOFF.md

• PROGRESS.md



NO-TOUCH



• application runtime code

• unrelated repositories

• remote network operations

• governance spec redesign

• source repository history mutation

• non-governance files outside installer scope



ENFORCEMENT MODE: FAIL-CLOSED



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

EXECUTION NOTES

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━



Design intent



The VKG installer must deploy the governance kernel, not clone the

source repository’s execution history.



Target repositories should become governed repositories with clean,

repo-native history from the moment of installation.



Desired default lifecycle:



ValidKernel-Governance repo

→ install-vkg.py

→ fresh target repo

→ clean registry

→ install receipt

→ runtime gate

→ validator

→ CI



Suggested summary language



“Hardened the VKG installer so target repositories receive the

governance kernel with clean target-native registry and receipt state,

without inheriting source repository history by default.”



━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

END COMMAND

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

