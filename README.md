# Page Access Tracer #

This kernel patch allows to collect page accesses of applications by hooking into the page fault handler. Basically, a process's page table entries (PTEs) are periodically reset (every 1 ms by default) to cause a fault at the subsequent access. The events are written to the kernel tracer.

## Usage ##

* The primary interface for the tracer is `/proc/trace_page_access` in the `procfs`. You can tag, list, or reset the targets to collect traces by writing to or reading from the file.

* To register a target,
```
# echo [executable_name] > /proc/trace_page_access
```

* To list targets,
```
# cat /proc/trace_page_access
ls
hello_world
```

* To reset the target list,
```
# echo "" > /proc/trace_page_access
```

* When a process having the designated executable name is forked, the kernel starts to collect the trace automatically. The trace is accessible through `/sys/kernel/debug/tracing/trace_pipe` as follows;

```
                                                                    Instr  Accessed
  Process name                   TIME       EVENT            PID RW ptr    address
  target-app-1716  [011] ....    98.463923: init_page_access_trace: START target-app
  target-app-1716  [013] ....    98.467282: handle_mm_fault: 1716 W 40f395 7fffffffe5b8
  target-app-1716  [013] ....    98.467294: handle_mm_fault: 1716 R 40f384 7ffff7ffd000
  target-app-1716  [013] ....    98.467295: handle_mm_fault: 1716 R 40f395 7ffff7ddc8a8
  target-app-1716  [013] ....    98.467299: handle_mm_fault: 1716 R 40f350 7ffff7df5380
  target-app-1716  [013] ....    98.467301: handle_mm_fault: 1716 R 40f384 7ffff7de6890
  target-app-1716  [013] ....    98.467302: handle_mm_fault: 1716 R 40f34a 7ffff7df1300
  target-app-1716  [013] ....    98.467307: handle_mm_fault: 1716 R 40f39b 7ffff7df20b0
  target-app-1716  [013] ....    98.467316: handle_mm_fault: 1716 R 40f366 7ffff7df20bc
  ....
  kworker/6:0-2039  [006] ....  1044.618466: __reset_page_access_trace: END target-app
```

* Sang-Hoon Kim (sanghoon@vt.edu)
