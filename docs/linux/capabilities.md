# [Linux](./README.md) > Setuid Capabilities

## Vulnerability

TODO

## Prerequisites

- Low privileged access to the Linux target.

## Exploit

First, search for binaries that have *setuid* capabilities enabled with *+ep* flag (effective and permitted).

```bash
getcap -r / 2>/dev/null | grep setuid
```

Once you find a suitable binary, you can either try to exploit it by your way, or check on [GTFOBins](https://gtfobins.github.io/).

## Recommendations

TODO