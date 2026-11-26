🕵️** SOC Threat Hunting Lab**

**I break into my own Windows machine, then hunt myself down.**

|Every attack leaves footprints. This lab is my training ground for learning to read them.

🎬 The Story

Imagine a quiet Windows 11 laptop living inside a virtual box. Nothing looks wrong.

Then I play the villain: I run a sneaky PowerShell command, drop a file from the internet, and plant a little something so it survives a reboot. A moment later I put on the detective's coat and ask the questions a SOC analyst would ask:

Who ran that?
What did they run?
Where did it come from?
Would my tools have caught it?

That cat-and-mouse loop, attacker first and defender second, is this whole repository.

🧰 The Toolkit
🔧 Tool	🎭 Role in the story
Windows 11 + VirtualBox	The crime scene: a safe sandbox where nothing real is at risk
PowerShell	The attacker's weapon of choice
Sysmon	The hidden camera that records every process
Windows Event Viewer	The detective's notebook of raw evidence
Splunk	The big evidence board where clues get connected
Wireshark (coming soon)	The wiretap for catching network traffic
🔍 Case Files
📁 Case #1: The Disguised PowerShell

Attackers love hiding commands in plain sight, so I did the same.

I ran encoded PowerShell commands and a download, then tracked the trail through the logs:

🕳️ Unmasked an encoded command to see what it was really doing
🌐 Caught the download activity red-handed
📜 Read the story told by Event ID 4688 (process creation) and Event ID 4104 (PowerShell script block logging)
✅ Confirmed the downloaded file actually landed on disk
📁 Case #2: The Uninvited Guest Who Won't Leave (in progress 🚧)

Getting in is only half of an attack. Staying in is the other half.

Persistence is how an intruder makes sure a reboot doesn't evict them. I'm hunting the three favorite hiding spots:

🗝️ Registry Run Keys: programs that quietly start with Windows
⏰ Scheduled Tasks: sleeper agents with a timer
⚙️ Windows Services: backdoors dressed up as legitimate software
🧭 How I Run Every Investigation
📖 Study the trick: learn the attacker technique (with MITRE ATT&CK as my map)
🧪 Recreate it: generate controlled test activity
📡 Collect the clues: gather endpoint and system telemetry
🔎 Investigate: dig through events and validate what is suspicious
🛡️ Write the trap: build detection logic that would catch it next time
📝 Tell the story: document everything with screenshots and findings
🗂️ What's Inside
SOC-Threat-Hunting-Lab/
├── Threat-Hunting/   → Case #1 and other investigations
├── Persistence/      → Case #2: hiding spots that survive a reboot
└── README.md
🎯 Why I'm Doing This

I'm not just reading about threats, I'm learning to think like both sides. The goal is to become the analyst who spots the strange process at 2 a.m. and knows exactly what it means.

Skills I'm sharpening: threat hunting · incident investigation · detection engineering · log analysis · Windows endpoint forensics

⚠️ Fair Play

Everything here happens inside an isolated lab I own. No real systems were harmed or targeted. This is defensive learning only.

⭐ Follow along as the cases pile up. More hunts are on the way.
