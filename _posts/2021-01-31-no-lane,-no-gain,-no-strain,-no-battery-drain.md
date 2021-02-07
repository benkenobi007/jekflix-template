---
date: 2021-01-31 15:20:41
layout: post
title: "No Lane - No Gain; No Strain - No Battery Drain!"
subtitle: 
description: A not so brief account of my tryst with laptop power management
image: /assets/img/Battery_Drain/battery_drain_background.jpeg
optimized_image:
category: hardware
tags: laptops, battery life
author: Agent 007
paginate: false
---

<div class="contents" style="border-style: solid; border-width: 20px">
    <h2>Contents</h2>
    <ul>
        <li><a href="#background">Background</a></li>
        <li><a href="#the-investigation">The Investigation</a></li>
        <li><a href="#tldr---the-problem-and-the-solution">TL;DR - The Problem & The Solution</a></li>
        <li><a href="#battery-drain-vs-system-performance-modes">Battery Drain Vs. System Performance Modes</a></li>
        <li><a href="#quantifying-the-performance-variation">Quantifying The Performance Variation</a></li>
        <li><a href="#cinebench-r23-results">Cinebench R23 Results</a></li>
        <li><a href="#cinebench-r23---single-core">Logs And Analysis: Cinebench R23 - Single Core Benchmark</a></li>
        <li><a href="#cinebench-r23---multi-core">Logs And Analysis: Cinebench R23 - Multi Core Benchmark</a></li>
        <li><a href="#conclusion">Conclusion</a></li>
    </ul>
</div>

## Background

A couple of laptops at home were being plagued by the most dreaded infection (not COVID; that hasn’t manifested as a computer virus yet!) – the battery would drain incredibly fast when they were sleeping – it would completely discharge from 100% in less than a day !

![Laptop Details](/assets/img/Battery_Drain/table1.JPG "Table 1 - Laptop Details")

The first laptop was an Ideapad S340-14API powered by the Ryzen 5 3500U (Zen + architecture), while the other was an Asus Vivobook 14 with a beastly Ryzen 7 4700U (Zen 2 architecture) under the hood.

