# Raptoreum ($RTM) Mining on Raspberry Pi 2 Model B

## Introduction
This guide provides step-by-step instructions on how to mine Raptoreum ($RTM) using a Raspberry Pi 2 Model B. Raptoreum utilizes the **GhostRider** algorithm, which is CPU-friendly, making it feasible to mine on low-power devices like the Raspberry Pi.

---

## 1. Prepare Your Raspberry Pi 2
### Requirements
Before starting, ensure you have the following:
- Raspberry Pi 2 Model B
- MicroSD card (8GB minimum recommended)
- Power supply (5V, 2.5A recommended)
- Network connection (Ethernet preferred for stability)

### Installing Raspberry Pi OS
#### Step 1: Download and Flash Raspberry Pi OS
Flash the OS onto the microSD card using Raspberry Pi Imager:
   ```sh
   sudo apt update && sudo apt install rpi-imager -y
   rpi-imager
   ```
   - Select the "Raspberry Pi 2 Model B" device
   - Select "Raspberry Pi OS (Legacy, 32-bit) Lite" as OS
   - Choose your microSD card as storage
   - Click "Next"
   - Edit settings to enable SSH connection to connect to your Raspberry Pi remotely via SSH after booting.

#### Step 2: Boot and Connect to the Raspberry Pi
1. Insert the microSD card into your Raspberry Pi 2.
2. Connect power and Ethernet cable.
3. Find the Raspberry Pi’s IP and connect via SSH:
   ```sh
   sudo arp-scan --localnet
   ```
   ```sh
   ssh pi@<IP_ADDRESS>
   ```
   Default password: `raspberry`

#### Step 3: Update the System
Once logged in, update the system to ensure all packages are up to date:
```sh
sudo apt update && sudo apt upgrade -y
```

---

## 2. Install Necessary Dependencies
Raptoreum mining requires XMRig, which needs several dependencies to be installed before compiling.

### Install Required Packages
```sh
sudo apt install -y build-essential cmake automake libtool pkg-config libssl-dev libboost-all-dev libuv1-dev git
```

---

## 3. Download and Compile XMRig
Since Raspberry Pi 2 has an ARM CPU, we need to compile XMRig from source.

### Step 1: Clone the XMRig Repository
```sh
git clone https://github.com/xmrig/xmrig.git
cd xmrig
```

### Step 2: Build XMRig
```sh
mkdir build && cd build
cmake ..
make
```
The compilation process may take some time depending on the Raspberry Pi’s processing power.

---

## 4. Configure XMRig for Raptoreum Mining
### Step 1: Obtain Your Raptoreum Wallet Address
Ensure you have a Raptoreum wallet. If not, create one using the official [Raptoreum Wallet](https://raptoreum.com/).
You can install the Raptoreum core wallet from: https://github.com/Raptor3um/raptoreum/releases

### Step 2: Select a Mining Pool and Configure XMRig
Find a mining pool: [Mining Pool Stats](https://miningpoolstats.stream/raptoreum)  
Example: [Raptoreum Zone](https://raptoreum.zone/get-started)

Follow the instructions from the pool to generate a mining configuration and update your `config.json`.

#### Create `config.json`
```sh
cd ~/xmrig/
nano config.json
```

Example `config.json`:
```json
{
  "autosave": true,
  "donate-level": 1,
  "cpu": {
    "enabled": true,
    "huge-pages": true,
    "priority": 3,
    "max-threads-hint": 50,
    "asm": true
  },
  "opencl": false,
  "cuda": false,
  "pools": [
    {
      "algo": "ghostrider",
      "url": "europe.raptoreum.zone:3333",
      "user": "YOUR_WALLET.WORKER",
      "pass": "x",
      "tls": false,
      "keepalive": true,
      "nicehash": false
    }
  ]
}
```
Replace `YOUR_WALLET.WORKER` with your actual wallet address and worker name.

---

## 5. Start Mining
### Use Screen (Recommended for Remote Mining)
Start a screen session:
```sh
screen -S mining
```
Run XMRig:
```sh
cd ~/xmrig/build
./xmrig --config config.json
```
Detach the session:
```sh
CTRL + A, then D
```
Reopen the session:
```sh
screen -r mining
```
XMRig will display stats about your mining performance, including hash rates and accepted shares.

### Monitor Raspberry Pi Temperature
```sh
vcgencmd measure_temp
```
Monitor temperature continuously:
```sh
watch -n 1 vcgencmd measure_temp
```
#### Safe Temperature Ranges:
- Below 50°C → ✅ Ideal
- 50-70°C → ⚠️ Warm, but okay
- Above 70°C → 🔥 Too hot! Consider cooling solutions

#### Cooling Suggestions:
- Lower "max-threads-hint" in `config.json`
- Use a small cooling fan or heat sink
- Place the Raspberry Pi in a well-ventilated area

---

## 6. Optimize Performance (Optional)
- Adjust the `max-threads-hint` value in `config.json` based on your Raspberry Pi’s CPU cores.
- Consider overclocking your Raspberry Pi for better performance but monitor temperatures carefully.

---

## 7. Monitor Your Mining Earnings
Check your mining pool's dashboard to track earnings via https://raptoreum.zone/miners/<YOUR_WALLET_ADDRESS>

---

## Conclusion
Mining Raptoreum on a Raspberry Pi 2 Model B is possible, though performance is limited due to hardware constraints. However, it’s a great way to experiment with cryptocurrency mining on low-power devices!

Happy mining!

