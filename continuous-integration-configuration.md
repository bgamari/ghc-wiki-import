# GitLab runner configuration

A sample configuration:
```toml
concurrent = 1                    # <---- Set
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "centriq.haskell.org"    # <---- Set
  url = "https://gitlab.staging.haskell.org/"
  token = "TOKEN"                 # <---- Set
  executor = "docker"
  environment = ["CPUS=16"]       # <---- Set
  output_limit = 16000            # <---- Set
  [runners.docker]
    tls_verify = false
    image = "ghcci/aarch64-linux-deb9:0.1"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]

```

## Darwin configuration

Install Homebrew.
```
$ brew install autoconf automake python3 wget
```

Install `gitlab-runner` according to
<https://docs.gitlab.com/runner/install/osx.html>.


## Windows configuration

Note: In the case of Windows builders it is important that we run only one build per machine. Unfortunately concurrent builds are simply too fragile under Windows' file locking semantics.

Start with Windows Server GCE image.

Install [Git for Windows](https://git-scm.com/download/win). When prompted
select `Git from the command line and also from 3rd-party software`. Also,
ensure `core.autocrlf` is set to `false` (this can be set during installation or
in `C:\ProgramData\Git\gitconfig` thereafter).

Install msys2.

```
$ pacman -Syuu
$ pacman -S \
    git tar bsdtar unzip binutils autoconf make xz \
    curl libtool automake python python3 p7zip patch ca-certificates \
    mingw-w64-$(uname -m)-gcc mingw-w64-$(uname -m)-python3-sphinx \
    mingw-w64-$(uname -m)-tools-git
```

Create a `gitlab` user with a password.
[Grant](https://docs.gitlab.com/runner/faq/README.html#the-service-did-not-start-due-to-a-logon-failure-error-when-starting-service-on-windows)
this account the `SeServiceLogonRight` in the `Local Security Policy` tool.


[Download](https://docs.gitlab.com/runner/install/windows.html) 
`gitlab-runner` and place in `C:\GitLabRunner`. In an Administrator shell run,
```
cd C:\GitLabRunner
gitlab-runner install --user ".\gitlab" --password ...
```
Register the runner.


## AArch64 configuration

On a Debian/Ubuntu machine as of Dec 2018:

```
$ sudo apt-get install ruby ruby-dev golang
$ git fetch https://gitlab.com/solidnerd/gitlab-runner.git 
$ git checkout feature/arm64-support
$ cd gitlab-runner
$ make deps
$ make build_simple
$ make out/helper-images/prebuilt-arm64.tar.xz
```

Also relevant: https://gitlab.com/gitlab-org/gitlab-runner/merge_requests/725


## Current Runners

 * `maurer`: A large Linux box hosted by @bgamari
 * `maurer-windows`: A Windows VM running on Maurer
 * `ben-server`: A smaller but faster Linux box hosted by @bgamari
 * `ghc-ci-1`: A server hosted by [Packet.net](https://app.packet.net/devices/a5f1422f-1708-44a9-9ee6-c464385ec386) (sponsored by packet.net)
 * `unsafePerformCI`: A server hosted by @alp
 * `gce-windows`: A Windows Server VM hosted by Google Compute Engine (sponsored by Google)
 * `mac-mini-x86_64-darwin-davxkc`: A macOS box on Mac Stadium (sponsored @davean)
 * `centriq.haskell.org`: A Qualcomm Centriq hosted and sponsored by [Packet.net](https://app.packet.net/devices/bda5d024-ad2a-4390-9788-75163e46b2e4)
 * `futurice-darwin`: A Mac Mini hosted and sponsored by [Futurice](https://www.futurice.com/)
