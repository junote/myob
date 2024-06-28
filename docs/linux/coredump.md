
To enable core dumps, first check the maximum core dump size:

$ ulimit -c
If the result of this is zero (i.e. no core dump will be produced), set the limit to the maximum:

$ ulimit -c unlimited
A core dump will now be generated and placed in the location specified by /proc/sys/kernel/core_pattern. Check this location by running:

$ cat /proc/sys/kernel/core_pattern
On CSE systems (and many other systems), the default settings result in the output:

core
This means that any core dumps will be placed in the current directory in a file named core.

You can change this location using:

$ echo "<desired-file-path>/<desired-file-name>" > /proc/sys/kernel/core_pattern


Start a GDB session with the program binary and coredump file

$ gdb <binary-file> <core-dump-file>


ulimit -c unlimited
echo "/root/coredump" > /proc/sys/kernel/core_pattern
 
 