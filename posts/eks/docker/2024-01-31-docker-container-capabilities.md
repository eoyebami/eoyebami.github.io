<h1>Capabilities</h1>
* Containers in a Host machine are split across "namespaces" that grant the container is own set of resources and though we have this separation, there isn't complete isolation between the containers and the host machine
  - Unlike a vm, which exists in complete isolation and cannot influence one another
* In order to safe guard itself and other containers, the host machine restricts the containers privileges to itself.
* A container `runAsRoot` will not be able to perform the same level of tasks that the root user on the host machine can, because of these restrictions
  - However it is possible to give a container more permissions
* Here is a list of capabilites that all containers have by default
```
CHOWN: Make arbitrary changes to file UIDs and GIDs.
DAC_OVERRIDE: Bypass file read, write, and execute permission checks.
FSETID: Don’t clear set-user-ID and set-group-ID bits when a file is modified.
FOWNER: Bypass permission checks for operations that normally require the file system UID of the process to match the UID of the file.
SETGID: Make arbitrary manipulations of process GIDs and supplementary GID list.
SETUID: Make arbitrary manipulations of process UIDs.
NET_BIND_SERVICE: Bind a socket to Internet domain privileged ports (port numbers less than 1024).
KILL: Bypass permission checks for sending signals.
SYS_CHROOT: Use chroot().
```

* Here is a list of possible capabilities that can be added or removed from a container; including those that are granted to containers by default
```
AUDIT_CONTROL: Enable and disable kernel auditing.
AUDIT_WRITE: Write records to kernel auditing log.
CHOWN: Make arbitrary changes to file UIDs and GIDs.
DAC_OVERRIDE: Bypass file read, write, and execute permission checks.
DAC_READ_SEARCH: Bypass file read permission checks and directory read and execute permission checks.
FOWNER: Bypass permission checks for operations that normally require the file system UID of the process to match the UID of the file.
FSETID: Don’t clear set-user-ID and set-group-ID bits when a file is modified.
KILL: Bypass permission checks for sending signals.
SETGID: Make arbitrary manipulations of process GIDs and supplementary GID list.
SETUID: Make arbitrary manipulations of process UIDs.
SETPCAP: Modify process capabilities.
LINUX_IMMUTABLE: Make files immutable.
NET_ADMIN: Allows various network-related operations
NET_BIND_SERVICE: Bind a socket to Internet domain privileged ports (port numbers less than 1024).
NET_BROADCAST: Make socket broadcasts, and listen to multicasts.
NET_RAW: Use RAW and PACKET sockets.
IPC_LOCK: Lock memory.
IPC_OWNER: Bypass permission checks for operations on System V IPC objects.
SYS_MODULE: Load and unload kernel modules.
SYS_RAWIO: Perform I/O port operations (iopl).
SYS_CHROOT: Use chroot().
SYS_PTRACE: Trace arbitrary processes using ptrace.
SYS_PACCT: Use acct().
SYS_ADMIN: Perform various system administration operations.
SYS_BOOT: Use reboot().
SYS_NICE: Raise process nice value.
SYS_RESOURCE: Override resource limits.
SYS_TIME: Set system clock.
SYS_TTY_CONFIG: Use vhangup().
MKNOD: Create special files using mknod().
LEASE: Establish leases on arbitrary files.
AUDIT_READ: Allow reading the audit log via unicast netlink socket.
SETFCAP: Set file capabilities.
MAC_OVERRIDE: Override Mandatory Access Control (MAC) checks.
MAC_ADMIN: Allow MAC configuration or state changes.
SYSLOG: Configure kernel terminal logging.
WAKE_ALARM: Trigger something that will wake up the system.
```
