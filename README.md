# Aperture Control - Windows environment automation

This is a set of scripts to help automate your Windows installations. It's intended to be used by forking and customizing the repository to your needs. It is not intended as a tool for the general public, some understanding of command line utilities, registry, and such is required for efficient usage.

With well designed scripts, it can maintain your various systems in the desired state over time and minimize the hassle of taking new computers into use.

You can also use it to e.g. deploy various common tools to your fellow developers working in the same project, or distribute software and basic settings for all your employees without resorting to the pretty abusive domain level controls over the computers.

This has been tested on Windows 10 Professional. No promises about it working under anything else (but please do send feedback if you are successful in other environments as well).

See [Contributing Guidelines](./CONTRIBUTING.md) and [Code of Conduct](./CODE_OF_CONDUCT.md).


## How to run Aperture Control

You should likely start off by taking a look at [https://github.com/Lieturd/aperture-control-example](https://github.com/Lieturd/aperture-control-example) for how to set up a good amount of basic things, fork it, and then edit the contents of `recipes` to match your needs. You can find a number of pre-made "recipes" to accomplish various tasks in [https://github.com/Lieturd/aperture-control-recipes](https://github.com/Lieturd/aperture-control-recipes). 

If you care about the order of execution use numbered prefixes such as `01-first-things.cmd`.

Once you have a repository set up in GitHub with the contents you want, launch PowerShell as Administrator and paste this in it (editing the last bit for the repository).

```
Set-ExecutionPolicy -ExecutionPolicy Unrestricted
(New-Object System.Net.WebClient).DownloadFile('https://raw.githubusercontent.com/Lieturd/aperture-control/master/setup.ps1', 'setup.ps1')
.\setup.ps1 username/aperture-control
```

Note: It seems Windows installations by default block PowerShell scripts, which is pretty weird considering they want to promote the tool as "the" scripting tool for Windows, but that's why you need to unrestrict the execution policy before running Aperture Control.

Simply what happens is that it downloads the [setup.ps1](./setup.ps1) -script and executes it with your repository as the argument so it knows where to download your configuration. This downloads your configuration, runs it, and sets a scheduled task to check for updates hourly.

Alternatively, if you don't want your configuration to be public, just copy it from your favorite secure storage, and run the `run-ac-recipes.ps1` -script as Administrator.

**PLEASE NOTE:** Installing a lot of things with this may take a while, and your computer might launch or close various things you are already running. This is best run with a clean system, or right after starting Windows, and letting it do it's thing. Also restarting afterwards might be necessary to finish some installations, to activate various registry changes, and so on.


## What does it really do?

Simply put, it runs a number of PowerShell and cmd scripts to set up your environment, and is able to apply registry patches as well.

In more detail:
1. The `setup.ps1` script is downloaded with the command above and executed
2. `setup.ps1` downloads the complete repository from GitHub, unzips it under `%USERPROFILE%\aperture-control` and executes `run-ac-recipes.ps1` with Administrator permissions
3. `run-ac-recipes.ps1` loops through `recipes/*` and
    - `*.ps1` scripts are executed with PowerShell
    - `*.cmd` scripts are executed with Cmd.exe
    - `*.reg` files are applied with Regedit
4. It sets up the `update.ps1` script in Task Scheduler to be run hourly. It will poll the source repo for updates (just a GitHub API call, nothing heavy), and then if any are found it will download them and apply changes.


## Words of warning

Do not blindly trust random scripts on the internet, including these. Check them for yourself, make sure you understand what is going on, and that you have reasonable reason to trust them and have control over what is running on your PC.

Do not use other people's repositories for your configuration, as they may change them, and the scripts can execute anything with administrator permissions without you noticing.


# License

Short answer: This software is licensed with the BSD 3-clause -license.

Long answer: The license for this software is in [LICENSE.md](./LICENSE.md), the other pieces of software installed and used have varying other licenses that you need to be separately aware of.
