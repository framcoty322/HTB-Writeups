![](<attachments/Pasted image 20260109083534.png>)

We can see port **5000** open running an `http` service, so we’ll fuzz it with `wfuzz`.

```bash
wfuzz -c --hc=404 -t 200 -w <path-wordlist> http://<ip>/FUZZ
```

W
