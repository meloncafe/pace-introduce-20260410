# How to Make Claude Code Work Smarter — 6 Months Later (Hooks → Harness)

Hello, Orchestrators

I wrote a post about Claude Code Hooks last November, and seeing that this technique is now being referred to as "Harness," I was glad to learn that many others have been working through similar challenges.

If you're interested, please take a look at the post below
https://www.reddit.com/r/ClaudeAI/comments/1osbqg8/how_to_make_claude_code_work_smarter/

At the time, I had planned to keep updating that script, but as the number of hooks increased and managing the lifecycle became difficult due to multi-session usage, I performed a complete refactoring. The original Hook script collection has been restructured into a Claude Code Plugin called "Pace."
Since it's tailored to my environment and I'm working on other projects simultaneously, the code hasn't been released yet.

![Currently set to CSM, but will be changed to Pace.](images/1.png)

Currently set to CSM, but will be changed to Pace.

Let's get back to Claude Code.

My philosophy remains the same as before.

**Claude Code produces optimal results when it is properly controlled and given clear direction.**

Of course, this doesn't mean it immediately produces production-grade quality.

However, in typical scenarios, when creating a program with at least three features by adjusting only CLAUDE.md and AGENTS.md, the difference in quality is clearly noticeable compared to an uncontrolled setup.

The current version of Pace is designed to be more powerful than the restrictions I previously outlined and to provide clearer guidance on the direction to take.

It provides CLI tools tailored to each section by default, and in my environment, Claude Code's direct use of Linux commands is restricted as much as possible.

As I mentioned in my previous post, when performing the same action multiple times, Claude Code constructs commands arbitrarily.

At one point, I asked Claude Code:

**"Why do you use different commands when the result is the same, and why do you sometimes fail to execute the command properly, resulting in no output?"**

This is what came back:

**"I'm sorry. I was trying to proceed as quickly and efficiently as possible, so I acted based on my own judgment rather than following the instructions."**

This response confirmed my suspicion.

Although AI LLMs have made significant progress, **at least in my usage, they still don't fully understand the words "efficient" and "fast."**

This prompted me to invest more time refining the CLI tools I had previously implemented. Currently, my Claude Code blocks most commands that could break session continuity or corrupt the code structure — things like modifying files with `sed` or `find`, arbitrarily using `nohup` without checking for errors, or running `sleep 400` to wait for a process that may have already failed. When a command is blocked, alternative approaches are suggested.
(This part performs the same function as the hooks in the previous post, but the blocking methods and pattern recognition have been significantly improved internally.)

In particular, as I am currently developing an integrated Auth module, this feature has made a clear difference when using test accounts to build and test the module via Playwright scripts — both for cookie-based and Bearer-based login methods.

![CLI for using test accounts](images/2.png)

CLI for using test accounts

Before creating this CLI, it took Claude Code over 10 minutes just to log in for module testing.

The module is being developed with all security measures — device authentication, session management, MFA, fingerprint verification, RBAC — enabled during development, even though these are often skipped in typical workflows.

The problem is that even when provided with account credentials in advance, Claude Code uses a different account every time a test runs or a session changes.

It searches for non-existent databases, recreates users it claims don't exist, looks at completely wrong databases, and arbitrarily changes password hashes while claiming the password is incorrect — all while attempting to find workarounds, burning through tokens, and wasting context.

**And ultimately, it fails.**

That's why I created a dedicated CLI for test accounts. This CLI uses project-specific settings to create accounts in the correct database using the project's authentication flow. It activates MFA if necessary, manages TOTP, and holds the device information required for login. It also includes an Auto Refresh feature that automatically renews expired tokens when Claude Code requests them.

Additionally, the CLI provides cookie-injection-based login for Playwright script testing, dynamic login via input box entry, and token provisioning via the Bearer method for curl testing.

By storing this CLI reference in memory and blocking manual login attempts while directing Claude Code to use the CLI instead, it was able to log in correctly with the necessary permissions and quickly succeed in writing test scripts.

It's difficult to cover all features in this post, but other CLI configurations follow a similar pattern.

The core idea is to pre-configure the parts that Claude Code would execute as raw commands and force it to use only the dedicated CLI.

I'll wrap up this post here for today.

Additionally, since someone mentioned in a previous post that they wanted to see my CLAUDE.md configuration, I'm attaching it here.
I haven't included the linked critical.md, project.md, and rules.md files this time, as they are based on my personal standards and may not align with everyone's setup.

Each of the three linked files contains about 200 lines of operational-level guidelines.

```
## Core

@core/critical.md
@core/project.md
@core/rules.md

---

## Plugins

### cc-session-manager
For details on the Skill/CLI, refer to `critical.md`; for priority rules, refer to `rules.md`.
```

If possible, I would be very happy if my next post could be an introduction to the official version of Pace.

Thank you for reading this long post today.

Have a great day!

---

- The original text of this post was written in Korean and translated with assistance from DeepL.
- As a result, the message may not be conveyed exactly as I intended.
- If you have any issues or questions, please feel free to leave a comment.
- You can also check the posts I manage and my open-source projects at https://devsaurus.notion.site/.
