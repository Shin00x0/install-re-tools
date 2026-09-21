#!/bin/bash

set -e

GHIDRA_VERSION="11.4.2"
GHIDRA_ZIP="ghidra_${GHIDRA_VERSION}_PUBLIC_20250826.zip"
GHIDRA_URL="https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_${GHIDRA_VERSION}_build/${GHIDRA_ZIP}"

echo "[+] Actualizando paquetes..."
sudo apt update

echo "[+] Instalando dependencias básicas..."
sudo apt install -y \
    wget \
    curl \
    unzip \
    tar \
    software-properties-common \
    ca-certificates \
    build-essential

echo "[+] Instalando Python..."
sudo apt install -y \
    python3 \
    python3-pip \
    python3-venv \
    python3-dev

echo "[+] Instalando Java JDK 21..."
sudo apt install -y openjdk-21-jdk

echo "[+] Instalando herramientas de debugging..."
sudo apt install -y \
    gdb \
    gdbserver \
    lldb \
    strace \
    ltrace \
    checksec \
    file \
    binutils \
    patchelf

echo "[+] Instalando herramientas Python útiles..."
pip3 install --break-system-packages \
    pwntools \
    requests



echo "[+] Descargando Ghidra..."
cd /tmp
wget -O "${GHIDRA_ZIP}" "${GHIDRA_URL}"

echo "[+] Instalando Ghidra..."
sudo mkdir -p /opt/ghidra
sudo unzip -q "${GHIDRA_ZIP}" -d /opt/ghidra

GHIDRA_DIR=$(find /opt/ghidra -maxdepth 1 -type d | grep ghidra_ | head -n 1)

echo "[+] Creando launcher..."
sudo tee /usr/local/bin/ghidra > /dev/null <<EOF
#!/bin/bash
${GHIDRA_DIR}/ghidraRun
EOF

sudo chmod +x /usr/local/bin/ghidra

echo "[+] Verificando instalaciones..."
python3 --version
java --version
gdb --version | head -n 1

echo
echo "[+] Instalación completada"
echo "[+] Ejecuta Ghidra con:"
echo "    ghidra"


wget -O ~/.gdbinit-gef.py -q https://gef.blah.cat/py
echo source ~/.gdbinit-gef.py >> ~/.gdbinit

