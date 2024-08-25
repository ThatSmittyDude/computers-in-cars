# computers-in-cars?
Automotive ECUs

INTRO

So, the Dell Optiplex—most of you are at least familiar with seeing these at work, right? A common configuration includes a Core i5, 16GB of RAM, and 500GB of storage with Windows 10 or 11. This is a modern-day example of x86 superiority in the world of desktop computing.
Many of you are more familiar with phones, tablets, and gaming consoles when it comes to personal computing. Hell, many of you are probably watching this video on one of those devices.
But what about the 3,500-pound elephant in the room? Or driveway, I guess...
Yes, your car has a computer. Actually, more like a dozen computers. Newer cars even have somewhere around 50 of them. Confused yet? Me too, and I work on cars for a living.

AUTOMOTIVE ECU

Okay, now what in the world is an automotive computer? I mean, I’ve never had to fill out a spreadsheet on my dash display. Well, like your gaming systems and smartphones, computers can come in all sorts of forms and functions. In this case, your car is a network of small computers that manage operations, diagnostics, features, and much more.

These automotive computers are usually referred to as modules or ECUs (Electronic Control Units) in the industry. They are much simpler than a full-blown gaming PC and run quietly in the background while you're driving. So let’s take a look at an example.

OUR EXAMPLE ECU

The example we have today is an ECU that controls the airbag system in a vehicle. This particular ECU is called the Occupant Restraint Controller (ORC). So you might wonder, why do we need such a thing? It has to do with safety, redundancy, and diagnostics.

If we were to simply wire the airbags to a crash sensor, like the one in the grille area, that would work, right? If the crash sensor detects a crash, it completes a circuit and sets off the airbag. This seems to work just fine. However, what if it was a false reading, like hitting a speed bump too hard, a short circuit, or a faulty crash sensor? That would not be a good day for the person behind the wheel of that car.

Another bad day could be on the mechanic’s side. Let’s say I have to replace an airbag inflator. I’m hunched over the steering wheel trying to remove the bolts located between the back of the wheel and the dash. At this particular moment, I’m extremely vulnerable if the airbag system had a wire with some insulation rubbed off or a connector that has been corroded. If the wiring wiggles while I’m working on the wheel, that could be game over for me.

So if we were to change the system to run with a computer, how can that help us? At the very least, we can add more crash sensors and even rollover sensors. These can be controlled with software. This software can be tested for a variety of different conditions and can be programmed to work in any way we want.

So if we need to actuate the airbag, let’s first make sure someone is in the seat. We can use various types of occupant detection equipment. In this case, let’s use a weight sensor. Yes, your car can more than likely weigh you. It’s usually a sensor integral to the seat itself. So now that we have a weight sensor, we can know someone is there and how much they weigh. If we have a smaller occupant, we can set off the weaker stage of the airbag. This can be implemented with a simple line of code—a line of code that just gave us two functions from one sensor.

On top of this functionality, we can add updates. Yup, just like your phone or your computer, your car is often subject to updates. If we find that there is a situation where the airbag could be unsafe, like when I was removing the steering wheel, we can simply update the software to include more safety features. It’s actually quite common for airbag connectors to have shorting bars on the safety lock. This will cause the system to short circuit. But isn’t that bad? Unlike before, when a short would cause a detonation of the airbag, we can make the ORC disable the airbags when it detects a short. Short circuits tend to have different voltage and amperage measurements than a healthy circuit. So we can use those measurements to disable the system—no added parts.

So that brings us to diagnosis. Like the shorting bar from before, we can add code that checks measurements and adjusts functionality. In this case, let’s say the weight sensor under the seat has corroded and failed. If the ORC finds the weight to read 0 lbs but the car is driving, there is likely a problem. In this case, we cannot trust that sensor, so we can disable the airbag system to not cause harm to any occupants, and we can notify the driver there is an issue by lighting up the airbag light on the dash.

So now that the customer noticed the light, they decided to bring the vehicle to the shop. The mechanic scans the vehicle and finds there is an open circuit code for the airbag system. Here’s where there is a common misconception: The scanner usually doesn’t tell you what’s wrong. But it does give you a good idea of what circuit or component is not operating up to par. It is important that the mechanic verifies the concern and also the diagnosis of that concern. Many issues can be misdiagnosed by simply replacing a part that has a specific code.

SO HOW DOES THIS LITTLE BOX WORK?

