---
date: 2019-10-13 22:15:49
layout: post
title: "RX 560X Overclocking Guide"
subtitle: 
description: The AMD Radeon &trade; RX 560X GPU has become a very popular choice for budget gaming laptops,
                        including the Acer Nitro series, the Asus TUF series, and the Dell Inspiron Gaming series.
                        This post is a guide for enthusiasts interested in extracting its maximum
                        performance without risking damage to the laptop.<br/>
                        The post is targeted at beginners, and no prior knowledge of overclocking/tweaking is needed.
image: /assets/img/RX560X_OC/homescreen_thumb.jpg
optimized_image:
category: hardware
tags:
author: Agent 007
paginate: false
---

<div style="background-color: #ffbf00; border-style: double; border-width: 20px; ">
<h3>Before We Begin</h3>
                <ul>
                    <li>Overclocking, undervolting, or tweaking any other GPU settings will
                        <span style="color: red; font-weight: bold">void the warranty</span> .<br>
                        Remember to disable Wattman and uninstall Afterburner before visiting the service centre !</li>
                    <li>Avoid increasing the voltage and the power limit, even by small amounts.<br>
                        Since laptops are optimized for power efficiency, chances are the motherboard
                        won't be able to sustain the supply and may get damaged.</li>
                    <li>This guide minimizes the voltage and maximizes the clock speed.<br>
                        Note that better results may be obtained simply by increasing the clock speed and the voltage,
                        but the risks outweigh the benefits.
                    </li>
                </ul>
</div>

<h3>Prerequisites</h3>
<ul>
    <li>
        The latest <a href="https://www.amd.com/en/support">Radeon Adrenalin Driver</a>.
        Version >= 19.3.1.
    </li>
    <li>
        <a href="https://www.msi.com/page/afterburner">MSI Afterburner</a>,
        for enabling Wattman and verifying the overclock.
    </li>
    <li>
        <a href="https://benchmarks.ul.com/3dmark">3DMark</a> benchmark suite,
        for measuring the performance gain.<br>
        Download the basic edition and install the <span style="font-weight: bold">FireStrike benchmark</span>.
    </li>
    <li>
        Excel, notepad, or any other method to note down the results.
    </li>
    <li>
        Patience. In sufficiently high quantities :)
    </li>
</ul>

<h3>Step 1: Enable Wattman</h3>
<p>
    Wattman is AMD's utility for tuning various parameteres of the GPU, including GPU clocks,
    GPU voltages, memory clocks, GPU fan-speeds, etc. It is available as part of the Adrenalin driver
    for desktop GPUs. However, it is disabled for mobile (laptop) GPUs by default due to the risk of damage
    to the laptop.
</p>
<p>
    To enable Wattman, open up MSI Aferburner and ensure that the GPU listed is the RX 560X.
    The default clock speeds of the GPU and the vRAM should be visible.
</p>

![afterburner-home-screen](/assets/img/RX560X_OC/Afterburner_home.jpg "Fig 1. MSI Afterburner")

<p>
    Head into <span style="font-style: italic;font-weight: bold">Settings</span>. Again ensure that the GPU
    selected is the RX560X<br>
    Under the <span style="font-style: italic;font-weight: bold">General</span> tab, enable the following options:
</p>

<ol>
    <li>
        Unlock Voltage Control
    </li>
    <li>
        Unlock Voltage Monitoring
    </li>
    <li>
        Extend official overclocking limits
    </li>
</ol>

![afterburner-settings](/assets/img/RX560X_OC/Afterburner_settings.jpg "Fig 2. Ensure that the highlighted checkboxes are selected")

<p>
    Click <span style="font-style: italic;font-weight: bold">Apply</span> to apply the changes.
    <br>
    When prompted to reboot, click <span style="font-style: italic;font-weight: bold">Yes</span>. You will
    now be rebooted. Ensure that any important data on the system is saved before rebooting.
</p>

<h3>Step 2: Verify if Wattman is enabled</h3>
<p>
    Open
    <span style="font-style: italic;font-weight: bold">AMD Radeon Settings</span>
    , and head into the
    <span style="font-style: italic;font-weight: bold">Gaming</span> tab.
</p>

![radeon-settings](/assets/img/RX560X_OC/Radeon_settings.jpg "Fig 3. Radeon Settings home")

<p>
    Click on <span style="font-style: italic;font-weight: bold">Global Settings</span>
</p>

![global-settings](/assets/img/RX560X_OC/Game_tab.jpg "Fig 4. Click on Global Settings")

<p>
    If all went right, the
    <span style="font-style: italic;font-weight: bold">Global Wattman</span>
    tab should be visible.
</p>

![global-settings](/assets/img/RX560X_OC/Global_settings.jpg "Fig 5. Click on the Global Wattman tab")

