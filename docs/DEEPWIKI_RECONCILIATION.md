# CBT325 — DeepWiki reconciliation against source

Repository: `cog-demo-is/CBT325` (`https://github.com/cog-demo-is/CBT325`)
Commit inspected: `26ad1cb  Updates from cbttape.org (2025-04-15)`
Wiki baseline: DeepWiki pages for `cog-demo-is/CBT325` as generated before `.devin/wiki.json` existed (2,080 lines of wiki text).

Purpose: this file is the line-cited source of truth that `.devin/wiki.json` points DeepWiki at. Where older wiki text and this file disagree, this file wins.

## 0. Status and method

- The findings below were produced by static reading of the source, independently of DeepWiki. Regenerate the wiki (ideally at High effort, set at `https://app.devin.ai/settings/deepwiki#wiki-effort-level`) after this file and `.devin/wiki.json` are merged.
- Everything below comes from reading every file in the repo (`README.md`, `.gitattributes`, `.zigi/dsn`, `.zigi/PDS`, `cbt2git.log`, all 24 `PDS/` members) and comparing it line-by-line with the wiki text.
- Verification is **static reading only**. Nothing was assembled or executed; there is no HLASM/MVS environment here. Statements about runtime behaviour are what the source *says* it does, not what was observed.
- Line citations are `MEMBER:start-end` into `PDS/<MEMBER>` at commit `26ad1cb`; the `PDS/` members are unchanged since the repository was created. If a member is ever refreshed from cbttape.org, re-verify that member's line numbers here and in `.devin/wiki.json` before regenerating. Members are 80-column card images; columns 73–80 carry change flags (`WFB`, `SL`, `JUL`, `GTEL`, …), not sequence numbers.
- Labels used: **CONFIRMED** (wiki matches source), **CORRECT** (wiki is wrong), **QUALIFY** (wiki is true but over-generalised or missing a caveat), **ADD** (source fact absent from the wiki), **INFERENCE** (reviewer's reading, not stated literally in the source).

---

## 1. Repository / packaging facts

| # | Wiki claim | Source | Verdict |
|---|---|---|---|
| 1.1 | "…containing exactly 23 primary source, JCL, and documentation members" (cites `$$$#DATE:6-8`); `$CONTENT` is "the definitive component manifest for all 23 programs" | `$$$#DATE:8` says `23 MEMBERS COUNTED; CUMULATIVE SIZE IS 22,236 RECORDS`, but the repo has **24** files under `PDS/`, `cbt2git.log:2-25` extracts 24 members, and `.zigi/PDS` has 24 directory entries. Summing the current-size column of `.zigi/PDS` gives **22,273** lines; 22,273 − 37 (`MVSVARS`) = 22,236. `MVSVARS` is stamped `13/08/14 17:35:45` in `.zigi/PDS`, i.e. after the `$$$#DATE` ship time `14:20:36`. | **CORRECT.** State: 24 members in the repo; `$$$#DATE` counts 23 / 22,236 records because it predates the addition of `MVSVARS` (INFERENCE from the arithmetic and timestamps, but exact). `$CONTENT` (dated 1987) lists only the original 1986 Wells Fargo items; it does not describe `$PRINTOF`, `PRINTOFX`, `PRINTOF$`, `PROF$ZAP`, `MVSVARS`, `PRINTOLD` or `@FILE325`. |
| 1.2 | Dataset attributes `FB 80 5600 PO` (from `$$$#DATE`) and, on the tooling page, `.zigi/dsn` = `PDS P0 FB 80 32720` | Both are in source: `$$$#DATE:6-7` and `.zigi/dsn:1`. | **QUALIFY.** They are two different datasets: `SBGOLOB.CBT487.FILE325` as shipped in 2013 (BLKSIZE 5600) vs. the zigi-managed target PDS (BLKSIZE 32720). The wiki presents both without saying they differ. |
| 1.3 | "administrative sequence and member identifiers reside in columns 73 to 80" (cites `$CONTENT:5-7`) | `$CONTENT:5-7` reads: `THIS DATASET IS NOT NUMBERED.  THERE IS DATA IN COLUMNS 73 TO 80` (spaced letters). The 73–80 data is change/ownership flags (`WFB`, `SL`, `TP01`, `CN02`, `JUL`, `GTEL`, `FILE 325`), not sequence numbers or member IDs. | **CORRECT.** Reword as: members are unnumbered; do not renumber, columns 73–80 hold change-tracking flags. |
| 1.4 | CBT "Version 487" | `$$$#DATE:1` says `REGULAR CBT TAPE - VERSION 487`; `cbt2git.log:1` says the XMI unpacked was `CBT.V505.FILE325.PDS` (tape version 505 on 2025-04-15). `SETSSI CB487325` in `PRINTOF$:26` / `PRINTOFX:2912` encodes tape 487 / file 325. | **QUALIFY.** The member contents date from the V487 build (2013-08-14); the Git snapshot came from the V505 distribution. |
| 1.5 | `README.md` is cited (`README.md:17-27`, `29-43`, …) as a component description | `README.md` embeds the `@FILE325` catalog text (lines 6 onward). Its line 10-11 refers to `THE MEMBER CALLED $$DOC` — **no `$$DOC` member exists** in the repo. | **ADD** the dangling `$$DOC` reference. |
| 1.6 | `.gitattributes` "enforce proper EBCDIC/ASCII code page translation" | `.gitattributes:1-3`: `git-encoding=iso8859-1 zos-working-tree-encoding=ibm-1047` for `*`, but `iso8859-1` for `.gitattributes` and `.gitignore`; binary for doc/pdf/ebook extensions. | **CONFIRMED**, add the exception lines. |
| 1.7 | `MVSVARS` is presented as "configuring installation-wide variables via the MVSVARS member" and as a build prerequisite ("Building … requires configuring installation-wide variables via the MVSVARS member") | `MVSVARS:1-37` is a saved 2007 mailing-list email (Robert Zenuk) containing a 3-line REXX (`say mvsvar(var)`) and a 9-line `VARS.SH` that exports `SYSNAME`, `SYSCLONE`, `SYSPLEX`. Nothing in the repo references it; no build uses it. | **CORRECT.** It is an informational note, not a build dependency and not "installation-wide configuration". Keep the description of the technique but remove the prerequisite wording. |
| 1.8 | `.zigi/PDS` "tracking creation and modification dates, version/modification levels, current size, initial size, and user IDs (e.g., FILE325, SBGOLOB, UPDATED)" | Columns match. Useful provenance in the last column: `FILE325` (1987 originals), `CBT-487` (`$$$#DATE`, `@FILE325`), `SBGOLOB` (`$PRINTOF`, `MVSVARS`), `ASMJCL` (`PRINTOF$`), `UPDATED` (`PRINTOFF`), `NEW-Y2K` (`PRINTOFX`), `OLDVERS` (`PRINTOLD`), `ZAPLMOD` (`PROF$ZAP`). | **CONFIRMED / ADD** the label meanings — they encode which members are original vs. 2013 additions. |

---

## 2. PRINTOFF family (`PRINTOFF`, `PRINTOFX`, `PRINTOLD`, `PRINTOF$`, `PROF$ZAP`, `$PRINTOF`)

### 2.1 Which member is which (wiki page "PRINTOFF … Y2K" and build page)

| Member | What it is (source) | Cite |
|---|---|---|
| `PRINTOFF` | Current, **preferred** source. Header date call replaced by `CALL JULSUB` (`PRINTOFF:725`, list form `CALLMFJ` at `:2506`). Bill Godfrey's public-domain `JULSUB` is appended as its own CSECT (`PRINTOFF:2825-2827`, `JULSUB AMODE 24 / RMODE 24 / CSECT`). 3,354 lines. | `$PRINTOF:11-12`, `:66-79` |
| `PRINTOFX` | **First** Y2K fix: complete JCL job (`//SBGOLOBU JOB`, `PRINTOFX:1-33`) with the source in-stream, plus disassembled `IKJEFLPA` (`:2763`) and `IKJEFLPB` (`:2882`, `DC X'20',C':'`) so the century byte is already `X'20'`. Still calls `IKJEFLPA` (`PRINTOFX:754`). 2,917 lines. | `$PRINTOF:11-13`, `PRINTOFX:6-8` |
| `PRINTOLD` | Original 1986 source; calls `IKJEFLPA` (`PRINTOLD:696`); no `JULSUB`. 2,704 lines. | `.zigi/PDS` label `OLDVERS` |
| `PRINTOF$` | Sam Golob's minimal assemble+link JCL for `PRINTOFF`: `ASMA90` with `SYSLIB = SYS1.MACLIB, SYS1.MODGEN, SBGOLOB.B.ASM`, `SYSIN = SBGOLOB.B.ASM(PRINTOFF)`; `HEWL` with `NCAL,MAP,LIST,LET`; `SETSSI CB487325`, `ALIAS PRINTO,PROFF,PO`, `NAME PRINTOFF(R)`. | `PRINTOF$:1-29` |
| `PROF$ZAP` | `AMASPZAP` job: `NAME PRINTOFF IKJEFLPB / VER 0000 19 / REP 0000 20`. Only meaningful against a load module that still contains the IBM `IKJEFLPB` CSECT (i.e. a `PRINTOLD`/`PRINTOFX`-style link). | `PROF$ZAP:9-17` |
| `$PRINTOF` | Narrative: PRINTOFF came from IPO/CBIPO as an "unsupported" program and is the ancestor of IBM's `PRINTDS` (`:6-21`); IBM later **updated** `IKJEFLPA/B` so the new versions are "not compatible with this program's CALL" and the 1986 source "is not reassemblable on current systems" (`:59-66`); describes both fixes and says use `PRINTOFF` (`:30-51`). | `$PRINTOF:1-79` |

Wiki verdicts:

- **CONFIRMED:** the two-fix story, `X'19'`→`X'20'`, the preference for `JULSUB`.
- **CORRECT:** the overview page says `PRINTOFF / PRINTOFX` are both "Enhanced printing" tools. `PRINTOFX` is not a separate tool; it is a JCL job that builds an alternative `PRINTOFF` load module (named `PROFF`, alias `PRINTOFF`). Likewise `PRINTOLD` is never mentioned by the wiki — it should be documented as the unfixed original.
- **QUALIFY:** the wiki (build page §2) says `PROF$ZAP` is "for environments unable to immediately reassemble modules from source". The source is narrower: the zap fixes the *century byte only*, and only works if the load module contains `IKJEFLPB` — it does not address the incompatibility with IBM's current `IKJEFLPA/B` that makes the 1986 source unassemblable (`$PRINTOF:59-66`). The zap and `PRINTOFF` (`JULSUB`) are alternatives, not layers.
- **ADD (build differences worth a table):**
  - `PRINTOF$` link: `HEWL`, `NCAL,MAP,LIST,LET`, no `SETCODE`, output `NAME PRINTOFF(R)` with aliases `PRINTO,PROFF,PO`.
  - `PRINTOFX` link (`PRINTOFX:2909-2917`): `IEWL`, `ORDER PRINTOFF,PARMTAB,MESSAGES,VALDEST,IKJEFLPA,IKJEFLPB`, `SETSSI CB487325`, `MODE RMODE(24),AMODE(24)`, **`SETCODE AC(1)`**, `ENTRY PRINTOFF`, `ALIAS PO,PRINTO,PRINTOFF`, `NAME PROFF(R)`. The assembler step uses `PARM=(DECK,NOOBJECT,NORLD,RENT,TERM,'XREF(SHORT)',FLAG(5))` (`PRINTOFX:13-14`).
  - Neither `PRINTOFF` nor `PRINTOLD` uses `MODESET`/`TESTAUTH`; the `AC(1)` in `PRINTOFX` is therefore installation choice, not a source requirement (INFERENCE).
  - `JULSUB` uses `SAVE (14,12),,JULSUB_&SYSDATE._&SYSTIME` (`PRINTOFF:2828`) — underscore in an ordinary symbol, so `PRINTOFF` as shipped assumes HLASM (`ASMA90`), consistent with `PRINTOF$:7`. `PRINTOLD` is the only version that plausibly assembles under Assembler H (INFERENCE).
  - Target library in both `PRINTOF$` and `PROF$ZAP` is `SYS1.W$$.LINKLIB` — a Sam Golob test library, not a Wells Fargo or IBM name; installers must change it. `PRINTOFX:31` marks `OUTPUT='SBGOLOB.LOAD'` with `<=== CHANGE`.
- **ADD (functional facts the wiki page omits):** CSECTs are `PRINTOFF` (`:200`), `MESSAGES` (`:2245`), `VALDEST` (`:2394`), `JULSUB` (`:2827`), plus `PARMTAB IKJPARM` (`:2072`). `RECDATA DS CL32760` (`:2616`) is the record buffer. HELP text is bracketed by `AGO .ENDHELP` (`:2619`) / `.ENDHELP ANOP` (`:2744`), i.e. it is carried in the source but skipped at assembly and must be installed separately as TSO HELP.

### 2.2 `NOTE` → `PRINTOFF` coupling

`$CONTENT:72-90` says NOTE uses "a modified PRINTOFF" because PROFS could not decipher TRANSMIT control tags. `NOTE:450` and `:467` issue `PRINTOFF * DD(PDFTEMP&SCREEN) DEST(...) CLASS(B) NOHEADING NOMSG`. The wiki states this correctly; **ADD** that the "modified" PRINTOFF is the one in this file (the `WFB`-flagged changes in `PRINTOFF`, e.g. the `DDNAME` operand `PRINTOFF:2141`, `:2230`) — `NOTE` depends on the `DD()` keyword which stock IPO PRINTOFF lacked (INFERENCE from the `WFB` flags on those lines).

---

## 3. `DARTH` (1,278 lines)

| Wiki | Source | Verdict |
|---|---|---|
| Expansion "Dump Activity Reporter / Tape Handler" (overview, glossary) and "Damage Assessment Routine Tape Handler" (component page) | Both exist: `$CONTENT:12` / `@FILE325:10` use the former; the `TITLE` statement `DARTH:115` says `'DARTH - DAMAGE ASSESMENT ROUTINE TAPE HANDLER'` (sic). | **QUALIFY:** give both, note the catalog vs. code discrepancy. |
| Runs as started task, wakes every 15 min, `F DARTH,X` wakes it | `DARTH:1-22`; sleep interval `DC C'00150000'` (`:683`), `STIMER REAL … DINTVL=TIMELIMT` + `WAIT 1,ECBLIST=TWOECBS` (`:305-306`). At WFB started as `S DARTH.VADER` (`:3`). | **CONFIRMED.** |
| DDNAME convention `DARDMPxy` | `:25-32`: char 7 = system id, char 8 = dump dataset number; control member name `SDDDHHMM` (`:34-48`), collision resolved by incrementing minutes. | **CONFIRMED.** |
| Automatic purge / `SUSPEND` / `RESUME` | `:52-57`: "AT PRESENT, OLD MEMBERS MUST BE MANUALLY PURGED"; the `SOME FUTURE REQUIREMENTS CURRENTLY UNDER CONSIDERATION` list (`:95-112`) names `F DARTH,SUSPEND`, `F DARTH,RESUME`, automatic purge, and reading `PARM=` — all **not implemented**. | **QUALIFY** wherever the wiki lists these without marking them as future work. |
| Subroutines `DARGETDD`, `DARBLDCB`, `DARCNVRT` "linked into UTDARTH" | Main CSECT `UTDARTH` (`:114`); `DARBLDCB CSECT` (`:894`), `DARCNVRT CSECT` (`:976`), `DARGETDD START` (`:1062`) are all **in the same member**. Comment `:121` spells it `DARCONVRT` while the CSECT and `INCLUDE` card (`:134`) use `DARCNVRT`. | **CONFIRMED**; **ADD** the spelling discrepancy and that the sample link JCL `:127-139` assumes separately assembled object modules. |
| Error handling | User ABENDs (in `DARGETDD`): `004` (no `DARDMP__` DDs, `:1150-1151`), `008` (duplicate DD), `016` (>99 dump datasets); system-style `ABEND 600/605/610/670` on `QEDIT` failures, `620` EODAD on `CONT1`. Dump record 1 must start `X'FE'` (XA dump indicator) or DARTH falls back to current TOD (`:58-61`). ENQ on the "DEATHSTAR" resource (`:219-222`, V1.4) prevents two copies. `DYNALLOC` error `X'0210'` (DSN ENQ) → wait 2 min and retry (`:596-616`, `:631-637`). Hard-coded `SYS3.DARCNTRL` (`:748`). | **ADD** — none of this is in the wiki. |
| Control member layout | Two 80-byte records: `SDDDHHMM VVVVVV CPUID=… | first 50 bytes of title` / `TIME: hh.mm.ss DATE: yy.ddd | next 50 bytes` (`:65-70`). Note the 2-digit year `86.216` — DARTH itself is not Y2K-clean in its member naming/records (INFERENCE). | **ADD.** |

---

## 4. `VTAMCHK` (813 lines)

- **CONFIRMED:** started by `COMMNDxx`; `PARM='DELAY=xx,COL=xx'`; `;DELAY=`, `;COL=`, `;;` escape (`VTAMCHK:3-39`).
- **ADD:** VTAM-up check is an `OPEN` of `ACB AM=VTAM,APPLID=APPLID` where `APPLID DC AL1(8),CL8'VTAMCHK'` (`:783`, `:805`) — **the installation must define a VTAM APPL named `VTAMCHK`**. Wait between attempts is `ACBWAIT DC A(100*5)` = 5 s (`:787`, `STIMER WAIT,BINTVL=ACBWAIT` `:607`). Retryable ACB error codes are 92, 90, 20, 82, 88, 188 (`:582-593`); any other code → `VTAMCHK21I` + `ABEND 200` (`:594-602`); OPEN RC > 8 → `VTAMCHK20I` + `ABEND 100,DUMP` (`:576-579`).
- **CORRECT (retry window):** the comment `LA R4,36  36 X ACBWAIT (5 SEC) = 3 MIN TOTAL` (`:571`) states the intent, but the code does not implement it. The only counter test is `BCT R5,A00190` (`:604`), reached only for error 90 (APPLID inactive); `R5` is never initialised on that path and `R4` is never decremented or tested. Errors 92/20/82/88/188 branch straight to the wait (`A00190`) and retry every 5 s indefinitely; error 90 exhausts whatever `R5` happens to hold. Do **not** publish a guaranteed 3-minute window — describe it as "intended 36 × 5 s, not implemented (R4/R5 counter defect)".
- **ADD:** return codes: `12` for SYSIN open failure / RECFM not F or FB / LRECL > 140 (`:552-565`, messages `VTAMCHK01I/03I/04I`); `8` for invalid `DELAY=`/`COL=` (`:673-706`, `VTAMCHK02I`); `4` when a command is skipped because its length exceeds 130 (`:647-648`, `VTAMCHK05I`). Commands are issued with `SVC 34` (`:728`) under `MODESET KEY=ZERO` (`:535`) / `MODESET KEY=NZERO` (`:743`), so VTAMCHK must run APF-authorized.
- **QUALIFY:** the wiki's "Assembly and Macro Framework" section attributes a `BEGIN` housekeeping macro with reentrant `GETMAIN` (`VTAMCHK:43-188`). The macro is defined in-line in the member; it is not an IBM macro and other members (`ROOM`, `OPCON`) do not use it. Say "local in-member macro".

---

## 5. `IEFUTL` (497 lines)

- **CONFIRMED:** SMF time-limit exit (`$CONTENT:21-36`), ACF2 traversal (`ACFGACVT R4` `IEFUTL:229`, `ACFGUCB R4` `:232`, `USING LIDREC,R4` `:245`, `LIDREC` DSECT `:496`), TSB `TSBTRMID` used for the LU name instead of the ACF2 in-storage record (`MVC SVC34LU,TSBTRMID` `:241`; rationale in the header `:1-19`), conditional VTAM disconnect command via SVC 34.
- **QUALIFY the extension numbers.** The wiki diagram says CPU "Extend 5 mins max 6 times" and wait "Extend 30 mins max 6 times". Source:
  - CPU: `LA R3,60*5` (+5 min) with `C R1,SIX` / `SIX DC F'6'` (`IEFUTL:285-289`, `:410`) for production/privileged batch — **confirmed**. TSO sessions are extended "indefinitely" on CPU expiry (`:38-39`, `:56`); test (`Z`) jobs by non-privileged users are cancelled (`:36-37`).
  - Wait (batch): the code extends by **one SMF JWT increment** (`L R3,SMFWTIME`, `:365`) up to 6 times (`:358-362`). The header is self-contradictory: `:49-50` says "5 MINUTES UP TO 6 TIMES … (I.E. 3.5 HOURS)" and `:68` says "30 MINUTES UP TO 6 TIMES". Neither 5 nor 30 is in the code; the increment is whatever JWT the SMF parms specify (converted from 1.048576-s units, `:311-323`). Publish "JWT × up to 6" and drop the 30-minute figure.
  - Wait (TSO): total idle allowed comes from the ACF2 LID field (`TSOIDLE`, `SMFWORKH`), **capped at 90 minutes** (`CLI SMFWORKH,X'5A'` … `LA R6,90`, `:331-334`); each pass extends by one JWT (`:383-384`), or JWT + 1 minute after the conditional-disconnect SVC 34 (`:385`). Extension counter is reset when the previous time-out is not continuous (`:300-307`).
  - `TSOMSG1 'IEFUTL - CPU TIME EXPIRED, TSO SESSION EXTENDED'` (`:417`) is TPUT to the user.
- **ADD (dependencies):** ACF2 macros `ACFGACVT`, `ACFGUCB` (with `SYS=AOS2`) and the ACF2 `LIDREC` DSECT must be in `SYSLIB`; installation-defined LID field `TSOIDLE` (`:2-3`). This exit cannot assemble at a non-ACF2 site without rework.
- **QUALIFY:** the wiki describes it generically as "MVS/XA 2.1.5 SMF Time Limit Exit". It is specifically the *Wells Fargo* `IEFUTL` with ACF2-based privilege classes and hard-coded site conventions (job-class tests); it is not a drop-in exit.

---

## 6. `INMXZ01` / `INMXZ02` (257 / 274 lines)

- **CONFIRMED:** `INMXZ01` validates local TSO recipients against ACF2 and drops invalid ones, always RC 0; `INMXZ02` writes the `$HASP549`-style delivery notice via `SVC 34` under `MODESET KEY=ZERO … KEY=NZERO`, always RC 0.
- **ADD:** `INMXZ01` header requires link-edit `INCLUDE` of `ACF$GCVT` (aka `$ACFGCVT`, from `SYS3.ACF2.V###.ACFMOD`) and **PTF `UZ39974`** (or equivalent) (`INMXZ01:19-23`, `:48`) and documents attributes `KEY 8, PROBLEM STATE, APF ON, REENTRANT` (`:40`). `INMXZ01` contains no `MODESET`; only `INMXZ02` does the `KEY=ZERO`/`KEY=NZERO` transition (`INMXZ02:160`, `:164`). Both exits exist to compensate for TSO/E TRANSMIT behaviour of that era; on current z/OS the exit interfaces still exist but the ACF2 macro dependencies remain installation-specific.

---

## 7. `JESMAXCC` (440 lines)

- **CORRECT:** the wiki centres on JES2 exit 016 only. `$CONTENT:63-71` already calls JESMAXCC "a pair of JES2 (SP2.1.5) exits" that append `MAX COND CODE nnnn` / `ABENDED USER xxx` / `CANCELLED SYSTEM 222` to `$HASP165`. The member contains **two** independent exit bodies: `EXIT016 TITLE 'WFB - EXIT 016 - MAX COND CODE/ABEND CODE SUPPORT'` and `EXIT252 TITLE 'WFB - EXIT 252 - MAX COND CODE/ABEND CODE SUPPORT'` (with `UEXIT252`/exit 008 handling). Exit 252 scans the SCT chain and stores the maximum condition code / abend code in `JCTUSER1`; exit 016 consumes `JCTUSER1` to put it on the job's output/notify. Document both and the `JCTUSER1` contract between them.
- **ADD:** because it writes `JCTUSER1`, it conflicts with any other installation code using that field; the exit numbers (252 = installation-defined range) mean the `EXIT(252)` `JES2PARM` definition and `$EXIT` load are site-specific.

---

## 8. `JESLOGON` (319 lines)

- **CONFIRMED:** prompts for a subsystem name, builds SSIB/SSOB, issues "Request Job ID", swaps `JSCBSSIB`, attaches `IKJEFT01` (or `ADFMDF03` when Session Manager is requested), restores on TMP exit, passes TMP RC back (`JESLOGON:7-36`).
- **ADD (restrictions, `:38-44`):** no `SYSOUT` DDs allowed in the logon proc (013 abend when a secondary subsystem accesses them); the job name seen by the secondary JES2 is `SYSLOG`. Uses `MODESET MODE=SUP` / `EXTKEY=ZERO` (`:108-234`) → must be APF-authorized and named as the logon proc `PGM=`. Origin credited to Jim Vinson / George DeLano / Mike Shannon (IBM) (`:46-49`).

---

## 9. `NOTE`, `NOTEIMAC`, `NOTEHELP`, `WFBNOTE`

- **CONFIRMED:** CLIST `NOTE` (`PROC 0 PROLOG(YES) EPILOG(YES) COPYLIST(YES) PROFNODE(EMINFO) DEBUG`, `MAXNICK 15`), panel `WFBNOTE`, edit macro `NOTEIMAC` (`PROFSFORMAT` reformatting, `X'FDFDFD'`→`X'000000'` cleanup), help `NOTEHELP`.
- **ADD:** work file `ALLOC DD(PDFTEMP&SCREEN) REUSE UNIT(VIO) TRACK SPACE(1) DSORG(PS) RECFM(F B) LRECL(80) BLKSIZE(9040)` (`NOTE:27-28`); nickname file `NAMES.TEXT` (`:37`, warning at `:39-41` if unallocatable); delivery via `PRINTOFF * DD(PDFTEMP&SCREEN) DEST(node.user) CLASS(B) NOHEADING NOMSG` — so NOTE depends on this file's `PRINTOFF`, on a JES2 `DEST` routing to the PROFS node, and on `PROFNODE(EMINFO)` being a real node at the site. `SYSOUT CLASS(B)` is a site convention.
- **QUALIFY:** the wiki describes NOTE as "electronic mail delivery". It is more precisely a PDF edit-based front end that prints formatted text to a JES2 destination that PROFS (VM) picks up; there is no SMTP/TRANSMIT path.

---

## 10. `OPCON` (1,672 lines)

- **CONFIRMED:** renamed from `SPY` (`OPCON:27`); requires APF (`:75-77`: "SPY MUST, ALAS, BE APF AUTHORIZED … PLACE THE 'SPY' COMMAND IN IKJEFTE2 AND/OR IKJEFTE8"); any operator command can be entered, guarded by a 3-character password in `VIPWORD` (`:4`, `:82`).
- **QUALIFY:** ASID of the console address space. Change log records `SET CONASID TO 6 FOR XA (S/370 CONASID IS 5)` (`:54`) and `V3.3.1 - CHANGE CONSOLE ASID TO 7 DUE TO CATALOG ASID W/ DFP V2` (`:39`); the active code is `CONASID EQU 7`, loaded and set as secondary ASID via `SSAR` (`:369-370`, `:547`). The wiki should say the value is release-specific and hard-coded, and is certainly wrong on current systems (CONSOLE ASID is not fixed).
- **ADD:** the program reads operator console buffers/control blocks in fetch-protected storage — this is intrinsically release-dependent MVS/XA internals and should be flagged as not portable rather than documented as a general facility.
- **ADD:** the command name in the source is still `SPY` in messages/comments; `OPCON` is the CBT member name (`$CONTENT:91-124`).

---

## 11. `ROOM` (491 lines)

- **CONFIRMED:** lists or modifies `JCTROOMN`; exists because WFB accounting information overlays the room field.
- **ADD:** `MODESET KEY=ZERO` / `KEY=NZERO` around the JCT store (`ROOM:343-345`) → must run APF-authorized (authorized command table). Not stated by the wiki.

---

## 12. `VTOCLIST` (5,986 lines)

- **CONFIRMED:** multi-CSECT Assembler-H-era utility; CSECTs include `XVTCREAD`, `XDATEDIT`, `XPRNTSUB`; handles 3380-E and >16 extents.
- **QUALIFY:** the wiki calls VTOCLIST a "dataset utility" alongside ROOM. It is a VTOC lister/reporter (the biggest member), with its own print macros and separate-assembly fallback instructions in the header; it deserves its own page with the CSECT map and the return-code branches.

---

## 13. Build / link-edit / installation dependency table (ADD as a wiki section)

| Member | Assembler assumed | Non-IBM / site macros & DSECTs | Authorization | Other site dependencies |
|---|---|---|---|---|
| `PRINTOFF` | HLASM (`ASMA90`; underscore symbol in `JULSUB`) | none beyond `SYS1.MACLIB`/`MODGEN` | not required by source; `PRINTOFX` links `AC(1)` | HELP member must be installed separately; target lib names are test names |
| `PRINTOFX` | HLASM (`ASMA90`) | embeds `IKJEFLPA/B` disassembly | `SETCODE AC(1)` | `OUTPUT='SBGOLOB.LOAD'` must change |
| `PRINTOLD` | Assembler H or HLASM | needs IBM `IKJEFLPA/B` at link | — | fails on TSO/E levels without those modules |
| `PROF$ZAP` | — (`AMASPZAP`) | — | — | target `SYS1.W$$.LINKLIB(PRINTOFF)` with `IKJEFLPB` CSECT |
| `DARTH` | Assembler H | none | started task; ENQ + `QEDIT` | `SYS3.DARCNTRL`, tape unit names, `DARDMPxy` DDs |
| `VTAMCHK` | Assembler H | VTAM `ACB`/`SHOWCB` macros | `MODESET KEY=ZERO` (`:535`) around SVC 34 → APF | VTAM `APPL VTAMCHK`, `SYS3.PARMLIB(member)` |
| `IEFUTL` | Assembler H | ACF2 `ACFGACVT`, `ACFGUCB`, `LIDREC`; `TSOIDLE` LID field | SMF exit (key 0) | ACF2 installed; site job-class rules |
| `INMXZ01` | Assembler H | ACF2 `ACF$GCVT`; PTF `UZ39974` | APF ON, key 8, problem state (header `:40`; no `MODESET`) | ACF2 |
| `INMXZ02` | Assembler H | JES2 `$HASP549` text | `MODESET KEY=ZERO` | JES2 |
| `JESMAXCC` | JES2 `$` macros | `JCTUSER1` use | JES2 exits 016 and 252/008 | `EXIT(252)` definition in JES2PARM |
| `JESLOGON` | Assembler H | SSIB/SSOB/JSCB mappings | `MODESET MODE=SUP` → APF | logon proc `PGM=JESLOGON`, no SYSOUT DDs |
| `OPCON` | Assembler H | console control-block layouts (XA) | APF; `IKJEFTE2/8` | `CONASID EQU 7`, `VIPWORD` |
| `ROOM` | Assembler H | `JCT` mapping | `MODESET KEY=ZERO` → APF | — |
| `VTOCLIST` | Assembler H | in-member print macros | — | 3380-E era device tables |
| `NOTE` etc. | CLIST/ISPF | — | — | `PRINTOFF` with `DD()` support, PROFS node `EMINFO`, `NAMES.TEXT`, class B SYSOUT |

---

## 14. Historical vs. current-system caveats (ADD one wiki paragraph)

- All 1987-stamped members target **MVS/XA 2.1.5, JES2 of that era, ACF2, VTAM, ISPF/PDF** (`$CONTENT:1-3`). Control-block offsets used by `OPCON` (console ASID, fetch-protected buffers), `IEFUTL` (TSB/ACF2 fields) and `JESMAXCC` (`JCT`/`SCT` via `$` macros) are release-specific; none should be described as working on current z/OS without re-assembly against current macros and review.
- Only the **PRINTOFF** line was touched for z/OS (2013): `@FILE325:8` "(Note: PRINTOFF was fixed for z/OS.)". Everything else is as of September 1986.
- `DARTH` writes two-digit years into control members and assumes the XA dump header (`X'FE'`).
- Site-specific names throughout: `SYS3.*` datasets, `SYS1.W$$.LINKLIB`, `SBGOLOB.*`, `EMINFO`, `DARTH.VADER`, WFB job-class conventions.

---

## 15. Wiki statements not supported by source (remove or mark as unverified)

1. `IEFUTL` "Extend 30 mins max 6 times" for wait — the 30 comes from a header comment (`IEFUTL:68`) that contradicts another header line (`:49-50`, "5 minutes … 3.5 hours") and the code (`:365`, one SMF-JWT increment). The "5 mins × 6" CPU figure is correct. See §5.
2. "`MVSVARS` … required for building" — not supported (see 1.7).
3. "exactly 23 members" — contradicted by the tree (see 1.1).
4. "administrative sequence and member identifiers in columns 73–80" — contradicted by `$CONTENT:5-7` (see 1.3).
5. `PRINTOFX` as a distinct printing tool — it is a build job for an alternate `PRINTOFF` (see 2.1).
6. Any implication that `DARTH` supports `SUSPEND`/`RESUME`/automatic purge — future-requirements list only (see 3).

---

## 16. Suggested page-level actions

1. Overview: fix member count; add `PRINTOLD`, `$PRINTOF`, `PRINTOF$`, `PROF$ZAP`, `MVSVARS` to the inventory with their `.zigi/PDS` provenance labels; add the missing-`$$DOC` note.
2. Packaging page: split "as shipped (FB 80 5600, V487, 2013)" from "zigi target (FB 80 32720)" and from "V505 XMI extracted 2025-04-15"; correct the columns-73–80 statement.
3. Build page: replace the `MVSVARS` prerequisite with an informational note; add the `PRINTOF$` vs `PRINTOFX` link-card comparison and the `AC(1)` / HLASM observations; scope `PROF$ZAP` correctly.
4. PRINTOFF page: add the three-version table (§2.1), CSECT list, HELP-skip mechanism, `NOTE` dependency on `DD()`.
5. DARTH page: add ABEND/ENQ/retry behaviour and control-record layout; mark future requirements as unimplemented; note both name expansions and the `DARCNVRT`/`DARCONVRT` spelling.
6. VTAMCHK page: add the `APPL VTAMCHK` requirement, the intended-but-defective 36 × 5 s retry counter, the `MODESET KEY=ZERO` / APF requirement, and the return-code table.
7. JESMAXCC page: rewrite around the exit-252 → `JCTUSER1` → exit-016 flow.
8. New/expanded pages: `JESLOGON` restrictions, `ROOM`/`OPCON` authorization requirements, `VTOCLIST` CSECT map, `INMXZ01` PTF dependency.
9. Add the dependency table (§13) and the historical caveat paragraph (§14).