So now that we have a way to use our little ECU, we need to know the ins and outs of this little machine. How in the world do we even interface with it? Let’s look back to where we came from: there was a scanner, circuits with voltages, and malfunction lights. These are the most common ways to interface with the ECU. These computers don’t have a monitor with DOOM running on them—at least not yet. They are more like a development board. Development boards like the STM32, Raspberry Pi, or even a BeagleBoard.

So what architecture are they? Something weird and proprietary? Yes and no. There are a ton of MIPS architecture-based boards and also a ton of ARM Cortex-based boards. There are also many others like Texas Instruments’ C2000 and Infineon’s Aurix. There aren’t a lot of x86-based systems; they would be a bit overkill and cause a lot of power draw, which is not ideal. Plus, x86 is pretty expensive in comparison to the RISC alternatives.

COMMS

So these little boxes need to talk to each other. For example, imagine a situation where the driver is merging onto a highway and the accelerator is most of the way to the floor. In this case, the Powertrain Control Module (PCM) can tell our Transmission Control Module (TCM) that we need to shift more aggressively.

The most common form of communication between these ECUs is a Controller Area Network (CAN). It’s a square wave frequency that sends data over a wire, and that’s the simplest way I can put it for now. Some modules use a Local Interconnect Network (LIN), which tends to have higher voltages. Some newer vehicles have Ethernet. Yes, that Ethernet.

If you look at your STM32 GPIO, it somewhat resembles the pins on our example ECU. That’s simply because it works in mostly the same way. If we flash this STM32 with code to blink the LED on the board, it is really no different than flashing the Body Control Module (BCM) to flash the four-way hazard lights. It simply just uses a different protocol to flash. Every car from 1996 to the present uses On-Board Diagnostics 2 (OBD2) as the interface. It’s the same plug under the dash we use to scan for codes, and like how the ECUs communicate, the OBD2 system uses CAN to flash the ECU.

The more involved explanation of how this works is quite interesting. These ECUs communicate with the CAN Bus using serial data. This means that the data is sent in series—each bit is sent one after another.

CAN protocol involves two wires controlling data: CAN High and CAN Low. The way they send bits is through voltage. If CAN High is something like 2.8 volts and CAN Low is 2.6 volts, that registers a 0. If CAN High is reduced to equal CAN Low at 2.6, that registers a 1. Ones and zeros. But each message is sent to the entire CAN Bus. It is up to each ECU to determine if the message is to be processed or ignored. The way this is done is with a header file. Each ECU has its own unique ID. So if the BCM needs to communicate with the ORC, it will send out a beacon with the ID of the ORC. This is an 11- or 29-bit message known as a header that includes a module ID, the length of the message, and possibly some other info like the priority of the message. If the ORC sees its ID on the Bus, it will know that it needs to process the message. Once it processes the message, it can set what’s known as a flag. It can set a bit in a header to the Bus with the BCM’s ID to acknowledge that it received the BCM’s message. This is known as an ACK. Then it is up to these two modules to determine if they want to synchronize (SYN). If they decide they want to communicate, they can have the ACK-SYN flag set to continue communication.

What if they don't want to communicate?

After all, this is a real option in this situation. It’s possible that the module has gone what's called dormant. This means that, for some reason—usually due to a particular measurement or message—the module does not think it is safe to communicate or even operate in some cases. Often, this is due to a physical electrical issue with the vehicle. The module will no longer communicate on the Bus until it meets the conditions to safely operate. If there is a short in the system, that will need to be fixed before the module is willing to communicate again, and it usually requires a reset after the repair for the module to wake up.

Wrapping it up

We’ve learned how these automotive machines function in their environments. My question to the reader is this: What can we do to bring automotive tech further into the 21st century? After all, most of this was engineered in the early to mid-1990s. Is there a better way to ensure the safe, efficient operation of modern cars?

All of these little gadgets throughout the vehicle require electricity. I've seen key-on draws of up to 29 amps. Yes, 29 amps. That is a lot of current running through a modern vehicle. Batteries are the weak point in this system. They often need to be charged or replaced due to the massive, consistent load placed on them. I personally think we should find a more efficient way to manage the electrical load on the vehicle. What do you think?
by ThatSmittyDude
ThatSmittyDude@outlook.com
tiktok@that_smitty_dude
youtube@that_smitty_dude
discord@that_smitty_dude
Reddt@that_smitty_dude_17