<p>
    If the tab is not visible, go back to Afterburner, disable all the checkboxes,
    reboot, and repeat Step 1.
</p>

<p>
    Click on the <span style="font-style: italic;font-weight: bold">Global Wattman</span> tab.
    If any warning about the warranty/risks pops up, click 'Yes' to accept it.<br>
    You should now get a screen resembling the one below.
</p>

![wattman](/assets/img/RX560X_OC/wattman.jpg "Fig 6. Hooray, Wattman has been enabled !")

<h3>Step 3: Get the baseline score</h3>

<p>
    The baseline score is used to determine the default performance of the GPU. It is
    the advertised score of the manufacturer.
    <br>
    To get the baseline, open 3DMark and run the Firestrike benchmark. It is advisable to
    close all other GPU-utilizing applications prior to running the benchmark.
    <br>
    After a few minutes of painful waiting, the benchmark result will appear. Note down
    the values.
</p>

![baseline](/assets/img/RX560X_OC/baseline.jpg "Fig 7. Baseline Result - 3DMark FireStrike")


<p>
    Note that your results may be slightly different from the one above, based on various
    factors such as the CPU, RAM, motherboard, and so on.<br>
    We are now ready to begin tweaking the card.
</p>

<h3>Step 4: Basic tweak - Memory Timings</h3>

<p>
    Just like <a href="https://en.wikipedia.org/wiki/Memory_timings">RAM timings</a>
    affect the time taken by the CPU to access data in the RAM, GPU vRAM timings affect
    the time taken by the GPU to access data in the vRAM. The lower the strobing latency,
    the faster is the data access rate.<br>
    AMD has included 2 timing levels - level 1 and 2, where level 1 has higher latency
    and consumes lesser power, while level 2 has lower latency but consumes higher power.
    By default, the timing levels are in level 1 most of the time, to minimize the power draw.
</p>
<p>
    In Wattman, scroll down to the Memory section. Click on
    <span style="font-style: italic;font-weight: bold">Memory Timings</span> and select
    <span style="font-style: italic;font-weight: bold">Memory Timing Level 2</span>
</p>

![memory_mods.jpg](/assets/img/RX560X_OC/memory_mods.jpg "Fig 8. Memory Tweaks")

<p>
    Hit <span style="font-style: italic;font-weight: bold">Apply</span>
    to apply the changes.<br>
    Re-run Firestrike and verify that a small improvemtn in performance has been obtained.
</p>

<h3>Step 5: Basic tweak - Memory clock speeds and voltages</h3>

<p>
    We'll now fix the clock speed and voltages of the vRAM. While it is possible to
    play around with these values as well, the performance gain isn't that great. Note that the
    <a href="#procedure">general tweaking heuristic</a> remains the same, so it is simply a matter
    of applying them to the vRAM clocks/voltages instead of the GPU clocks/voltages.
</p>
<p>
    Head back to the Memory section in Wattman. Enable the frequency slider and the voltage control.
    <br>
    Slide the frequency slider till <span style="font-weight: bold">1550Mhz Max</span>.
    <br>
    Set the 3rd value under <span style="font-style: italic;font-weight: bold">Voltage Control(mV)</span>
    to <span style="font-weight: bold">850</span>.
    <br>
    Check that it looks like teh above figure.
</p>
<p>
    Again, re-run Firestrike and verify that performance is slightly increased.
</p>
<p></p>

<div style="background-color: #8a5bd0;
    font-family: sans-serif;
    font-size: 1.2rem;
    padding: 20px;
    border-style: double;
    border-width: medium;
    border-color: white;">
    <span style="font-weight: bold; font-size: 1.3rem;">Saving and Restoring Profiles</span>
    <p>
    While messing around with the clocks and voltages, it is a good idea
    to frequently save the current configurations by clicking
    <span style="font-style: italic;font-weight: bold">Save Profile</span>. This exports
    the current settings in an XML file.
    <br>
    After encountering a crash/ degradation in performance, simply click
    <span style="font-style: italic;font-weight: bold">Load Profile</span>, and select
    the saved XML file. This will restore the settings to the saved values, and saves
    a lot of time since Wattman gets reset after every crash.
    </p>
</div>

<p></p>
<h3>Step 6: Advanced Tweaking - GPU Clocks and Voltages</h3>

<p>
    The idea here is to first increase the clock at a constant voltage, then decrease the
    voltage at constant clocks till the system becomes unstables/ crashes.
    <br>
    <span style="font-weight: bold">Unstable state:</span> This occurs when the voltage supplied
    is insufficient to drive the clock at the specified rate. As a result we may have a lot
    of pixelation and stuttering in the benchmark, or the GPU drivers crash, often causing
    the entire system to crash.
    <br>
    Let us now look at the general procedures for overclocking and undervolting.
