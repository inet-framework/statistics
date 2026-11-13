# INET statistical baselines

This repository holds the stored results of the statistical tests of [INET](https://github.com/inet-framework/inet). The test runs run 0 of each simulation configuration, converts its vectors to scalars, and compares each scalar with the file in this repository. The comparison is exact: a change in the last digit fails the test.

## Layout

The path of a file is the working directory of the simulation in INET, then `<ini file>-<config>-#<run>.sca`. For example, `examples/ethernet/lans/omnetpp.ini-MixedLAN-#0.sca` holds the results of the config `MixedLAN` of `examples/ethernet/lans/omnetpp.ini`.

The folder `attribution/` holds the reduction patches of baseline changes, if a change needs one (see below).

## How INET uses this repository

Each INET commit pins one commit of this repository: the `ref` of the `statistical` entry of `test_parameters` in `inet.opp`. The tests of that INET commit compare with the files of that commit. The command `opp_checkout_test_baselines` of [opp_repl](https://github.com/omnetpp/opp_repl) clones this repository into the folder `statistics` of INET and checks out the pin.

`master` is the pin of INET `master`. After an INET change lands, `master` moves to its pin by a fast-forward.

## Change a baseline

The procedure is in the INET guide [change-a-baseline.md](https://github.com/inet-framework/inet/blob/master/doc/project/guide/change-a-baseline.md), and the rules are `TR-BASELINE-*` in [testing.md](https://github.com/inet-framework/inet/blob/master/doc/project/rule/testing.md). In short:

- The INET commit that moves a result changes the pin, in the same commit.
- One INET commit has at most one commit in this repository that changes results.
- Only the tests that the change moves get new files. Each of them passes at the parent of the INET commit.
- The message of the commit holds the record: an analysis of the moved results, the `Moved results:` block that `doc/project/enforcement/baseline_summary.py` of INET computes, and the trailers `INET-Subject:` and `Attribution:`.
- Record the values in the CI environment: the artifact of a CI run, or a copy of the CI job. Values from another compiler or C library can differ in the last digit.

A commit that changes no result, such as this file, needs no record.

## Review

Push a branch with the name of the INET branch, or open a pull request from a fork of this repository. The review of that branch or pull request approves the values. Name it in the description of the INET pull request.
