# Spectre Attack Example
Example of using revealed "Spectre" exploit from 2 Jan 2018 (CVE-2017-5753 and CVE-2017-5715)

Spectre breaks the isolation between different applications. It allows an attacker to trick error-free programs, which follow best practices, into leaking their secrets. In fact, the safety checks of said best practices actually increase the attack surface and may make applications more susceptible to Spectre.

* [Spectre paper] - Spectre paper and source of code

## Language
C, uses libs:
* stdio.h
* stdlib.h
* stdint.h
* intrin.h - for rdtscp and clflush

## What's example do?
We're putting text "The Magic Words are Squeamish Ossifrage." in memory and then we're trying to read it using exploit. If system is vulnerable, you'll see same text in output, readed from memory.
***
In this code, if the compiled instructions in `victim_function()` were executed in strict program order, the function would only read from `array1[0..15]` since array1 size = 16. 
However, when executed speculatively, out-of-bounds reads are possible. The read memory `byte()` function makes several training calls to `victim_function()` to make the branch predictor expect valid values for x, then calls with an out-of-bounds x. The conditional branch mispredicts, and the ensuing speculative execution reads a secret byte using the out-of-bounds x. The speculative code then reads from `array2[array1[x] * 512]`, leaking the value of `array1[x]` into the cache state. To complete the attack, a simple flush+probe is used to identify which cache line in `array2` was loaded, reveaing the memory contents. The attack is repeated several times, so even if the target byte was initially uncached, the first iteration will bring it into the cache. 
The unoptimized code reads approximately 10KB/second on an i7 Surface Pro 3.

## Sources
* [Spectre exploits info]
* [CVE-2017-5753] - Variant 1: bounds check bypass
* [CVE-2017-5715] - Variant 2: branch target injection

[Spectre paper]: <https://spectreattack.com/spectre.pdf>
[Spectre exploits info]: <https://spectreattack.com>
[CVE-2017-5753]: <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5753>
[CVE-2017-5715]: <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715>
