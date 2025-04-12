# Lab

first we try login with valid credential on rdp server

<figure><img src="../../../.gitbook/assets/Screenshot 2025-03-27 235529.png" alt=""><figcaption></figcaption></figure>

we success to login and we found file call `pentest-open`

<figure><img src="../../../.gitbook/assets/Screenshot 2025-03-27 235811.png" alt=""><figcaption></figcaption></figure>

now let’s change value of `DisableRestrictedAdmin` from 0 to 1

<figure><img src="../../../.gitbook/assets/Screenshot 2025-03-27 234425.png" alt=""><figcaption></figcaption></figure>

```bash
reg add HKLM\\System\\CurrentControlSet\\Control\\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

then let’s try sign in by this hash

<figure><img src="../../../.gitbook/assets/Screenshot 2025-03-27 235825.png" alt=""><figcaption></figcaption></figure>

we found flag

<figure><img src="../../../.gitbook/assets/Screenshot 2025-03-27 235839.png" alt=""><figcaption></figcaption></figure>
