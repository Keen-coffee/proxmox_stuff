# Install the required Linux kernel packages
sudo apt install -y linux-headers-6.8.0-41-generic \
                    linux-headers-6.8.0-41 \
                    linux-image-6.8.0-41-generic \
                    linux-modules-6.8.0-41-generic \
                    linux-tools-6.8.0-41-generic \
                    linux-modules-extra-6.8.0-41-generic

# Mark the installed packages to prevent updates
sudo apt-mark hold linux-headers-6.8.0-41-generic \
                     linux-headers-6.8.0-41 \
                     linux-image-6.8.0-41-generic \
                     linux-modules-6.8.0-41-generic \
                     linux-tools-6.8.0-41-generic \
                     linux-modules-extra-6.8.0-41-generic

# Override the linux-check-removal script
sudo mv /usr/bin/linux-check-removal /usr/bin/linux-check-removal.orig
echo -e '#!/bin/sh\necho "Overriding default linux-check-removal script!"\nexit 0' | sudo tee /usr/bin/linux-check-removal
sudo chmod +x /usr/bin/linux-check-removal

# Remove old kernel packages
sudo apt remove -y linux-headers-$(uname -r) \
                    linux-image-$(uname -r) \
                    linux-modules-$(uname -r) \
                    linux-tools-$(uname -r) \
                    linux-modules-extra-$(uname -r)

# Add i915 options to modprobe configuration
echo "options i915 force_probe=7d55 enable_guc=3" | sudo tee -a /etc/modprobe.d/i915.conf

# Create firmware directory and download required firmware files
sudo mkdir -p /lib/firmware/i915
sudo wget https://github.com/intel-gpu/intel-gpu-firmware/raw/main/firmware/mtl_gsc_102.0.0.1511.bin -O /lib/firmware/i915/mtl_gsc_102.0.0.1511.bin
sudo wget https://github.com/intel-gpu/intel-gpu-firmware/raw/main/firmware/mtl_guc_70.6.4.bin -O /lib/firmware/i915/mtl_guc_70.6.4.bin
sudo wget https://github.com/intel-gpu/intel-gpu-firmware/raw/main/firmware/mtl_huc_8.4.3_gsc.bin -O /lib/firmware/i915/mtl_huc_8.4.3_gsc.bin

# Reboot the system
sudo shutdown -r now &

# Clone the i915 SR-IOV DKMS repository
git clone https://github.com/strongtz/i915-sriov-dkms

# Install DKMS dependencies
sudo apt install build-essential dkms -y

# Add and install the i915 SR-IOV DKMS module
cd i915-sriov-dkms && sudo dkms add .
cd i915-sriov-dkms && sudo dkms install -m i915-sriov-dkms -v $(cat VERSION) --force

# Update initramfs
sudo update-initramfs -u

# Reboot the system
sudo shutdown -r now &
