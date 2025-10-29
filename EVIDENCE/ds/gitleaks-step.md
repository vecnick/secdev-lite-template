Warning: Unexpected input(s) 'args', valid inputs are ['']
Run gitleaks/gitleaks-action@v2
[vecnick] is an individual user. No license key is required.
gitleaks version: 8.24.3
Version to install: 8.24.3 (target directory: /tmp/gitleaks-8.24.3)
Cache hit for: gitleaks-cache-8.24.3-linux-x64
Received 5717454 of 5717454 (100.0%), 6.3 MBs/sec
Cache Size: ~5 MB (5717454 B)
/usr/bin/tar -xf /home/runner/work/_temp/9698943d-10d8-4063-9b18-f9742615c586/cache.tzst -P -C /home/runner/work/hh/hh --use-compress-program unzstd
Cache restored successfully
Gitleaks restored from cache
event type: push
gitleaks cmd: gitleaks detect --redact -v --exit-code=2 --report-format=sarif --report-path=results.sarif --log-level=debug --log-opts=-1
/tmp/gitleaks-8.24.3/gitleaks detect --redact -v --exit-code=2 --report-format=sarif --report-path=results.sarif --log-level=debug --log-opts=-1

    ○
    │╲
    │ ○
    ○ ░
    ░    gitleaks

11:08PM DBG using github.com/wasilibs/go-re2 regex engine
11:08PM DBG no gitleaks config found in path .gitleaks.toml, using default gitleaks config
11:08PM DBG executing: /usr/bin/git -C . log -p -U0 -1
11:08PM DBG SCM platform parsed from host host=github.com platform=github
11:08PM INF 1 commits scanned.
11:08PM DBG Note: this number might be smaller than expected due to commits with no additions
11:08PM INF scanned ~140 bytes (140 bytes) in 147ms
11:08PM INF no leaks found
Artifact name is valid!
Root directory input is valid!
Beginning upload of artifact content to blob storage
Uploaded bytes 6740
Finished uploading artifact content to blob storage!
SHA256 digest of uploaded artifact zip is 5270be0dcb0c8afbd3c064dfaa6ed3b89bc201696df0515096b7b2860075ed34
Finalizing artifact upload
Artifact gitleaks-results.sarif.zip successfully finalized. Artifact ID 4399945424
✅ No leaks detected