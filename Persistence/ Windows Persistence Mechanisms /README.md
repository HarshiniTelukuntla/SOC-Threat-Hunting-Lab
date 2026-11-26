# 📁 PROJECT 1: Threat-Hunting
# 🎭 "The Disguised PowerShell"

> *The best hiding place is in plain sight. So that's where I looked.*

---

## 🎬 The Setup

PowerShell is the Swiss Army knife of Windows. Admins love it, and attackers love it even more, because malicious commands can hide inside something the system already trusts.

So I ran an experiment. I became the intruder on my own lab machine, then switched sides and hunted myself using only the clues left behind.

## 🕵️ The Investigation, Scene by Scene

### Scene 1: The Mask 🎭
I ran an **encoded PowerShell command**, a string of gibberish that hides what it really does. Then I decoded it to see the true intent.

![Encoded PowerShell command being executed](screenshots/01-encoded-command.png)
*📸 Evidence 1: The encoded command as it ran in PowerShell.*

![Decoded command revealing its real purpose](screenshots/02-decoded-command.png)
*📸 Evidence 2: The same command after decoding.*

### Scene 2: The Delivery 📦
I made PowerShell **download a file** from the internet, the first step of many real attacks.

![PowerShell download activity](screenshots/03-download-activity.png)
*📸 Evidence 3: The download caught in action.*

### Scene 3: The Paper Trail 📜
Windows keeps a diary, and I read two key pages:
- **Event ID 4688** records every process that was born, and who started it
- **Event ID 4104** records the actual script text PowerShell executed

![Event ID 4688 process creation in Event Viewer](screenshots/04-event-4688.png)
*📸 Evidence 4: Event ID 4688, the process creation record.*

![Event ID 4104 script block logging](screenshots/05-event-4104.png)
*📸 Evidence 5: Event ID 4104, the script block that PowerShell logged.*

### Scene 4: The Proof 🔒
I verified the **downloaded file** really landed on disk.

![Downloaded file confirmed on disk](screenshots/06-file-on-disk.png)
*📸 Evidence 6: The file sitting on disk, proof the attack step worked.*

## 💡 What I Found
> ✍️ *Add 2-3 lines in your own words: what did the logs reveal? What surprised you?*

## 🧰 Tools Used
PowerShell · Windows Event Viewer · Sysmon · Splunk

## 🛡️ The Takeaway
Attackers disguise commands, but Windows still writes down what happened. The skill is knowing where to look.

---
---

# 📁 PROJECT 2: Persistence
# 👻 "The Uninvited Guest Who Won't Leave"

> *Getting in is the easy part. Staying in is the real art.*

---

## 🎬 The Setup

Picture an intruder who breaks into a house. A clever one doesn't just steal and run. They hide a spare key under the doormat so they can walk back in tomorrow.

On a computer, that spare key is called **persistence**: a trick that makes malware survive a reboot. I created the hiding spots myself, then went hunting for them like an investigator.

## 🗝️ The Three Hiding Spots

### 1. The Registry Run Keys 🔑
The "welcome mat" of Windows. Anything listed here starts automatically at login.

![Run key entry created in the registry](screenshots/01-registry-run-key.png)
*📸 Evidence 1: The planted entry in the Run key.*

### 2. Scheduled Tasks ⏰
A sleeper agent with an alarm clock. It can run at a set time or at every startup.

![Scheduled task created in Task Scheduler](screenshots/02-scheduled-task.png)
*📸 Evidence 2: The scheduled task in Task Scheduler.*

### 3. Windows Services ⚙️
The sneakiest disguise: a program dressed up as a legitimate background service.

![Suspicious service in the services list](screenshots/03-windows-service.png)
*📸 Evidence 3: The service hiding among the real ones.*

## 🔍 How I Hunted Them
1. 🧪 **Planted it** in a controlled lab
2. 👀 **Found it** by checking the registry, task list or service list
3. 📡 **Traced the telemetry** it left in the logs

![Sysmon or Event Viewer log entry for the persistence activity](screenshots/04-log-evidence.png)
*📸 Evidence 4: The log entry that exposed the persistence attempt.*

4. 🛡️ **Wrote down how to detect it** so it can't hide next time

## 💡 What I Found
> ✍️ *Add your findings: which hiding spot was easiest to spot? Which was hardest?*

## 🧰 Tools Used
Windows Registry · Task Scheduler · Services console · Sysmon · Event Viewer · Splunk

## 🛡️ The Takeaway
An attacker who can't persist has to start over after every reboot. Finding these hiding spots is how a defender forces them to.

---

> 🚧 *Status: in progress. More hunts are on the way.*
