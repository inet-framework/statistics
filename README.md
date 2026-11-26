# INET statistical baselines

This repository holds the stored results of the statistical tests of [INET](https://github.com/inet-framework/inet). The test runs run 0 of each simulation configuration, converts its vectors to scalars, and compares each scalar with the file in this repository. The comparison is exact: a change in the last digit fails the test.

## Layout

The path of a file is the working directory of the simulation in INET, then `<ini file>-<config>-#<run>.sca`. For example, `examples/ethernet/lans/omnetpp.ini-MixedLAN-#0.sca` holds the results of the config `MixedLAN` of `examples/ethernet/lans/omnetpp.ini`.

The folder `attribution/` holds the reduction patches of baseline changes, if a change needs one (see below).

## How INET uses this repository

Each INET commit has one commit of this repository as its baseline. The tests of that INET commit compare with the files of that commit. The command `opp_checkout_test_baselines` of [opp_repl](https://github.com/omnetpp/opp_repl) clones this repository into the folder `statistics` of INET and checks out the baseline of the INET commit.

An INET commit that moves a result has the trailer `Statistical-Baseline: <hash>` in its message: the pin, the last commit of its step on a branch of this repository. `inet.opp` does not change. After the INET change lands, the landing job of INET (`check-baseline-masters.yml`) puts the steps onto `master`: by a fast-forward when a step starts on the head of `master`, and else by a rebase, with the same messages and authors. After the steps it adds a mark: a commit with no change of files, with the line `Baseline-Of: <INET hash> <baseline hash>` for each landed INET commit. Thus `master` holds the baseline of INET `master`.

A file that a branch changes and that `master` changed since the base of the branch is an overlap, and the landing stops. The commit status `Baseline merge` of the INET pull request shows an overlap before the landing.

After a rebase, the head of a pull request of this repository is not on `master`, so GitHub does not mark it as merged. The mark names it in the line `Baseline-Pull-Request: #N`, the log and the job summary of the landing job name it too, and a maintainer closes it.

## Change a baseline

The procedure is in the INET guide [change-a-baseline.md](https://github.com/inet-framework/inet/blob/master/doc/project/guide/change-a-baseline.md), and the rules are `TR-BASELINE-*` in [testing.md](https://github.com/inet-framework/inet/blob/master/doc/project/rule/testing.md). In short:

- The INET commit that moves a result has the pin trailer.
- One INET commit has at most one commit in this repository that changes results.
- Only the tests that the change moves get new files. Each of them passes at the parent of the INET commit.
- The message of the commit holds the record: an analysis of the moved results, the `Moved results:` block that `doc/project/enforcement/baseline_summary.py` of INET computes, and the trailers `INET-Subject:` and `Attribution:`.
- Record the values in the CI environment: the artifact of a CI run, or a copy of the CI job. Values from another compiler or C library can differ in the last digit.

A commit that changes no result, such as this file, needs no record.

## Review

Start the branch on the head of `master`. Push a branch with the name of the INET branch, or open a pull request from a fork of this repository. Do not merge the pull request with the button: the landing job lands it. The review of that branch or pull request approves the values. Name it in the description of the INET pull request.
