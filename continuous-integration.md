# Continuous Integration



This page is to support the discussion of GHC DevOps Group on the CI solution for GHC to provide continuous testing and release artefact generation. See also [\#13716](https://gitlab.staging.haskell.org/ghc/ghc/issues/13716).


## Requirements



**Primary** 


- Build GHC for Tier 1 platforms (Windows, Linux & macOS), run `./validate`, and produce release artefacts (distributions and documentation).
- Build PRs (differentials) and run `./validate` on Linux/x86-64.
- Security: PRs builds run arbitrary user code; this must not be able to compromise other builds and especially not release artefacts.
- Infrastructure reproducibility (infrastructure can be spun up and configured automatically)
- Infrastructure forkability (devs forking the GHC repo can run their own CI without additional work)
- Low maintenance overhead
- Low set up costs


**Secondary**


- Build PRs (differentials) and run `./validate` on non-Linux/x86-64 Tier 1 platforms.
- Build GHC for non-Tier 1 platforms & run `./validate`

## Possible solutions


### Jenkins



Pros


- We can run build nodes on any architecture and OS we choose to set up.


Cons


- Security is low on PR builds unless we spend further effort to sandbox builds properly. Moreover, even with sandboxing, Jenkins security record is troublesome.
- Jenkins is well known to be time consuming to set up.
- Additional time spent setting up servers.
- Additional time spent maintaining servers.
- It is unclear how easy it is to make the set up reproducible.
- The set up is not forkable (a forker would need to set up their own servers).

### CircleCI & AppVeyor



Pros


- Easy to set up
- Excellent security
- Low maintenance cost
- Infrastructure reproducible 
- Infrastructure forkable
- Easy to integrate with GitHub


Cons


- Direct support only for Linux, Windows, macOS, iOS & Android, everything else needs to rely on cross-compiling and QEMU, or building on remote drones.
- We are limited to CircleCI's & Appveyor's current feature set and have to rely on them for feature development.
- We need to deal with two different CI providers.

## Discussion summary



After a detailed discussion on the GHC DevOps Group mailing list [
https://mail.haskell.org/pipermail/ghc-devops-group/](https://mail.haskell.org/pipermail/ghc-devops-group/), the group reached the conclusion that all factors considered, CircleCI & AppVeyor provides the best trade offs for the following reasons.



**Costs**


- Both solutions incur hardware or subscription costs. In the case of Jenkins, we need servers (and we just learnt that RackSpace terminated its OSS program at the end of the year) and, in the case of CircleCI & AppVeyor, we will likely need more resource than they provide for free to open source projects. However, Google X and Tweag I/O have indicated that they would be happy to help cover such costs.
- Both solutions incur some developer costs. However, by all accounts and our own experience, this is substantially lower for the CircleCI & AppVeyor solution. In particular, it completely eliminates server maintenance costs. Integration with GitHub is straight forward and Phabricator also seems to have some support.


Overall, CircleCI & AppVeyor are more cost-effective.



**Flexibility**


- Jenkins is fully customisable and provides complete flexibility wrt to the architectures and operations systems used in build machines.
- With CircleCI & AppVeyor, we are dependent on the features provided by the hosting services and limited in the architectures and OSes that are directly supported. In particular, we will have to fall back to cross-compiling and using QEMU for less commonly used platforms. Our base line, the Tier 1 platforms, as well as Android and iOS on ARM are supported by CircleCI & AppVeyor.


Overall, Jenkins is more flexible.



**Security**


- Security is crucial as we are planning to run untrusted and unreviewed code in PRs/differentials in the CI environment.
- To provide adequate security, we need to add sandboxing to Jenkins (which is possible, but more work). However, even with that, the security track record of Jenkins is not very good. 
- In the case of CircleCI & AppVeyor, security is taken care of by the provider.


Overall, CircleCI & AppVeyor are more secure.



**Scalability**


- With forkability, we refer to a user's ability to fork GHC, modify it, and run their own CI instance (not using GHC HQ's infrastructure) without any significant extra work. This significantly improves scaling and takes load of the GHC HQ's infrastructure. CircleCI & AppVeyor is forkable, but Jenkins is not (as a user needs to set up their own infrastructure).
- While Jenkins can be combined with other Docker/Kubernets to improve scaling, this is difficult (e.g., Tweag I/O had some rather bad experiences with this). In contrast, this is handled by the provider in the CircleCI & AppVeyor case.


Overall, CircleCI & AppVeyor are more scalable.



**Reliability**


- With our own infrastructure, reliability is in our own hands, but we will not be able (as it would be too expensive) to provide any real reliability guarantees or support with guaranteed turn around times.
- In the case of CircleCI & AppVeyor we depend on the providers for a reliable service. Ben talked to the Rust team who were very positive about CircleCI in this respect, but at the end of the day, it will be hard to know without trying (given we want to rely mostly on free open-source plans).


Overall, let's call this a tie.


## Usage estimate



A quick back-of-the-envelope calculation suggests that to simply keep up
with our current average commit rate (around 200 commits/month) for the
four environments that we currently build we need a bare minimum of:


```wiki
   200 commit/month
 * 4 build/commit             (Linux/i386, Linux/amd64,
                               OS X, Windows/amd64)
 * 2.5 CPU-hour/build         (approx. average across platforms
                               for a validate)
 / (2 CPU-hour/machine-hour)  (CircleCI appears to use 2 vCPU instances)
 / (30*24 machine-hour/month)
 ~ 2 machines
```


Note that this doesn't guarantee reasonable wait times but rather merely
ensure that we can keep up on the mean. On top of this, we see around
300 differential revisions per month. This requires another 3 machines
to keep up.



So, we need at least five machines but, again, this is a minimum;
modelling response times is hard but I expect we would likely need to
add at least two more machines to keep response times in the
contributor-friendly range, especially considering that under Circle CI
we will lose the ability to prioritize jobs (by contrast, with Jenkins
we can prioritize pull requests as this is the response time that we
really care about). Now consider that we would like to add at least
three more platforms (FreeBSD, OpenBSD, Linux/aarch64, all of which may
be relatively slow to build due to virtualisation overhead) as well as a
few more build configurations on amd64 (LLVM, unregisterised, at least
one cross-compilation target) and a periodic slow validation and we may
be at over a dozen machines.


## Todo list



Below, we track all work that needs to be done until we have achieved the following specific goals:


- automatic per-commit builds of master for Linux/x86\_64 on CircleCI (per each block of commits from one PR/Differential would be sufficient),
- automatic builds of Linux/i386, macOS/x86\_64 & Windows/x86\_64 on CircleCI and AppVeyor at least once per day,
- automatic Linux/x86\_64 build for each PR/Differential on CircleCl, and
- automatic generation of all release artefacts on Linux/i386, Linux/x86\_64, macOS/x86\_64 & Windows/x86\_64 on CircleCI and AppVeyor at least once per day.


**Most of the below should be turned into Trac tickets.**


### General


- Talk to CircleCI about increased limits for the free plan for GHC. Determine how much on top of that we need.
- AppVeyor is also free for OSS projects, but I couldn't find anything specific about the limits. Determine how much on top of the OSS plan we need and whether they are willing to relax the limits for a visible OSS project.

### Per-commit build on Linux/x86\_64


- What is missing from Mathieu's CircleCI build?

  - We want to run `./validate --slow` at some point — see [\#13205](https://gitlab.staging.haskell.org/ghc/ghc/issues/13205).
- Probably easiest to just trigger those builds from GitHub (as all commits are mirrored there anyway).

### Daily builds on Linux/i386, macOS/x86\_64 & Windows/x86\_64


- Implement AppVeyor build config.
- Jonas has started on macOS CircleCI build. What is the status?
- Linux/i386 ought to be a small change on Linux/x86\_64, or is there more to it?

### Per-PR/Differential build on Linux/x86\_64


- This is the CircleCI 1.0 documentation on Phabricator integration: [
  https://circleci.com/docs/1.0/phabricator/](https://circleci.com/docs/1.0/phabricator/) Does this work with CircleCI 2.0 as well? (The CircleCI API 1.1 supposedly can drive both.)
- Implement CircleCI/GitHub integration for PRs.

### Daily release artefacts for all Tier 1 platforms


- What is missing in Ben's existing scripts?
- Integrate release artefact building and uploading into all daily builds.