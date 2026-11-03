# Fix the adopt check of root on neve

`adopt-fix-root-t1` · Task · bug

The adopt check of root failed on this machine: tier0.

Diagnose the failure first. A code failure you fix and land as usual. A machine failure, such as a missing tool, an old version or timing like a security agent holding new binaries, has nothing to commit.

Stop condition: the failure is a machine failure. Write a stop report naming the exact steps the operator must take on this machine and, as its way forward, a re-run of lym adopt root once the machine is settled, never lym loom resume.
