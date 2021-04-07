# REALITY_SMASHER
vRealize RCE + Privesc (CVE-2021-21975, CVE-2021-21983, CVE-0DAY-?????)

*"As easy to stop as it is to comprehend."*

## What is it?
"Reality Smasher" is an exploit for vRealize leveraging the security issues addressed in [VMSA-2021-0004](https://www.vmware.com/security/advisories/VMSA-2021-0004.html) (CVE-2021-21975, CVE-2021-21983) in addition to a privilege escalation vulnerability discovered during testing.

## How does it work?
"Reality Smasher" exploits CVE-2021-21975 to steal credentials for vRealize's "maintenanceAdmin" account. This is achieved by hosting an HTTPS listener using the key and certificate included in this repository. These credentials are used in all subsequent authenticated requests. First, the admin account's hashed passwords are requested and stored for later use.

Next, a request is made to enabled the SSH service. This is followed by an overwrite of the "admin.properties" file using CVE-2021-21983. A request to set the admin user's initial password is made next. This is allowed because admin's credentials were stored in "admin.properties". Without this file, vRealize believes that credentials had never been set and accepts the unauthenticated "/security/adminpassword/initial" request.

After validating that the new admin credentials allow for SSH access, ssh keys are generated. The privilege escalation vulnerability is then used to permit root SSH login, restart the sshd service, and append the public key to "/root/.ssh/authorized_keys." After root SSH access has been validated, the original hashed passwords are restored, and the user is granted an interactive SSH shell as the root user. All SSH operations are performed using "golang.org/x/crypto/ssh".

## Is it safe?
Significant effort was made to ensure the reliability and robustness of this exploit. However, this is my first golang project, so **use this exploit at your own risk**. It is recommended to run this exploit with verbosity to capture hashed passwords and the generated private key.

## Demo

![REALITY_SMASHER](https://user-images.githubusercontent.com/8302932/113794010-31c17e00-970f-11eb-9bf5-1ebe25d0ecb3.gif)
