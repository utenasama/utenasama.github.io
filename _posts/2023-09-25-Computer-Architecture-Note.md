---
layout: post
title: Computer architecture note
date: 2023-09-25 11:10 +0800
last_modified_at: 2023-09-26 23:18:25 +0800
tags: [computer science, note]
toc:  true
---


## Fundamentals of Computer Design - Basis

#### RISC Architecture
- Reduced Instruction Set Computer
- Feature simpler instructions
- Present two critical perf techniques: 
    - pipelining
    - multiple instruction issue(more than one instruction per cycle)

#### Processor Perf Growth
- Dennard Scaling
  - Power density is constant for a given area of silicon even as you increase the number of transistors because of smaller dimensions of each transistor
  - Ended around 2004 because current and voltage couldn’t keep dropping and still maintain the dependability of integrated circuits
 
- Moore’s Law
  - The nubmer of transistors per chip would double every year, as predicted in 1965, which was amended in 1975 to every two years
  - Ended around 2015


#### Multi-Core Processor 
- Amdahl’s Law
  - Multi-core parrallelism requires the restructuring of the application, a major new burden for programmers
  - speedup effect:

$$  
Speedup_{overall} = \frac{1}{(1 - Fraction_{enhanced}) + \frac{Fraction_{enhanced}}{Sppedup_{enhanced}}} 
$$ 

In this formula, “$Speedup_{overall}$” represents the overall speedup achieved, “$Fraction_{enhanced}$” represents the fraction of the task that can be enhanced or parallelized, and “$Speedup_{enhanced}$” represents the speedup achieved by the enhanced or parallelizable portion of the task. We can get the speedup limit:

$$
Speedup limit = \lim_{Speedup_{enhanced} \to \infty} Speedup_{overall} = \frac{1}{1 - Fraction_{enhanced}}
$$
