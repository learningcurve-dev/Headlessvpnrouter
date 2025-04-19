#!/data/data/com.termux/files/usr/bin/bash
# Stealth Autopilot Setup - LearningCurve Edition

# Set working path
export BASE_DIR=$HOME/learningcurve-dev
mkdir -p $BASE_DIR && cd $BASE_DIR

echo "[*] Updating and installing core packages..."
pkg update -y && pkg upgrade -y
pkg install -y git curl wget proot pulseaudio tsu
pkg install -y golang make clang openssh unzip zip termux-api

echo "[*] Cloning and compiling tun2socks..."
mkdir -p go && export GOPATH=$BASE_DIR/go
git clone https://github.com/eycorsican/go-tun2socks.git $GOPATH/src/go-tun2socks
cd $GOPATH/src/go-tun2socks
go build

echo "[*] Creating tunnel scripts and watchdogs..."

# Startup script
mkdir -p $HOME/.termux/boot
cat << 'EOF' > $HOME/.termux/boot/startup.sh
#!/data/data/com.termux/files/usr/bin/bash
cd $HOME/learningcurve-dev/go/src/go-tun2socks
./go-tun2socks &
bash $HOME/learningcurve-dev/watchdog.sh &
EOF
chmod +x $HOME/.termux/boot/startup.sh

# Watchdog script
cat << 'EOF' > $BASE_DIR/watchdog.sh
#!/data/data/com.termux/files/usr/bin/bash

while true; do
    if ! pgrep -f go-tun2socks > /dev/null; then
        echo "[!] tun2socks not running. Restarting..."
        cd $HOME/learningcurve-dev/go/src/go-tun2socks
        ./go-tun2socks &
    fi

    # Speed test and IP check every 5 minutes
    curl -s https://ipinfo.io/ip > $HOME/.current_ip
    speed=$(ping -c 3 1.1.1.1 | grep 'avg' | cut -d '/' -f 5)
    echo "IP: $(cat $HOME/.current_ip), Ping: ${speed}ms"
    
    sleep 300
done
EOF
chmod +x $BASE_DIR/watchdog.sh

# Self-check fixer
cat << 'EOF' > $BASE_DIR/selfheal.sh
#!/data/data/com.termux/files/usr/bin/bash
echo "[*] Running self-diagnosis..."
pkg install -y git curl wget proot pulseaudio tsu golang make clang openssh unzip zip termux-api
[ ! -d "$GOPATH/src/go-tun2socks" ] && git clone https://github.com/eycorsican/go-tun2socks.git $GOPATH/src/go-tun2socks
cd $GOPATH/src/go-tun2socks && go build
EOF
chmod +x $BASE_DIR/selfheal.sh

# Endpoint sync tool (dummy logic for now)
cat << 'EOF' > $BASE_DIR/sync_endpoints.sh
#!/data/data/com.termux/files/usr/bin/bash
echo "[*] Syncing endpoints..."
# Placeholder - implement your real logic or rotate list here
echo "endpoint1.vpn.net" > endpoints.txt
EOF
chmod +x $BASE_DIR/sync_endpoints.sh

echo "[*] Making all scripts executable..."
chmod +x $BASE_DIR/*.sh

echo "[*] Setup complete. Run 'termux-reboot' to test autorun."