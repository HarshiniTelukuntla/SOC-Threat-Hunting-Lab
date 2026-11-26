# 📁 PROJECT 1: Threat-Hunting
# 🎭 "The Disguised PowerShell"

> *The best hiding place is in plain sight. So that's where I looked.*

---

## 🎬 The Setup

PowerShell is the Swiss Army knife of Windows. Admins love it, and attackers love it even more, because malicious commands can hide inside something the system already trusts.

So I ran an experiment. I became the intruder on my own lab machine, then switched sides and hunted myself using only the clues left behind.

## 🕵️ The Investigation, Scene by Scene

**Scene 1: The Mask 🎭**
I ran an **encoded PowerShell command**, a string of gibberish that hides what it really does. Then I decoded it to see the true intent.

**Scene 2: The Delivery 📦**
I made PowerShell **download a file** from the internet, the first step of many real attacks. I wanted to see what that act looks like from the defender's side.

**Scene 3: The Paper Trail 📜**
Windows keeps a diary, and I read two key pages:
- **Event ID 4688** records every process that was born, and who started it
- **Event ID 4104** records the actual script text PowerShell executed

**Scene 4: The Proof 🔒**
I verified the **downloaded file** really landed on disk. A suspicion is only a theory until the evidence is in hand.

## 💡 What I Found
> ✍️ *Add 2-3 lines in your own words: what did the logs reveal? What surprised you? Which event was most useful?*

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

**1. The Registry Run Keys 🔑**
The "welcome mat" of Windows. Anything listed here starts automatically at login, which makes it a favorite hiding place for unwanted programs.

**2. Scheduled Tasks ⏰**
A sleeper agent with an alarm clock. It can run quietly every hour, every day, or at every startup, and nobody notices until it's too late.

**3. Windows Services ⚙️**
The sneakiest disguise. A malicious program dressed up as a legitimate background service, blending in with dozens of real ones.

## 🔍 How I Hunted Them
For each hiding spot I followed the same loop:
1. 🧪 **Planted it** in a controlled lab
2. 👀 **Found it** by checking the registry, task list or service list
3. 📡 **Traced the telemetry** it left in the logs
4. 🛡️ **Wrote down how to detect it** so it can't hide next time

## 💡 What I Found
> ✍️ *Add your findings: which hiding spot was the easiest to spot? Which was the hardest? What would you alert on?*

## 🧰 Tools Used
Windows Registry · Task Scheduler · Services console · Sysmon · Event Viewer · Splunk

## 🛡️ The Takeaway
An attacker who can't persist has to start over after every reboot. Finding these hiding spots is how a defender forces them to.

---

> 🚧 *Status: in progress. More hunts are on the way.*
