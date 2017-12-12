---
layout:     post
title:      GDB Instruction
date: 2016-12-20 14:22:00 +0800
keywords:   
categories:
	- computer science
tags:		
	- GDB
---


```
#list all
(gdb)l

#display next line
(gdb)n

#enter for redo
(gdb)

#set breakpoint
(gdb)break [line_number]

#enable/disable breakpoint
(gdb)enable/disable [breakpoint_number]

#delete all breakpoints
(gdb)d
#delete breakpoint
(gdb)d [breakpoint_number]/[breakpoint_number_range]
e.g.
(gdb)d 1/1-2/1 2

#run
(gdb)r

#continue
(gdb)c

#check breakpoints info
(gdb)info break

#check function stack - backtrack
(gdb)bt
```