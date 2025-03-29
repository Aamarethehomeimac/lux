# My Vision for Lux: A Customizable Partition Table (Pre-Alpha) - Improved Spacing
Author: Aamare
Date: March 29, 2025 (Pre-Alpha Concept)

Introduction:

I'm developing Lux, a new partition table scheme and its accompanying command-line tool (lux). My goal with Lux is to empower advanced users with unparalleled configurability and the potential for optimized performance. I'm not aiming for simplicity; instead, I want to provide the tools for granular control over disk organization. Lux is designed to be built from source, allowing for deep pre-build customization tailored to specific systems.

# The Lux Partition Table Format:

My design for Lux defines partitions using a string-based syntax. Here's how it works, with more visual spacing:

*(d(for disk)#(for number))   (name)   (flags:...)   (fs;...)   (amount on disk)   (mount point)   (verbose=yes)

Let me explain each part:

(d(for disk)#(for number)): This is how I identify the disk and partition number. For example, d01 would be the first partition on the first standard disk. I'm also using prefixes like sd for SD cards, usb for USB drives, rd for other removable media, and nd for network drives. For standard internal disks, I use d followed by a number.

(name): Here, you can give your partition a descriptive name.

(flags:...): This is where the real power lies. You can specify a colon-separated list of flags to define the partition's attributes. I've included flags like bootable, encrypt, latest_boot (which I see as the EFI System Partition), msformat, raid_array, lvm_part, swap, swap_hibernate, hide, and bls_boot_partition for BootLoaderSpec compliance.

(fs;...): This semicolon-separated list tells the system what filesystem I intend to use on the partition. I'm supporting a wide range, including ext4, ext3, ext2, xfs, btrfs, zfs, reiser, f2fs, jfs_beta, docker_overlayfs_beta, ntfs_unstable, fat32, exfat, refs_beta, apple_hfs, nfs, microsoft_smb (with cifs as an alternative flag), iso_9660_dvd, iso_9660_cd, and tmpfs.

(amount on disk): I'm letting you specify the partition size in gigabytes (GB), from a tiny 0.01GB up to a massive 2147583647GB (a common 32-bit limit). For those who need even more space, adding the *bits_64* flag will allow up to 18446744073709551615GB. Of course, you can't allocate more space than your disk has.

(mount point): This is where you tell the system where you want to access the partition in the file system (e.g., /, /home).

(*verbose=yes): If you want more detailed output during operations, you can add this optional flag.

# Here are a few examples of how I envision using this format:

(d1) mypartition bootable btrfs 23G /mnt/mypartition

(sd1) usb_data fat32 4G /mnt/usb

(d3) my_swap_space swap fat32 4G /swap

(d2) data_storage encrypt,lvm_part xfs 500G /data

# The lux Command-Line Tool: My Interface to Lux:

To manage disks and partitions with Lux, I'm building a command-line tool called lux. It's not meant to be a simple install; I envision users building it from the source code, and it might even require some tweaking for your specific system architecture before you compile it.

These are the commands I've designed so far, with more spacing for readability:

lux create "{partition_definition}" (quick): Creates a new partition based on the Lux string. quick performs a fast, non-erasing format. Omitting quick results in a slower, data-erasing format.

lux getinfo (disk_number or mount_point): Displays partition details (name, identifier, flags, size, mount point).

lux fix (disk_identifier): Diagnoses the specified disk and reports its state (OK or Error).

lux error: Provides detailed error information after lux fix reports an error.

lux sandbox (disk_identifier)/(script_path): Executes a script within a temporary Alpine Linux VM for safe disk interaction.

lux mount (partition_identifier) (mount_point): Mounts a Lux partition.

lux umount (partition_identifier or mount_point): Unmounts a Lux partition.

lux garbage (disk_label): Securely overwrites data on a disk/partition with warnings and password confirmation.

lux flash (image_location) (disk_label): Writes a disk image to a disk/partition with warnings and password confirmation.

lux copy (disk_label): Creates a disk image (.img file) of a disk/partition.

lux clone (disk_label) (receiving_disk_label): Clones one disk/partition to another (overwriting the receiver).

lux set-location (disk_label) (place_in_the_world): Associates a geographical location with a disk/partition (country names lowercase with underscores for spaces).

lux download (site or url to file) (disk_label): Downloads a file to a mounted Lux partition.

lux experiments: Accesses a menu of pre-alpha features (e.g., RAID, network access, iPXE boot).

lux tree: Displays a hierarchical view of Lux-managed disks and partitions.

lux prepare-for-linux-install (disk_label): Prepares a disk for standard Linux installation by removing Lux formatting.

lux optimise-for-game (disk-where-installed-system-is) (game): Optimizes an SSD for a specific game (requires lux edit-os-files true and password).

lux edit-os-files (true/false): Enables/disables deeper system-level modifications for certain lux commands (requires password).

lux set-coding-environment (disk_label) (programming_language) (name_for-disk): Associates a programming language and name with a partition.

lux create-android-virtualization-device (disk_label) (video or no-video) (sound or no-sound) (apkinstall or no-apkinstall) (network or no network) (ppc or no-ppc): Prepares a partition for an Android virtual device.

lux apply-format <filename>.format: (Conceptual) Applies a pre-configured formatting script created by advanced users. The .format file would contain enable_lux+ true and Lux partition definitions.

# Important Things to Keep in Mind:

Lux is really for advanced users who want a lot of control. If you're new to this, I'd recommend sticking with GPT or MBR. Building Lux will require compiling it from source, and you might need to tweak some configuration files for your specific computer. Many of the powerful features in Lux will erase your data, so you need to be careful. Also, keep in mind that Lux won't work with every system out there – its compatibility is finite.

# My Vision for the Future:

I'm hoping the community will help build Lux into package managers for different Linux distributions. I also have a bigger dream: to build an entire Unix-like operating system using Lux as its partition table. I'm calling it Susmo, and I envision it being something like BSD.

# Ultimately, I think Lux is pretty cool. It's about giving power and customization to those who want it. This is all still in the early stages, though, so things might change.


