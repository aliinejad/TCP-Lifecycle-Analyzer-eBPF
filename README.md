# TCP-Lifecycle-Analyzer

A **bpftrace** script that traces TCP state transitions in real time by hooking into `tcp_set_state`.

It logs:

- Process name
- Source → Destination (IP:Port)
- TCP state
- Time since `SYN_SENT`
- Final summary on `CLOSE`:
  - Handshake duration
  - Total connection lifetime
  - Establishment result (`OK` / `NO_EST`)

## Requirements

- Linux with eBPF support
- `bpftrace`
- Root privileges

## Run

```bash
sudo bpftrace tcp_lifecycle_analyzer.bt
```

## Example Output

```text
=============================================================================================================
 COMMAND       |        SRC            ->          DST          | STATE        | SINCE_SYN(ms)
=============================================================================================================
curl           | 10.0.0.5:45322  ->  142.250.74.14:443 | SYN_SENT     |          0
curl           | 10.0.0.5:45322  ->  142.250.74.14:443 | ESTABLISHED  |         23
curl           | 10.0.0.5:45322  ->  142.250.74.14:443 | CLOSE        |        145
--------------------------------------------------------------------------------
RESULT | hs=23 ms | life=145 ms | OK
--------------------------------------------------------------------------------
```

## Notes

- IPv4 only
- Uses `struct sock *` as connection key
- Useful for debugging handshake latency and short-lived connections