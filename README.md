(Updated 10/3/2024)

**Hi, I'm Ben.**

I'm a second-year Rochester Institute of Technology student who is interested in all things technology, particularly with writing code and creating projects.

You'll find many of my personal projects hosted here. Feel free to check them out. I am fairly active on GitHub, so check back again soon for any future project updates.

> [!IMPORTANT]
> **_I am in the process of migrating older projects to GitHub_** so that all my work can be viewed on the same platform.
> Many of my projects are private for the time being, while they are being finished. This includes **Maze Finger**.

## My Thought Process

* Treated as if they were in production or an enterprise environment
* Documented extensively

# My Latest Projects

## autorit

[GitHub Repository](https://github.com/Ben-Does-Arcade/autorit) (coming soon)

Tired of navigating those notoriously awful frontends from RIT's many different online services? It is a known issue that it is difficult to use many of the online services that RIT has for different purposes.
For example, `ondemand.rit.edu` for online ordering, `reserve.rit.edu` for reserving rooms, `mycourses.rit.edu` for course materials, and a lot more.

Every service is essentially a different third-party contractor and there is no consistency across any services.
These services are also quite slow and often take forever to execute any basic commands due to how slow the UI loads.

Are you trying to check your Statistics grades in myCourses? It's gonna take over 2 minutes just to navigate the UI only to view a single number.
This also involves manually enter your credentials for RIT's SSO authentication every single time. This process gets annoying.

`autorit` aims to solve this by providing a consistent SDK for programmatically performing actions quickly, efficiently, and autonomously, across **almost all RIT Online Services.**

### What is `autorit`? How does it work?

`autorit` is a Python package that automatically handles the overhead of all RIT Online Services, letting you focus on what matters most - getting what you want, when you want, in the most efficient way possible.

The brains behind `autorit` involve `selenium`, a popular Python package for automating web browsers, like Chrome, Firefox, and Edge. The headless option was used for this package, eliminating the UI processing times,
lots of overhead, and even removes the need to have a desktop environment. This means that `autorit` is incredibly efficient when it comes to the speed of accessing and performing actions on RIT Online Services as the
only wait times are for user program `sleep()` delays, and actual RIT Online Service backend loading times.

### Automatic RIT authentication

For all services, `autorit` handles authentication for you. All RIT Online Service classes implement the `_Auth` class, meaning you do not have to specifically invoke or handle authentication manually.
Just approve the Duo Push or enter your text passcode that is sent to your mobile device.

Some classes support `retain_cookie`, where it is possible to automatically sign in without the need to do anything else. This is commonly used with `OnDemand` to immediately
invoke an online order without having to worry about RIT's SSO systen. Most services do *not* support this natively for security reasons.

### Connecting RIT Online Services together through automation

This package also empowers the incorporation of automation across multiple different RIT Online Services and your own third-party services, like personal calendars and to-do lists.

* When a quiz on myCourses is due tonight, schedule a room in Wallace Library from 3-4pm
* At 6pm on weekdays, place an online order at Global Village Fresh Cantina & Grill
* When an assignment is uploaded to a personal cloud drive service, upload the file to the associated myCourses dropbox

### Code example

```python
from autorit import Config, AuthMethods
from autorit.services.ondemand import OnDemand

Config.credentials_path = "/Users/ben/Projects"
Config.preferred_auth_mode = AuthMethods.DUO_PUSH

# Initialize OnDemand Service
ondemand = OnDemand(retain_cookie=True)

# Give it order information for checkout
ondemand.first_name = "John"
ondemand.last_initial = "D"
ondemand.phone_number = "8885551234"
ondemand.preferred_payment = OnDemand.PaymentMethod.DINING_DOLLARS

# List the stores that are open
open_stores = ondemand.list_stores(status_filter=OnDemand.StoreStatus.OPEN)
print(f"There are {len(open_stores)} open now.")
print(open_stores)

# Select Beanz
if ondemand.get_store_status("Beanz") == OnDemand.StoreStatus.OPEN:
    ondemand.select_store("Beanz")

    # Order my favorite drink!
    print("Let's order my favorite drink at Beanz!\n")
    ondemand.select_item("Strawberry Sunrise Smoothie")
    ondemand.add_options("Smoothie Base", "Apple Juice")
    ondemand.add_options("Add-ons", "Protein Powder")
    ondemand.set_quantity(1)
    ondemand.add_item()

    # Print the subtotal and total
    subtotal = ondemand.get_subtotal()
    total = ondemand.get_total()

    print(f"Subtotal: {subtotal}")
    print(f"Total: {total}")

    # Attempt to place the order
    try:
        ondemand.checkout(acknowledge_charge=True)
        print("Order placed!")
    except OnDemand.CheckoutError:
        print("Order didn't work...")
else:
    print("Store is closed right now")

# Sign out and terminate the headless browser
ondemand.end_auth()
ondemand.terminate()
```

```
There are 6 stores open now.
["Petals (RIT Inn)", "Artesano Bakery & Café", "Cafe at Crossroads", "The Commons", "Beanz", "Midnight Oil"]
Let's order my favorite drink at Beanz!

Subtotal: $7.10
Total: $7.67
Order placed!
```

> [!NOTE]
> Project is specific to RIT Students only.
> Function identifiers and/or overall functionality and code structure may change on launch.

## quickz

[GitHub Repository](https://github.com/Ben-Does-Arcade/quickz)

`quickz` is a Python library that automatically solves circuits equations for you. You can turn long, tedious circuits into simplified Python code. Then, invoke functions and plug in values into formulas as you need to. It even converts units automatically - no need to worry about manually entering exponential notation to convert to standard units!

For me, it's always far easier to turn anything into code, then work with the code to solve problems from class. This is why I made `quickz` - and on the first exam alone, it has proved to be a very useful tool for solving circuits equations.

The idea is that `quickz` will be able to run on the TI NSpire CX II CAS calculator. This calculator can run Python programs as it has the MicroPython interpreter pre-installed. Of course, this tool has been checked with the instructor first. I am allowed to use this on exams.

### Example Problem

![Circuits schematic](https://github.com/user-attachments/assets/7ee1f7c6-1c2c-476f-b09e-40549a085d89)

![Circuits question from schematic above](https://github.com/user-attachments/assets/048b6498-2833-4a38-82a5-4e56c5b2ad6d)

**Circuit transformed into Python:**

```python
from quickz import *

Settings.auto_eng = True                # Automatically convert values to engineering notation
Settings.auto_print_precision = True    # When printing values, automatically round to the global precision
Settings.precision = 3                  # By default, show 3 decimal place precision

# Define components that are known
V1 = Phasor("14 < 0")
L1 = L("4 Ω")
C1 = C("8 Ω")
R1 = R("12 Ω")

# Combine the resistor and capacitor, R1 and C1 respectively, into one component
P = parallel(R1, C1)

# Add the converted parallel component
Z_total = L1 + P

# Perform Ohm's Law to find total current in a series circuit
I1 = ohms_law(v=V1, z=Z_total)

print(f"Q7 ANSWER = {I1}\n")
```

```
Q7 ANSWER = 3.5 ∠ 22.62°
```

## Secret Keylogger

[GitHub Repository](https://github.com/Ben-Does-Arcade/secret-keylogger) (coming soon)

This project is in collaboration with one of my good friends Noah Wildey.

Keyloggers can be one of the most useful tools to use in cybersecurity. This one is designed to be secret - it's disguised as a typical file, like a PDF `.pdf` or Word document `.docx`. It will take advantage of the fact that Windows machines, by default, have file extensions hidden, meaning it is possible to take advantage of this and make fake extensions like `.pdf.vbs` where the `.vbs` portion will be hidden.

This keylogger sends all keystrokes to a server that runs on another machine (on the same network for now). The keys are logged in real-time, meaning hackers could actively be gaining access into a user's account.

<!--
### Video Ticket Redemption Arcade

I have been working on a video ticket redemption arcade game, which is a physical full-size arcade cabinet which is designed to run games that I program for it. It can adapt to be any game, whether that be card games, game shows, trivia, and more.

The cabinet incorporates:

- **_TW-389 coin mechanism_**, processes .984" arcade tokens and filters out other invalid coins
- **_DL-1275 Arcade ticket dispenser_**, capable of holding up to 4000 real arcade tickets
- **_19" point-of-sale capacitive touch screen monitor_**, supports just about any game imaginable
- **_Full quality stereo sound speakers_** provide unbeatable arcade game sound quality
- **_Dell Optiplex 7040 Micro PC running Ubuntu Desktop_**, configured to automatically turn on and launch the desired game on power up
- **_Custom DAQ module_**, communicates over the serial interface to the PC and handles all hardware inputs and outputs
- **_Industrial revolving beacon light_** to celebrate a player's jackpot win
- **_Mechanical bell module_** mounted inside cabinet to draw attention to the machine in ways speakers can't
- **_PC tray with mounted USB 3.0 port for flash drives_**, provides easy access to servicing hardware, USB port allows for loading new games and holds configuration data

### Maze Finger

The first game I am making for it is **Maze Finger**, a fast-paced maze game where the goal is to find the hidden exit as fast as possible. But be quick, the timer gets faster as you complete each level!

> [!NOTE]
> Repository for Maze Finger is coming soon.

### Screenshots

<p float="left">
  <img src="https://github.com/Ben-Does-Arcade/Ben-Does-Arcade/blob/main/Maze%20Finger%20Screenshot%201.png?raw=true" width="300">
  <img src="https://github.com/Ben-Does-Arcade/Ben-Does-Arcade/blob/main/Maze%20Finger%20Screenshot%202.png?raw=true" width="300">
  <img src="https://github.com/Ben-Does-Arcade/Ben-Does-Arcade/blob/main/Maze%20Finger%20Screenshot%203.png?raw=true" width="300">
  <img src="https://github.com/Ben-Does-Arcade/Ben-Does-Arcade/blob/main/Maze%20Finger%20Screenshot%204.png?raw=true" width="300">
</p>

### Video

https://github.com/user-attachments/assets/1060cd7c-0b47-4cf6-a6aa-d37f54b61eeb

### Other Game Ideas

Some other games I eventually want to make for this arcade cabinet:

- Wheel of Puzzles, a [Wheel of Fortune](https://www.wheeloffortune.com/) remake
- 4 Colors, an [UNO-style](https://www.unorules.com/) card game
-->

<!---
Ben-Does-Arcade/Ben-Does-Arcade is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
