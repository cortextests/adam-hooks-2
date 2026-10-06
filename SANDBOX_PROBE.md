# Sandbox probe

Anything marked `allowed` where the expectation says `denied` is a bug.

| Probe | Expected | Result |
|---|---|---|
| cwd | / (the project) | `allowed: '/'` |
| list / | project files only | `allowed: ['.cortex-hook-68581cde-98b5-41f4-a0f7-86749e3125e6.py', 'README.md', 'docs', 'src']` |
| list .. | same as / | `denied: PermissionError: [Errno 63] Operation not permitted: '..'` |
| list ../../.. | same as / | `denied: PermissionError: [Errno 63] Operation not permitted: '../../..'` |
| list /usr/local/lib | interpreter libraries only | `allowed: ['python-stdlib.zip', 'python.wasm', 'site-packages.zip']` |
| read /etc/passwd | denied | `denied: FileNotFoundError: [Errno 44] No such file or directory: '/etc/passwd'` |
| read ../../../../etc/passwd | denied | `denied: PermissionError: [Errno 63] Operation not permitted: '../../../../etc/passwd'` |
| read /proc/self/environ | denied | `denied: FileNotFoundError: [Errno 44] No such file or directory: '/proc/self/environ'` |
| list /tmp | denied | `denied: FileNotFoundError: [Errno 44] No such file or directory: '/tmp'` |
| write ../escape.txt | lands inside the project or denied | `denied: PermissionError: [Errno 63] Operation not permitted: '../escape.txt'` |
| write /usr/local/lib/probe.txt | denied (shared across runs) | `denied: OSError: [Errno 29] I/O error: '/usr/local/lib/probe.txt'` |
| environment variable names | only PYTHONPATH and PYTHONHOME | `allowed: ['PYTHONHOME', 'PYTHONPATH']` |
| os.system | denied | `denied: AttributeError: module 'os' has no attribute 'system'` |
| subprocess | denied | `denied: OSError: [Errno 58] wasi does not support processes.` |
| os.fork exists | False | `allowed: False` |
| socket connect | denied | `denied: AttributeError: module '_socket' has no attribute 'getaddrinfo'` |
| ctypes | denied | `denied: ModuleNotFoundError: No module named '_ctypes'` |
| absolute symlink to /etc/passwd | denied | `denied: PermissionError: [Errno 2] Permission denied: '/etc/passwd' -> 'probe_link'` |
