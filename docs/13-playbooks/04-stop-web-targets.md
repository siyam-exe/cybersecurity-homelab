# Playbook: Stop Web Targets

Stop vulnerable web targets after testing.

## Commands

```bash
docker stop caddy-juice juice-shop dvwa
docker ps --filter name='juice-shop' --filter name='dvwa' --filter name='caddy-juice'
```

Expected:

- no running vulnerable web target containers
- ports `3002` and `8080` are not reachable