</p>

![oc_and_uv](/assets/img/RX560X_OC/oc_and_uv.jpg "Fig 9. GPU overclocking and undervolting")

<p>
    First, scroll up to the GPU section and set
    <span style="font-style: italic;font-weight: bold">Frequency(%)</span> option.
    This allows Wattman to smooth the Frequency-Voltage curve for a given maximum
    frequency.
    <br>
    Also set the <span style="font-style: italic;font-weight: bold">Voltage Control</span>
    mode to manual, to enable changing the voltages.
    <br>
    We are now ready to begin tweaking !
</p>

<div id="procedure" style="border: 5px black solid; background-color: #5ed9e6">
    <h3 class="text-center" id="overclocking">Overclocking</h3>
    <ol style="font-size: larger">
        <li>
            Drag the orange Frequency slider(Fig. 9) till the Frequency becomes 1%.
        </li>
        <li>
            Hit Apply to apply the changes.
        </li>
        <li>
            Run FireStrike and record the score.
        </li>
        <li>
            If there is an improvement in performance, save the profile and go back to
            <span style="font-weight: bold"> Step 1</span>.
        </li>
        <li>
            In case of instability, load the most recent saved XML, and move to the
            <a href="#undervolting">undervolting</a> procedure.
        </li>
    </ol>
</div>
<p></p>
<p>
    After reaching a maximum clock value, start undervolting to minimize the power consumption
    and the heat generated. Further, undervolting often reduces the thermal limitation, and
    <span style="font-weight: bold">higher clocks can be achieved with slightly lower voltages</span>.
</p>

<div style="border: 5px black solid; background-color: #5ed9e6">
    <h3 class="text-center" id="undervolting">Undervolting</h3>
    <ol style="font-size: larger">
        <li>
            While the Maximum voltage is above 1000mV:<br>
            a. Reduce the maximum voltage by 10mV and Apply <br>
            b. Run Firestrike and ensure that it doesn't become unstable. In case of
            instability go to <span style="font-weight: bold">Step 9</span>.
        </li>
        <li>
            Save the profile
        </li>
        <li>
            Reduce the maximum voltage by 5mV.
        </li>
        <li>
            If it becomes less than the second highest voltage, set it equal.
        </li>
        <li>
            Run firestrike, and measure the score. In case of
            instability go to <span style="font-weight: bold">Step 9</span>.
        </li>
        <li>
            If Max Voltage > 2<sup>nd</sup> Highest voltage go to
            <span style="font-weight: bold">Step 2</span>
        </li>
        <li>
            Start reducing the 2<sup>nd</sup> Highest voltage by 10mV, maintaining
            atleast 50mV between it and the level below it.
        </li>
        <li>
            Again, run Firestrike and record the score. In case of
            instability go to <span style="font-weight: bold">Step 9</span>.
        </li>
        <li>
            In case of instability, there are 2 options:<br>
            a. Reduce the clock-speed in steps of 0.5% till it becomes stable.<br>
            b. Load the previous stable configuration, and fix it as the final one<br>
        </li>
    </ol>
</div>

<p></p>
<p>
    A (quite understandable) question would be
    <span style="font-style: italic;font-weight: bold">"How to know when to stop?"</span>.
    The answer for that is two pronged.
    <br>
    First, there is no configuration guaranteed to yield the maximum performance. The above
    procedures for overclocking and undervolting are more like
    <span style="font-weight: bold">heuristics</span>, i.e., they maximize the chances of getting
    a performance improvement. However, it is always advisable to play around a little - using smaller
    voltage steps for example. More on this in the <a href="#whyitworks">Why does it work ?</a> section.
    <br>
    Second, of course, is one's satisfaction with the current performance. The above process
    is very time consuming, and once a desirable performance level (improvement in Firestrike score)
    has been achieved, it becomes pretty pointless to continue and waste more time.
</p>

<h3>Results</h3>

<p>
    Great work! The GPU has now been overclocked and undervolted. Play some games to get a first-hand experience
    of the performance boost.
</p>

<p>
    My final configuration can be seen in Fig. 9.<br>
    The Frequency has been increased by <span style="font-weight: bold">6%</span>.
    <br>
    The maximum voltages are set to <span style="font-weight: bold">981mV</span>
    and <span style="font-weight: bold">987mV</span>.
</p>

![results](/assets/img/RX560X_OC/results.jpg "Fig 10. Original vs. final benchmark scores")

<p>
    From the figure, there is a net increase of 7% in the performance !! The next section deals
    with the analysis of the results, and is strictly an optional read.
</p>

<h3>Analysis</h3>

