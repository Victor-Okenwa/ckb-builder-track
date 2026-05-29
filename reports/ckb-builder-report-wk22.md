# Builder Track Weekly Report — Week 22

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 29th May, 2026


## Building on CKB with Go Lang


### Installing Go lang for WSL (Ubuntu distro) on windows 11

This week I went into learning how to build `smart contracts` with Go on CKB. GO is the best option for smart contract development on CKB but it was the only language among `Rust` and `Java` that I was a bit familiar with. I had it in mind to that learning Rust is a must because it is the _Go to Language_ of the ckb ecosystem and it is best for `smart contracts` because it doesn't have a garbaege collector like `Go Lang`.

I had several challenges installing `Go lang` on WSL, from _wrong links_ and _wrong commands_ to network connectivity. 

I followed the steps below to install `GO` on WSL seemlessly

1. Go to the official Go language downloads page: [https://go.dev/dl/](https://go.dev/dl/)

2. Look for the Linux distribution and copy the link of the stable version. it would look something like this `https://go.dev/dl/go1.26.3.linux-amd64.tar.gz`.

3. Open the `Ubuntu Shell` and run any of this command. _Choose one of the options._

__ Using `wget`__

```bash
    wget https://go.dev/dl/go1.26.3.linux-amd64.tar.gz
```

__Using `curl`__

```bash
curl -LO https://go.dev/dl/go1.26.3.linux-amd64.tar.gz
```

__If both of the options above failed you can install directly from ubuntu__

```bash
sudo apt update
sudo apt install golang-go -y
```

4. Unpack and install go if you use `wget` or `curl` options above. _Make sure you are in the root of the WSL if not use `cd ~` to go back to the root_

```bash
sudo tar -C /usr/local -xzf go1.26.3.linux-amd64.tar.gz
```

5. Add `GO` to your path.
- Run `nano ~/.bashrc`.
- Scroll at the bottom and paste in this code `export PATH=$PATH:/usr/local/go/bin`.
- Press `CTRL` + `O` to save ond close.

6. Reload your terminal using `source ~/.bashrc`

7. Check if God exists using `go version` or `which go` for path.




