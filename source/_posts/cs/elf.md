---
title: ELF
date: 2022-11-03 13:39:15
tags:
---

# 常用指令 #

| Action              | readelf         | objdump             | nm | hexdump 
|---------------------|:----------------|:--------------------|:---|:-------
| Show Dyn. Sec.      | -d              | -p                  |    |
| Show Sec. Header    | -S              | -h                  |    |
| Show Prog. Header   |                 |                     |    |
| Hex Dump of Sec.    |                 | -s [-j SEC]         |    | -C -s $((0xSTART)) -n $((0xSIZE))
| Disassembly of Sec. |                 | -d                  |    |
| Show All Header     | -a / --all      | -x                  |    |
| Show Dyn. Symbol    | -s / --dyn-syms | -T / --dynamic-syms | -D |
| Show Symbol Table   | -s / --syms     | -t / --syms         | -a |
| Show Reloc Table    | -r              | -r                  |    |


| Action              | Cmd
|---------------------|:----------------
| Show Sec. Size      | size [elf]
