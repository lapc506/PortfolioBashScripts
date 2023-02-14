# Portfolio Bash scripts I've implemented during my career

## macOS_Upgrade_Reboot_Deferral_with_Jamf_Parameters_Intel_Macs.sh

_Works only when deploying this script using a policy for Intel-based Macs only._

### Problem Statement
[Computers with Apple silicon (i.e., M1 chip) cannot be updated using a policy if a restart is required.](https://learn.jamf.com/bundle/jamf-pro-documentation-current/page/Running_Software_Update_Using_a_Policy.html)

When this script gets tested on Apple Silicon-based Macs, by executing a Jamf policy that includes it,
it is unavoidable that users get a prompt asking them for an administrator password.

This happens because [automated, non-interactive updates and upgrades on a Mac with Apple Silicon processor,
will only occur while authenticated using an MDM bootstrap token, starting macOS 11.2 or later, and the update being installed must be signed by Apple.](https://support.apple.com/guide/deployment/about-software-updates-depc4c80847a/web)

### Solutions Attempted (before this script was implemented)

Jamf has updated their documentation for Jamf Cloud version 10.43.0.

In their technical article [Remote Commands for Computers](https://learn.jamf.com/bundle/jamf-pro-documentation-current/page/Remote_Commands_for_Computers.html), it states that manually pushing a Major Upgrade selecting the option **“Download/Download and Install Updates”** will allow deferral on the _update_ execution _(not deferring the reboot)_ when selecting the Install Action **“Download and allow macOS to install later”**.

According to the same article, when sending the command via a mass action, the **“Update OS version and built-in apps”** option must be selected.
It seems this option does not allow for update deferral nor reboot deferrals.

### Verifying that the Bootstrap Token has been escrowed into the MDM server

To have the update for computers with Apple silicon (i.e., M1 chip) installed automatically without user interaction,
a Bootstrap Token for target computers must be escrowed to the MDM solution server (e.g. Jamf Cloud or Jamf Pro on-premises).

According to official Apple Platform Deployment documentation, the technical article [About software updates for Apple devices](https://support.apple.com/guide/deployment/about-software-updates-depc4c80847a/web) on its section **"macOS software update or upgrade process"** states:

The `profiles` command-line tool has a number of options to interact with the bootstrap token:

* `sudo profiles install -type bootstraptoken`

  * This command generates a new bootstrap token and escrows it to the MDM solution. This command requires existing secure token administrator information to initially generate the bootstrap token and the MDM solution must support the feature.

* `sudo profiles remove -type bootstraptoken`

  * Removes the existing bootstrap token on the Mac and the MDM solution.

* `sudo profiles status -type bootstraptoken`

  * Reports back whether the MDM solution supports the bootstrap token feature, and what the current state of the bootstrap token is on the Mac.

* `sudo profiles validate -type bootstraptoken`

  * Reports back whether the MDM solution supports the bootstrap token feature, and what the current state of the bootstrap token is on the Mac
