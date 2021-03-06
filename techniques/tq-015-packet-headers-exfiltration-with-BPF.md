# tq-015 Packet headers exfiltration with BPF

If UDP or TCP injection can be reliably blocked with a BPF and if the header values
are static it’s possible to exfiltrate the values through a series of tests
bisecting binary values for those headers in BPF<sup>[1](#fn1)</sup> filters.
IPv4 has a 8-bit TTL<sup>[2](#fn2)</sup> and a 16-bit Fragment ID, IPv6 has a Hop
Limit<sup>[2](#fn2)</sup> (modern pseudonym of TTL).

TTL exfiltration tricks are not needed for UDP as it has `IP_RECVTTL`.

TBD: is IPv6 flow label a useful value? Any IPv6 EHs? What TCP packet bits are useful? Window size? Presence of ACK in RST? TCP Options?

TBD: is it possible to exfiltrate the presence of a RST packet _ignored_ by the TCP stack?

<a name="fn1">1</a>: unfortunately, eBPF that is [so useful for metadata exfiltration](https://blog.cloudflare.com/epbf_sockets_hop_distance/) is [whitelisted only for the Android System, not for applications](https://github.com/aosp-mirror/platform_bionic/blob/android-9.0.0_r12/libc/SECCOMP_WHITELIST_SYSTEM.TXT)

<a name="fn2">2</a>: TTL in an incoming packet is different from the hop distance gathered via a traceroute. Also, “static” TTL may vary a bit due to possible multi-path routes from injecting server.

## Examples
- AS6697, Beltelecom, [Belarus blocks tor](https://ooni.torproject.org/post/belarus-fries-onion/) injecting TCP RST
- Egypt had equipment injecting TCP RST with [static IP Fragment IDs](https://ooni.torproject.org/post/egypt-network-interference/#attempts-to-block-tor)