<p>
    The Firestrike benchmark consists of 3 tests - Graphics, Physics, and Combined, each of
    which test different parts of the GPU. As can be seen in Fig. 10, the change in each
    is very different from the other. Let us look each one in greater detail.
</p>

<h4>I. Graphics Test</h4>
<p>
    The graphics test focuses on geometry, illumination, and pixel processing in the GPU.
    It makes use of both the compute and graphics shaders in order to distribute the work
    uniformly across the compute units. Here we have almost 5.5% improvement, which is
    along expected lines, since clock speeds have been increased by 6%.
</p>

<h4>II. Physics Test</h4>
<p>
    A massive 22% improvement is observed in the Physics scores. The Physics Test mainly loads
    the CPU with multi-threaded simulation workloads, while keeping the GPU load to a minimum.
    The sharp increase in performance indicates the presence of a
    <a href="https://www.wepc.com/tips/cpu-gpu-bottleneck/">GPU bottleneck</a>. Overclocking the
    GPU helps relieve this bottleneck, and allows more performance to be extracted from the CPU.
</p>

<h4>III. Combined Test</h4>
<p>
    The combined test loads both the CPU and the GPU – the CPU for running 32 parallel simulations
    and the GPU for various graphics operations present in the Graphics Test.
    An improvement of 4.4% is observed, which is slightly less than the 6% clock-speed increase.
    <br><br>
    Possible reasons for this include:<br>
    1. Overheads incurred in transferring the simulation input from the GPU to CPU, and output from
    CPU to the GPU.
    <br>
    2. The CPU has not been overclocked. So its contribution remains more or less the same.
</p>

<h3 id="whyitworks">But why does this work ?</h3>

<p>
    But if we can increase the performance with such "minimal" effort, then why doesn't the manufacturer
    keep the cards at these configurations beforehand? Are we just victims of a very poor practical joke?
</p>
<p>
    Fortunately, neither are manufacturers sadistic, nor do they have any sense of humour (so to speak).
    There is indeed a valid reason for the retail configuration being the way it is. To understand this,
    we must first look at how GPUs are manufactured.
</p>
<p>
    A GPU is nothing more than a piece of Silicon. And these pieces are manufactured in huge quantities
    (100s of thousands at a time). Each batch of GPUs is then rigorously tested for stability. It is
    important to note that each GPU is unique, i.e., the Silicon configuration on it is slightly different
    from that on other samples. Hence, each GPU potentially gives best performance at a different configuration.
    Naturally, the sheer amount of work needed to find the suitable configurations for each and every GPU
    makes this highly infeasible.
    <br>
    Further, a very small fraction of samples may fail at clocks higher than a certain
    <span style="font-weight: bold">threshold clock</span>, or at voltages lower than a
    <span style="font-weight: bold">threshold voltage</span>. Note that this is a "small" fraction,
    not a "negligible" fraction. Discarding these samples will lead to huge losses which is unacceptable.
</p>

<p>
    Hence we have all the samples configured with the threshold clock and threshold voltages. Since this
    configuration is only required for a very samll fraction of GPUs, there are very high chances that
    a random GPU has headroom for tweaking.
</p>

<h3>Conclusion</h3>

<p>
    Et voila!! You have successfully tweaked your GPU, and have got an idea of the general procedure to
    be followed. Note that I have skipped power tweaking completely in order to minimize the risk of damage
    to the laptop, but this is an important metric to be considered when working in less constrained setups,
    such as Desktop systems.
</p>
<p>
    Once you get a decent idea of how this works, you can start estimating values in much bigger steps than
    in the heuristics, and save a lot of time. Also remember that your results may be similar to someone
    else, but the chances of them being the same are remote (because of reasons discussed in the previous
    section).
</p>

<h3>References</h3>
<ul>
    <li>
        <a href="https://www.gamersnexus.net/guides/2501-amd-rx-480-overclocking-guide-and-walkthrough">
            <span style="font-weight: bold; ">Steve Burke's guide to overclocking the RX 480</span>
        </a>
    </li>
    <li>
        <a href="https://www.amd.com/en/support/kb/faq/dh-020">
            <span style="font-weight: bold; ">AMD's Wattman guide</span>
        </a>
    </li>
    <li>
        <a href="https://www.crucial.com/usa/en/memory-performance-speed-latency">
            <span style="font-weight: bold; ">Metrics for memory performance, by Crucial</span>
        </a>
    </li>
    <li>
        <a href="https://www.maketecheasier.com/what-is-ram-timing/">
            <span style="font-weight: bold; ">An easy explanation of RAM timings</span>
        </a>
    </li>
    <li>
        <a href="http://akamai-dl.futuremark.com.akamaized.net/3dmark-technical-guide.pdf">
            <span style="font-weight: bold; ">3D Mark Technical guide</span>
        </a>
    </li>
</ul>