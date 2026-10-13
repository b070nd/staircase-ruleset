# staircase-ruleset

A test repository for the [stAirCase](https://github.com/b070nd/stAirCase) verify Action under a GitHub ruleset. It is built by
`demo/ruleset-fixture.sh` in that repository; it is not a project, and nothing here should be copied as an example of an application.

**The rule it tests:** a pull request into `main`, `other-key` or `strict` can be merged only if the Action says its commits are covered by
a change certificate signed by the key that base trusts, at the level that base requires. Each base has its own ruleset and its own required
check (`staircase-admission-main`, `staircase-admission-other-key`, `staircase-admission-strict`).

| Pull request (head into base) | The check | Why |
|---|---|---|
| `pr-certified` into `main` | passes, merge allowed | a real certified commit (CAL 3), rebuilt from its ledger |
| `pr-sealed` into `main` | passes, merge allowed | a change certified after the fact (CAL 2), which `main` accepts |
| `pr-wrong-signer` into `other-key` | **fails, blocked** | certified, but this base trusts another key |
| `pr-below-strict` into `strict` | **fails, blocked** | certified at CAL 2, and `strict` requires CAL 4 |
| `pr-no-notes` into `main` | **fails, blocked** | a commit with no certificate |
| `pr-edits-the-check` into `main` | **fails, blocked** | it also edits the check's own workflow; the copy on `main` runs, so the edit is not used |

Every pull request has a head commit of its own, and the required check is named for its base. GitHub attaches a check to a commit, not to a pull
request: an earlier build of this repository shared one commit between a pull request into `main` and one into `other-key`, and with one check
name the pass for `main` unlocked the merge into `other-key`, which must have been refused. Open no pull request from `other-key` or `strict`: they
are bases, not heads.

The results, the run logs they come from and what GitHub answered to a merge of a blocked pull request are recorded in
[docs/release-evidence](https://github.com/b070nd/stAirCase/tree/master/docs/release-evidence) of the stAirCase repository.
