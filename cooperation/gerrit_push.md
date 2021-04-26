> 推送地址

git push --receive-pack='git receive-pack --reviewer=A@XX.com --reviewer=B@XX.com' origin HEAD:refs/for/`git rev-parse --abbrev-ref HEAD` 2>&1 >/dev/null |tee /dev/stderr| grep http | cut -d' ' -f 4| tee /dev/stderr | pbcopy