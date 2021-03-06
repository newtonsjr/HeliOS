![HeliOS](/extras/HeliOS_OG_Logo.png)

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://github.com/MannyPeterson/HeliOS/blob/master/LICENSE.md) ![GitHub last commit](https://img.shields.io/github/last-commit/MannyPeterson/HeliOS) ![GitHub release (latest by date)](https://img.shields.io/github/v/release/MannyPeterson/HeliOS)
# Overview
HeliOS is an embedded operating system that is free for anyone to use. While called an operating system for simplicity, HeliOS is better described as a multitasking kernel for embedded systems. HeliOS is very small. In fact, it is small enough to run on most 8-bit microcontrollers including the popular AVR based Arduino Uno. Written entirely in C, HeliOS runs on a variety of microcontrollers and integrates easily into any project. HeliOS is also easy to learn with an Application Programming Interface (API) consisting of only 21 function calls. HeliOS contains the following key features:
* Cooperative Multitasking (Run-time Balanced)
* Event Driven Multitasking
* Task Notification/Messaging
* Timers
* Managed Memory
* Non-Preemptive (no mutexs! no deadlocks! no race conditions! no headaches!)

As noted in the key features, HeliOS supports two types of multitasking: cooperative and event driven. Cooperative multitasking embedded operating systems and task schedulers often use a round-robin scheduling strategy. However, HeliOS uses a run-time balanced strategy which ensures tasks with shorter run-times are prioritized over tasks with longer run-times. This ensures all running tasks receive approximately equal total run-time without using context switching. The other multitasking option available in HeliOS is event driven multitasking, which uses the wait/notify and timer interfaces. Mixing cooperative and event driven tasks in HeliOS is not a problem.
# Why HeliOS?
There are already so many great and proven RTOS and scheduler options available, then why HeliOS? That is certainly a valid question. HeliOS was never intended to replace or compete with the other options already available today (if you have not checked out FreeRTOS - you should, it is nothing short of an amazing RTOS for embedded and is more accessible today than ever before through the Arduino Library Manager). HeliOS, however, due to its size and simplicity, is intended to play in the space between RTOS's and task schedulers. HeliOS is tiny (smaller than FreeRTOS), easy to use and a great place to start for enthusiasts, hobbyists and researchers.
# Getting Started
## Arduino
Because HeliOS is compliant with the Arduino 1.5 (rev. 2.2) Library Specification, getting up and running is quick and easy. HeliOS can be installed directly from the Arduino Library Manager or downloaded and installed manually. Both options are described [here](https://www.arduino.cc/en/Guide/Libraries#toc3). Once up and running, check out one of the example sketches or refer to the HeliOS Programmer's Guide in the Documentation section.
## Other Microcontrollers
While built-in support currently exists for the Arduino AVR, SAM and SAMD architectures, HeliOS is easily ported to a variety of other microcontrollers.
# Documentation
The HeliOS Programmer's Guide is a work in progress. What is available today can be found [here](/extras/HeliOS_Programmers_Guide.md). If you are interested in contributing to the HeliOS Programmer's Guide, please see the Contributing section for details.
# Example
Many embedded projects on microcontrollers implement what is called a "super loop". A super loop is a loop that never exits (i.e while(1){}) and contains most of the code executed by the microcontroller. The problem with super loops is they can grow out of control and become difficult to manage. This becomes especially challenging given the relatively few options for controling timing (e.g. delay()). Unfortunately the use of delay() to control timing also means the microcontroller is unable to perform other operations (at least without the help of an ISR) until delay() returns. Below is an example of how easy it is to leverage the event driven multitasking capabilities within HeliOS.
## Arduino "Blink" Example
Below is a copy of the traditional Arduino "Blink" example code.
```C
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(1000);
  digitalWrite(LED_BUILTIN, LOW);
  delay(1000);
}
```
## HeliOS "Blink" Example
Below is the Arduino "Blink" example code implemented using HeliOS. In this example, a HeliOS task, which alternates the microcontroller's GPIO pin state between high and low, is added in a "wait" state and a timer is set instructing HeliOS's scheduler to execute the task every 1,000,000 microseconds. That's it!
```C
#include <HeliOS_Arduino.h>

int ledState = 0;

void taskBlink(int id_) {
	if(ledState) {
		digitalWrite(LED_BUILTIN, LOW);
		ledState = 0;
	} else {
		digitalWrite(LED_BUILTIN, HIGH);
		ledState = 1;
	}
}

void setup() {
	xHeliOSSetup();

	pinMode(LED_BUILTIN, OUTPUT);

	int id = xTaskAdd("TASKBLINK", &taskBlink);
	xTaskWait(id);
	xTaskSetTimer(id, 1000000);
}

void loop() {
	xHeliOSLoop();
}
```
# Releases
All releases, including the current release, can be found [here](https://github.com/MannyPeterson/HeliOS/releases).
* 0.2.2 - Additional function calls, minor fixes and documentation enhancements
* 0.2.1 - The first official release
# Contributing
While all contributions are welcome, contributions are needed most in the following areas:
* HeliOS Programmer's Guide
* Performance Enhancements
* Stability Enhancements
* Testing

To contribute, please create an issue and indicate how you would like to contribute. Please fork from the **develop** branch as **master** is kept even with the current release.
# License
HeliOS is copyrighted open source software licensed under the Free Software Foundation's GNU General Public License Version 3. The license can be found [here](/LICENSE.md).
# Important
HeliOS is **not** certified for use in safety-critical applications. The HeliOS source code, whether in full or in part, must **never** be used in applications where a risk to life exists.
