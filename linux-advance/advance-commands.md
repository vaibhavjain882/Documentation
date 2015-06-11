#### 1. Echo and Append something from local to remote linux machines:
```
for i in {125..128}; do ssh -t saurabhs@192.168.22.$i "echo '192.168.21.11    IRONMQ.bookmyshow.org' | sudo tee -a /etc/hosts";done

Note: redirection >> doesn't work with echo in this case and it requires tty shell also so avoid to use >>.
```

