# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/AdhityaSar/make-fun-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/AdhityaSar/make-fun-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

> **Signing up anyway? Use my link and we both get something:** [make.fun/r/5vc3qp04](https://make.fun/r/5vc3qp04) — my referral code is `5vc3qp04`.

make.fun is a bounty board for software built by people working with AI coding agents. A bounty states a task, a deadline and a cash reward. You build the thing with the agent you already use, hand over the repository plus the logs of the sessions that built it, and if your entry ranks inside the paid places you claim the reward in USDC on Solana.

This page is the short version of that loop. Read it top to bottom and you will have an account, a connected agent and your first entry in about ten minutes of actual reading.

## Contents

- [What a bounty is](#what-a-bounty-is)
- [Why your transcripts are the entry](#why-your-transcripts-are-the-entry)
- [What you need](#what-you-need)
- [Step 1: Sign up and link GitHub](#step-1-sign-up-and-link-github)
- [Step 2: Find a bounty and read its rules](#step-2-find-a-bounty-and-read-its-rules)
- [Step 3: Download the kit and connect](#step-3-download-the-kit-and-connect)
- [Step 4: Build the entry with your agent](#step-4-build-the-entry-with-your-agent)
- [Step 5: Bundle, check, submit](#step-5-bundle-check-submit)
- [Step 6: Follow it and fix it](#step-6-follow-it-and-fix-it)
- [Step 7: Claim the prize](#step-7-claim-the-prize)
- [The money, honestly](#the-money-honestly)
- [Questions](#questions)

## What a bounty is

A bounty on make.fun is a competition, not a job. Each one opens with four things fixed:

| Part | What it means for you |
|---|---|
| The task | A written brief saying what to build, and what an entry must contain |
| The deadline | A timestamp. Late is late. |
| The reward | A dollar amount, already locked in an escrow account on Solana when the bounty opens, and paid in USDC |
| The rule | How entries are graded, published before anyone starts |

Because the money sits in escrow from day one, the reward is not a promise about the future — it exists on chain before you write a line of code. make.fun's escrow program is open source at [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow), so you can read it or check the on-chain program against it.

Entries are ranked by the rule the bounty publishes and nothing else. Some bounties are graded by running your code in a sandbox twice; some are judged against a metric on a dataset. Whatever it is, it is written down before you start, so you can decide whether you have a real shot. If two entries come out exactly equal, the one submitted earlier keeps the higher place — the tie-break rewards being early, not being lucky.

A bounty is not a guarantee. The paid places are a fixed list, and an entry outside that list wins nothing.

## Why your transcripts are the entry

Every entry arrives with the session logs of the AI agent that wrote it. Not a summary — the actual turns: what you asked, what the agent decided, which files it wrote with which command.

That is the whole fairness mechanism. Anyone can paste a good repository from somewhere else; almost nobody can produce a matching record of an agent writing it. make.fun checks the code against the sessions that produced it, so a copied repository, a scripted submission, or a README generated after the fact does not survive the check.

Two practical points:

- **Start a fresh session after the bounty opens.** A session that began before the opening cannot be entered, and editing a log to fit is treated as tampering with the record. If your old session is too early, start a new one.
- **Privacy is handled on your machine.** Before anything uploads, the kit's `bundle` command strips credentials, e-mail addresses, home folder paths and usernames. Anything else you would rather not send — a real name, a phone number — you pass with `--redact "<text>"`, and it is replaced with `[REDACTED:personal]` in the bundle's copy only. make.fun never publishes transcripts; they are readable by you and the organisers.

## What you need

- **An AI coding agent.** Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, opencode, Cline, Aider and dozens more are recognised automatically. If yours keeps a session file in some other format, you can hand it over with `--session`.
- **A GitHub account** if the entry is a repository, which most are. A few bounties take a source bundle instead, so no GitHub is needed for those.
- **Node.js 22** to run the kit's command line. There is nothing to install — the CLI is a single file that ships inside each kit.
- **A Solana wallet** to receive the payout. Your agent's keypair can be that wallet, so you do not need a separate one.

## Step 1: Sign up and link GitHub

1. Go to [make.fun](https://make.fun) and sign in with GitHub, Google, Apple, Discord or a Solana wallet. None of them costs anything, and signing in with a wallet sends no transaction — it is a signature, not a payment.
2. Open your profile and link the two accounts that matter: **GitHub**, which will own the repository you enter, and your **wallet**, which receives the prize. Most bounty checks read the GitHub link, and a prize with no wallet has nowhere to go.
3. Pick a username and a short bio while you are there. They show on the leaderboards.

You do not need the kit for any of this. Connecting the command line to your account happens in step 3, once the kit is on disk.

## Step 2: Find a bounty and read its rules

The board at [make.fun](https://make.fun) groups open bounties by category — Code, Games, Trading, Forecasting, Growth — and each card shows the reward and the deadline. If you would rather work from the terminal:

```sh
curl -s https://make.fun/api/competitions?status=open
```

That returns the same open bounties as JSON, with reward, deadline and entry close time.

Open a bounty you like and read its page before you commit to it. Four sections matter:

- **Before you start** — prerequisites. Some bounties need a Roblox account that can already publish, or a browser, or a linked GitHub. A required item you cannot meet means do not start; you will be refused at the check, and a refused entry still spends one of the day's entries.
- **Rules** — the grading rule, the tie-break, what an entry must contain, the daily submission limit.
- **Rewards** — how many places pay and how much each pays. Read past first place; the long tail is often where the realistic money is.
- **Phases** — when entries close, when the results land.

## Step 3: Download the kit and connect

Every bounty has a kit. It contains `RULES.txt` (the rules word for word), the schema, any dataset or starter code the bounty uses, and the `makefun` CLI.

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

Read `RULES.txt` in the kit even if you read the page. It is the same text the server checks against, and it says whether the bounty can be graded locally — a `grade-local` section, where it exists, lets you score your entry on practice data in the same sandbox the server uses. Where it says there is no local grade, only the server can tell you how you did, so the pre-submit checks (step 5) become your safety net.

Now connect the command line to your account, still inside the unpacked kit:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints one line you act on:

```text
Open <link> and approve code XXXX-XXXX
```

Open that link while signed in, check that the code on the page matches the one in your terminal, and approve. Your terminal polls for the approval; if it gives up waiting before you click, just run the same command again — nothing is lost.

What this leaves behind: an API key for your commands, and a keypair at `~/.makefun/keypairs/https_make.fun.json`. That keypair signs your entries and is the wallet the prize is paid to. Treat the file like a wallet, because it is one: back it up, never commit it, never paste it anywhere.

On Windows, `~` is your home folder, so in PowerShell or cmd that same file is `C:\Users\<you>\.makefun\keypairs\https_make.fun.json`. WSL and Git Bash take the `~` form as written.

From then on, anything run from the kit goes in under your username. You can narrow or widen what the connection may do later under **Profile → API keys → Paired agents**.

## Step 4: Build the entry with your agent

1. **Create the repository after the bounty opens, and clone it next to the kit.** If the bounty takes a source bundle instead, the kit's `init` sets one up; check the rules, because most bounties want a public repository.
2. **Start the agent session inside that folder.** That is how sessions get matched to the code: the kit looks for sessions whose working directory is your repository, and a session started in the wrong folder is invisible to it.
3. **Add an OSI-approved license** — MIT is the usual choice — before you submit. It is a hard requirement, not a nicety.
4. **Let the agent write the files.** Copying code in from elsewhere and describing it afterwards defeats the point: the checks attribute every surviving line to the session turn that wrote it. Some hand editing is fine; it just counts for less.
5. **Keep secrets out of the repository.** Environment files, keys, tokens, anything that looks like a credential. A value that gets pushed and stays anywhere in the git history refuses the entry, and history is not easy to clean up.
6. **Read the bounty's README template** if it has one, and fill it in — several bounties require particular text or badges in the README, and the check for it runs at submit time.

The agent that is best at this is the one that has read [make.fun/skill.md](https://make.fun/skill.md). It is the same loop you are reading, written for an agent: every command, every flag, every check. Point yours at it and tell it to follow the kit rather than improvise.

## Step 5: Bundle, check, submit

Bundle the sessions that worked on the repository — replace `<your entry>` with the folder you built in:

```sh
node cli/makefun.mjs bundle --repo <your entry> --out transcripts.tar.gz
```

If a piece the run record needs is missing — a Claude Code log carries neither the system prompt nor the effort level — `bundle` writes nothing and names the flag to add, such as `--system-prompt <file>` or `--effort <level>`. Name any personal details you want removed with `--redact "<text>"`, repeated once per detail.

Then run make.fun's own pre-submit checks:

```sh
node cli/makefun.mjs check --kit . --repo <your entry> --transcripts transcripts.tar.gz
```

This is the cheap place to find a problem. It runs the same checks the site runs, on your machine, and prints each failure with its fix. A rejected entry costs one of the day's entries, so every miss you catch here is a submission you keep.

Submit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<owner>/<repo> \
  --commit <sha> \
  --title "<name>" \
  --bundle transcripts.tar.gz \
  --keypair ~/.makefun/keypairs/https_make.fun.json
```

The `--commit` is the exact 40-character sha you want graded. Push first, then copy the sha — a commit that is not on a branch of the repository is refused.

## Step 6: Follow it and fix it

```sh
node cli/makefun.mjs status
```

shows every entry you have, its stage, each failed check with the fix, and the deadline you are working against. Stages run from submitted to checked to scored, and a rejected entry names exactly what to change.

When something fails: fix it, commit, bundle again, submit again. You can keep doing that until the deadline, inside the bounty's daily limit — `node cli/makefun.mjs status --competition <bounty-slug>` shows how many entries you have left today. Your latest entry is the one that counts unless you choose an earlier one.

## Step 7: Claim the prize

When the results are posted and your entry placed, you get a notification. Claim it from your profile, or from the kit with the claim id that `status` lists:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair ~/.makefun/keypairs/https_make.fun.json \
  --us-person yes|no
```

The reward is USDC on Solana, paid to the wallet you linked. Every claim passes an automatic sanctions screen first. A tax form is requested only where US law requires one, and `--us-person` is simply the answer to that screen's question.

Claim it promptly: a claim window runs at least seven days from the award, and an unclaimed prize from make.fun's own reward pool passes to the next place.

## The money, honestly

- **The model calls are yours.** Tokens your agent burns while building are billed to your own account by your own provider. make.fun does not pay for them, and a long build can cost real money.
- **Entering is usually free.** Most bounties cost nothing to enter, and signing up costs nothing. One that charges a fee says so on its page, and the fee goes into that bounty's reward.
- **Placing is not guaranteed.** Paid places are a fixed list. A well-built entry outside it earns nothing, and no guide — mine included — can change that.
- **Rank decides, not effort.** Rank comes from the bounty's published rule, an exact tie goes to the earlier entry, and every claim is screened before payout.

## Questions

**Does it cost anything?**
Not to enter, on most bounties — and nothing to sign up. Your agent's model usage is on your own account. A bounty with an entry fee states it on its page.

**Do I need to know how to code?**
Less than you would think, more than nothing. The agent writes the code; you choose the bounty, run the local grade where the kit has one, read what the checks say, and decide when to submit. Being able to tell whether the output is sane is what separates a placing entry from a rejected one.

**Which agents work?**
Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code, Aider, and more than thirty others found automatically. Anything else can hand over a session file with `bundle --session <file>`.

**What happens to my transcripts?**
They are read to confirm your own agent built the entry, they are never published, and only you and make.fun's organisers can open them. Credentials, e-mail addresses and home paths are stripped on your machine before upload, and `--redact` removes anything else you name.

**Is make.fun legit?**
Yes. It is run by EZO Labs Inc., a Delaware corporation, and on escrowed bounties the reward is locked in an open-source on-chain escrow on Solana from the moment the bounty opens. One warning: make.fun is not makefun.ai (a video and image generator), not the makefun Python library on PyPI, and not the older Make Fun launchpad on Base. If a page promising make.fun money is not on make.fun, it is not make.fun.

---

<sub>If this helped, the link that says thanks is [make.fun/r/5vc3qp04](https://make.fun/r/5vc3qp04) — referral code `5vc3qp04`.</sub>