## The Investigation
My first suspect was the CPU support for [**D3HOT**](https://docs.microsoft.com/en-us/windows-hardware/drivers/kernel/device-sleeping-states). D3HOT is a PCIe low power state which is entered in sleep by the endpoint devices (all devices connected to the PCIe bus).
If properly supported, the power draw will become equivalent to the NVMe PS4 state for the SSDs (few hundred milliwatts). D3HOT support in SSDs was required by Microsoft and Intel’s introduction of Modern Standby, which is what powers the ubiquitous [“fast boot”](https://docs.microsoft.com/en-us/windows-hardware/test/weg/delivering-a-great-startup-and-shutdown-experience) technology found in Windows 8 and 10. Since Intel was involved in the development of D3HOT, AMD’s support of D3HOT came into question.

However, battery drain in a friend's ROG Zephyrus G14 with a Ryzen 7 4800HS (also Zen 2 based) was perfectstyle="color: white;"y settings in it, let alone power management! Luckily enough a **Windows Update seemed to fix the issue on the Asus Laptop**.

The Lenovo BIOS was more generous with its settings, though still pretty limited with what one could do. Nevertheless, 2 settings looked interesting – **System Performance Mode** and **Always On USB**.

![Power Management Settings](/assets/img/Battery_Drain/pic1.png "Fig. 1 - Power Management Settings (Ideapad S340)")

**System Performance Mode** has 3 possible configurations -
- **Intelligent** - To quote Lenovo, *“Lenovo Intelligent Cooling”* enables *“automatic adjust thermal control under different user scenario”*
- **Performance** - As the name suggests, this provides *“best performance”*
- **Quiet** - This mode *“Balances Performance with acoustics”*

<!--![System Performance Modes](/assets/img/Battery_Drain/pic2.png "Fig. 2 - System Performance Modes")-->

![SPM Descriptions](/assets/img/Battery_Drain/pic3.png "Fig. 3 - System Performance Modes Description")

No information is given about what specific parameters are being modified, and it is also unclear why system performance levels are being called *“Thermal Control Methods”*. The vague descriptions notwithstanding, I went ahead and tried out the different power modes to see their impact on battery life while the laptop was in sleep. Unsurprisingly, **these didn’t make much of a difference in the battery drain**.

In contrast, **Always On USB** seemed much more promising.
![Always On USB Options](/assets/img/Battery_Drain/pic4.png "Fig. 4 - Always On USB Configuration")

This was **Enabled** by default. Consequently, power to the 3 USB ports is supplied even when the laptop goes into “low power states” – suspend/hibernate. This feature is very common and generally should not cause any major battery drain.

However, disabling this feature seemed to magically fix the battery drain. Given the CPU itself works well on other laptops, the most promising suspect is a faulty ACPI (Advanced Configuration and Power Interface) driver, which supplies very high power to the USB ports even when the device is on standby.

## TL;DR - The Problem and the Solution

![Battery Drain Animation](/assets/img/Battery_Drain/battery_drain.gif "Battery Drain Representation")

In a nutshell, to allow phones/other devices to charge even when the laptop is asleep, the battery continues to supply power to the USB root hub. The problem with Lenovo's hardware/ ACPI firmware is that the USB root hub consumes too much power, causing the battery to drain rapidly.

Disabling the power supply to the root hub prevents battery drain while the device is in low power states (sleep /hibernate) - consequently avoiding the gargantuan battery drain.

## Battery Drain Vs. System Performance Modes
Recall that the battery drain variation was negligible with different performance modes when **Always On USB** was **Enabled**, i.e., when the battery drain in Sleep was high. With the main issue of battery drain fixed, we can revisit this topic.

### Battery Drain Metrics
The battery is first charged to 100% and maintained for atleast an hour to avoid random leaks/under-reporting.

### 1. Quiet Mode
Quiet Mode sees a drain of 4% over 6 hours to end at 96%.

![Quiet Mode Battery Drain Graph](/assets/img/Battery_Drain/pic5.png "Fig. 5 - Quiet Mode: Battery Drain Graph")

### 2. Intelligent Mode
Under intelligent mode, the battery drained to 97% - a decrease of 3% over 6 hours. Interestingly, the drain from 100 to 99% takes longer (around 3 hours) than in quiet mode (2.5 hours).

![Intelligent Mode Battery Drain Graph](/assets/img/Battery_Drain/pic6.png "Fig. 6 - Intelligent Mode: Battery Drain Graph")

### 3. Performance Mode
Over 6 hours, the battery drained 5% to end at 95%. The rate of battery drain is by far the highest while in performance mode.

![Performance Mode Battery Drain Graph](/assets/img/Battery_Drain/pic7.png "Fig. 7 - Performance Mode: Battery Drain Graph")

## Quantifying The Performance Variation
*Is the battery drain,*  
*All in vain,*  
*Or is it a necessary pain?*  

We have established that the different performance modes have a tangible impact on battery drain. We will now look at how performance is affected by these performance modes. 

We will restrict ourselves to CPU performance alone, and not consider other factors like GPU performance, I/O performance, etc. This is because the performance of the system as a whole should change similar to the CPU when dealing with power and thermals. For example, it is very unlikely that we see a reduction in CPU performance and an increase in the performance of the GPU.

<div style="color: white; background-color: #001f68; border-style: double; border-width: 20px; border-radius: 10%;">
<h3 style="text-align: center">Benchmark Selection and Performance Logging</h3>
<p style="color: white;">  
We will be using <a href="https://www.maxon.net/en/cinebench">Cinebench R23</a> to benchmark the CPU. Cinebench has independent workloads for single threaded and multi threaded performance, both of which will be used by us.
</p>
<p style="color: white;">
For logging the parameters of the CPU, we'll make use of <a href="https://www.hwinfo.com">HWiNFO</a> - the swiss army knife for logging data.
Best possible airflow and room temperature are maintained while running the benchmark. The tests were all done at night time when the ambient temperature was low, and provision for plenty of airflow around the vents of the laptop were provided to keep it an apples to apples comparison as far as possible.
</p>
<p></p>
</div>

## Cinebench R23 Results

![Cinebench R23 Scores](/assets/img/Battery_Drain/pic8.png "Fig. 8 - Cinebench R23 Scores")

![Cinebench R23 Scores - Table](/assets/img/Battery_Drain/table2.png "Table 2 - Cinebench R23 Scores")

Single core performance increases by about 5% as we move from Quiet mode to Intelligent mode. Multi core performance increases by 22%, implying that the single core gains are carried uniformly across all 4 cores leading to the 4x multi core gain.  
Going from Intelligent mode to Performance mode, we see a 4.4% increase in single core performance. However, we only get 2% increase in multi core performance, which could be due to a number of factors - the all core frequency limit being reached, the max. power draw (15W TDP) being hit, and so on. To understand this better let’s look at the HWiNFO logs.

### Logs and Analysis
We’ll be looking at the clocks, voltages, temperatures and power variations. 

### Cinebench R23 - Single Core

The single core benchmark works by alternately loading each CPU core with the rendering workload.

### Maximum Single Core Frequency
Since only one CPU core is fully loaded at a time, we can safely consider the rest of the cores to be idling. We hence take maximum clock frequency at any given time as the parameter.

![Max. Frequency - Quiet](/assets/img/Battery_Drain/pic9.png "Fig. 9 - Max. Frequency in Quiet Mode")

![Max. Frequency - Intelligent](/assets/img/Battery_Drain/pic10.png "Fig. 10 - Max. Frequency in Intelligent Mode")

![Max. Frequency - Performance](/assets/img/Battery_Drain/pic11.png "Fig. 11 - Max. Frequency in Performance Mode")

We see that the max clock move between between 3500 and 3700 MHz for all 3 modes, though the dips below 3500 are much less pronounced in performance mode.

### VCore
The core voltage is the actual voltage supplied to the CPU by the voltage regulator. The greater the voltage, the more the performance headroom.

![Core Voltage - Quiet](/assets/img/Battery_Drain/pic12.png "Fig. 12 - Core Voltage in Quiet Mode")

![Core Voltage - Intelligent](/assets/img/Battery_Drain/pic13.png "Fig. 13 - Core Voltage in Intelligent Mode")

![Core Voltage - Performance](/assets/img/Battery_Drain/pic14.png "Fig. 14 - Core Voltage in Performance Mode")

In Quiet Mode, the Vcore varies between 1.15 and 1.18V for half of the test, and then drops to 1.12-1.15V. This may have been done in order to keep the laptop “quiet” even when the temperature increased. Since the fans can't spin too fast (because the laptop won't remain "quiet") the voltage is instead reduced to control the temperature.  
In intelligent mode, the Vcore varies between 1.2 and 1.24V, whereas in Performance mode it remains relatively steady at 1.22V. This explains the stabler clocks observed in the previous section.

### Power Draw
Power drawn is closely related to the voltage supplied. The greater the power supplied, the greater the clocking/performance headroom.

![Power Draw - Quiet](/assets/img/Battery_Drain/pic15.png "Fig. 15 - Power Draw in Quiet Mode")

![Power Draw - Intelligent](/assets/img/Battery_Drain/pic16.png "Fig. 16 - Power Draw in Intelligent Mode")

![Power Draw - Performance](/assets/img/Battery_Drain/pic17.png "Fig. 17 - Power Draw in Performance Mode")

Surprisingly, Quiet Mode consumes the maximum power for the first half of the test, crossing 7W on many occasions, before dropping to around around 6.5W. This correlates with the drop in the voltage and the lower score.   
The Performance and Intelligent mode power draws are very similar with the performance mode being slightly more steady.

<div class="quick-note" style="color: white; background-color: #001f68; border-style: double; border-width: 20px; border-radius: 10%;">
<h3 style="text-align: center">TDP vs Power consumed</h3>
<p style="color: white;">
You may have noticed that the actual power consumed by the CPU is nowhere near the "TDP" rating of 15 W.<br>  
TDP stands for <a href="https://en.wikipedia.org/wiki/Thermal_design_power">Thermal Design Power</a>. This is the absolute maximum amount of <span style="font-weight: bold;">heat (resistive power)</span> that can be generated by the CPU, and the cooling system design should be targeted at the TDP. <br>
This extreme power dissipation is very difficult to reproduce, and hence is generally rarely observed, especially in thin and light laptops.<br>

Power consumed on the other hand is the actual power drawn by the CPU measure by the power sensors (which are logged by HWiNFO).
</p>
<p></p>
</div>

### CPU Temperature
Temperature is another parameter closely connected with the power, clock, and cooling mechanisms. If the temperature is too high, the CPU starts throttling and hence performance is reduced.

![CPU Temperature - Quiet](/assets/img/Battery_Drain/pic18.png "Fig. 18 - CPU Temperature in Quiet Mode")

![CPU Temperature - Intelligent](/assets/img/Battery_Drain/pic19.png "Fig. 19 - CPU Temperature in Intelligent Mode")

![CPU Temperature - Performance](/assets/img/Battery_Drain/pic20.png "Fig. 20 - CPU Temperature in Performance Mode")

As expected, Quiet Mode has the highest temperature thanks to being "Quiet" and not using the cooling system. Intelligent mode is relatively cooler but occasionally spikes up. Performance mode, as expected, has the best cooling performance.

### Cinebench R23 - Multi Core
In contrast to single core performance, the multi core benchmark introduces many more constraints on the maximum clock attainable, power available, and so on. We’ll now look at the same parameters logged in the Cinebench multi core benchmark. The test conditions remain the same as before.

### Clock Frequencies
Since all the cores are loaded we’ll be looking at the frequency characteristic for each core.

![Frequencies - Quiet](/assets/img/Battery_Drain/pic21.png "Fig. 21 - Frequencies in Quiet Mode")

![Frequencies - Intelligent](/assets/img/Battery_Drain/pic22.png "Fig. 22 - Frequencies in Intelligent Mode")

![Frequencies - Performance](/assets/img/Battery_Drain/pic23.png "Fig. 23 - Frequencies in Performance Mode")

In Quiet Mode, all 4 cores initially run at around 2500MHz, before throttling to just above 2100 MHz. The core clocks go all over the place as the test completes. This could be because some cores which have completed rendering go to idle, which gives the remaining cores more breathing space.  
The clocks in Intelligent mode are initially around 2900MHz, before falling to 2800 MHz. In Performance mode, the clocks consistently remain around 2900MHz. This 100MHz clock difference accounts for the relatively small gap seen in the benchmark score. The almost 800MHz delta between Quiet and Intelligent/Performance Modes explains the massive difference in score.

### VCore

As previously stated, the core voltage is the actual voltage supplied to the CPU by the voltage regulator. The greater the voltage, the more the performance headroom.

![Core Voltage - Quiet](/assets/img/Battery_Drain/pic24.png "Fig. 24 - Core Voltage in Quiet Mode")

![Core Voltage - Intelligent](/assets/img/Battery_Drain/pic25.png "Fig. 25 - Core Voltage in Intelligent Mode")

![Core Voltage - Performance](/assets/img/Battery_Drain/pic26.png "Fig. 26 - Core Voltage in Performance Mode")

In Quiet Mode, the voltage remains consistently below 0.9V at around 0.85V until the end, when it spikes up to 1.1V. This corresponds to the frequency spikes observed towards the end.

Intelligent Mode witnesses the voltage moving around 0.96V before falling to 0.92V around 6 minutes into the Benchmark. This corresponds to the frequency drop seen above.

In Performance Mode, the voltage remains consistently around 0.96V throughout the benchmark run.

### Power Draw

Just like VCore, the power draw metrics do not change from Single Core to Multi Core. We measure the CPU package power, which is the total power consumed by the CPU.

![Power Draw - Quiet](/assets/img/Battery_Drain/pic27.png "Fig. 27 - Power Draw in Quiet Mode")

![Power Draw - Intelligent](/assets/img/Battery_Drain/pic28.png "Fig. 28 - Power Draw in Intelligent Mode")

![Power Draw - Performance](/assets/img/Battery_Drain/pic29.png "Fig. 29 - Power Draw in Performance Mode")

In Quiet Mode, the power draw starts at 8W, then falls to 6.5W, corresponding to the frequency drop. The second drop in power draw towards the end accompanying the clock and voltage spikes supports the theory that some cores are completing ahead of others, and consequently the power draw reduces as they go to idle.

In Intelligent mode, power draw stays at around 11W before a drop to 10W corresponding to the frequency and voltage drops seen earlier. 

Performance Mode does not have any major variation in power draw, and almost consistently stays at 11W.

### CPU Temperature

Again, the CPU temperature measurement remains same across single core and multi core benchmarks.

![CPU Temperature - Quiet](/assets/img/Battery_Drain/pic30.png "Fig. 30 - CPU Temperature in Quiet Mode")

![CPU Temperature - Intelligent](/assets/img/Battery_Drain/pic31.png "Fig. 31 - CPU Temperature in Intelligent Mode")

![CPU Temperature - Performance](/assets/img/Battery_Drain/pic32.png "Fig. 32 - CPU Temperature in Performance Mode")

In both Quiet and Intelligent modes, we see the temperature rising very high before going down corresponding to the clock, voltage, and power drops. The temperature seems to be the limiting factor based on which the throttling occurred. In Quiet Mode, temperature peaks at 78C, possibly because the cooling system was insufficient at that temperature itself to maintain operational stability. On the other hand, the temperature starts reaching 90C in Intelligent mode before throttling kicks in. 

Such throttling is not seen in Performance mode, with the temperature rarely crossing 85C. This indicates that the cooling system is completely powered in performance mode.

### Conclusion
To answer the question we previously asked ourselves – no, the battery drain with performance mode is not completely in vain :). However, it is worth noting that the multi core performance uplift from Intelligent to Performance modes isn’t really worth the reduced battery life while in sleep.  
On the other hand, while Quiet Mode gives pretty good battery life while sleeping, it has serious impacts on the performance and heating issues due to the cooling system being inadequately powered.  
Intelligent mode seems to be a balance of both that should suit the average end user.

Now coming to the issue with which we began – the massive battery drain caused by **Always On USB**. Why have all USB ports been enabled with this feature, and why can’t it be selectively applied to retain the ability to use the USB port while the laptop sleeps? Why has it been enabled by default by the manufacturer ? Why doesn’t it get disabled when we set low power consumption profile in Control Panel ?

Food for thought/ Instructions worth processing, no ?
