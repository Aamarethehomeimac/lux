My Vision for Lux: A Customizable Partition Table (Pre-Alpha)
Author: Aamare
Date: March 29, 2025 (Pre-Alpha Concept)

Introduction:

I'm developing Lux, a new partition table scheme and its accompanying command-line tool (lux). My goal with Lux is to empower advanced users with unparalleled configurability and the potential for optimized performance. I'm not aiming for simplicity; instead, I want to provide the tools for granular control over disk organization. Lux is designed to be built from source, allowing for deep pre-build customization tailored to specific systems.

The Lux Partition Table Format:

My design for Lux defines partitions using a string-based syntax. Here's how it works:

*(d(for disk)#(for number)) (name) (flags:...) (fs;...) (amount on disk) (mount point) (verbose=yes)

Let me explain each part:

(d(for disk)#(for number)): This is how I identify the disk and partition number. For example, d01 would be the first partition on the first standard disk. I'm also using prefixes like sd for SD cards, usb for USB drives, rd for other removable media, and nd for network drives. For standard internal disks, I use d followed by a number.
(name): Here, you can give your partition a descriptive name.
(flags:...): This is where the real power lies. You can specify a colon-separated list of flags to define the partition's attributes. I've included flags like bootable, encrypt, latest_boot (which I see as the EFI System Partition), msformat, raid_array, lvm_part, swap, swap_hibernate, hide, and bls_boot_partition for BootLoaderSpec compliance.
(fs;...): This semicolon-separated list tells the system what filesystem I intend to use on the partition. I'm supporting a wide range, including ext4, ext3, ext2, xfs, btrfs, zfs, reiser, f2fs, jfs_beta, docker_overlayfs_beta, ntfs_unstable, fat32, exfat, refs_beta, apple_hfs, nfs, microsoft_smb (with cifs as an alternative flag), iso_9660_dvd, iso_9660_cd, and tmpfs.
(amount on disk): I'm letting you specify the partition size in gigabytes (GB), from a tiny 0.01GB up to a massive 2147583647GB (a common 32-bit limit). For those who need even more space, adding the *bits_64* flag will allow up to 18446744073709551615GB. Of course, you can't allocate more space than your disk has.
(mount point): This is where you tell the system where you want to access the partition in the file system (e.g., /, /home).
(*verbose=yes): If you want more detailed output during operations, you can add this optional flag.
Here are a few examples of how I envision using this format:

(d1) mypartition bootable btrfs 23G /mnt/mypartition

(sd1) usb_data fat32 4G /mnt/usb

(d3) my_swap_space swap fat32 4G /swap

(d2) data_storage encrypt,lvm_part xfs 500G /data

The lux Command-Line Tool: My Interface to Lux:

To manage disks and partitions with Lux, I'm building a command-line tool called lux. It's not meant to be a simple install; I envision users building it from the source code, and it might even require some tweaking for your specific system architecture before you compile it.

These are the commands I've designed so far:

lux create "{partition_definition}" (quick): This will let you create new partitions based on the Lux string you provide. If you add the quick flag, it will do a fast format without erasing existing data. If you leave it out, it will perform a more thorough format that will erase data (better for installing an OS).
lux getinfo (disk_number or mount_point): I want you to be able to easily get information about a partition (like its name, identifier, flags, size, and mount point) by just giving the disk number or its mount point.
lux fix (disk_identifier): This command will be for diagnosing a specific disk, running checks, and telling you if everything is OK or if there's an Error.
lux error: If lux fix finds an error, you can use this command to get more details about what went wrong.
lux sandbox (disk_identifier)/(script_path): For safety, I'm including a way to run scripts that interact with your disks in a temporary Alpine Linux virtual machine. This way, you can test things without risking your main system.
lux mount (partition_identifier) (mount_point): This will do exactly what it says – mount a Lux partition to a specified location.
lux umount (partition_identifier or mount_point): And this will unmount a Lux partition, either by its identifier or its mount point.
lux garbage (disk_label): For securely erasing data, this command will overwrite everything on a disk or partition with random data. I'm making sure to include strong warnings and a password confirmation because this is irreversible.
lux flash (image_location) (disk_label): This will let you write a disk image (like an ISO) to a specified disk or partition, and it will also have warnings and password confirmation because it will erase everything.
lux copy (disk_label): This will create a full image (a .img file) of an entire disk or a single partition.
lux clone (disk_label) (receiving_disk_label): You'll be able to make an exact copy from one disk or partition to another, but be careful, as everything on the receiving disk will be overwritten.
lux set-location (disk_label) (place_in_the_world): This is a bit of a niche feature, but I want to let you associate a geographical location with a disk or partition. I'm thinking this could be useful for things like time zone servers or managing operations in specific countries. Remember to use lowercase for country names and underscores for spaces (e.g., new_york).
lux download (site or url to file) (disk_label): I want to make it easy to download files directly from the internet to a mounted Lux partition.
lux experiments: I'll have a place for trying out new, less stable features like RAID configuration, network access, and iPXE booting. Once these are well-tested, they might become standard flags.
lux tree: This will be like lsblk but specifically for Lux, showing you a clear, hierarchical view of your Lux-managed disks and partitions.
lux prepare-for-linux-install (disk_label): If you want to use a Lux-partitioned disk for a standard Linux installation, this command will wipe the Lux formatting and leave it as unallocated space so the installer can take over.
lux optimise-for-game (disk-where-installed-system-is) (game): This is a special one – it will try to optimize an SSD for a specific game. It will only work on SSDs where your system is installed and you have enough space. It won't support Hackintoshes, and you'll need to enable it first with lux edit-os-files true and enter your password.
lux edit-os-files (true/false): This is a safety switch. Setting it to true (after entering your password) will allow certain lux commands, like optimise-for-game, to make deeper system-level changes. You should probably set it back to false afterward.
lux set-coding-environment (disk_label) (programming_language) (name_for-disk): For developers, this will let you associate a programming language and a friendly name with a specific partition.
lux create-android-virtualization-device (disk_label) (video or no-video) (sound or no-sound) (apkinstall or no-apkinstall) (network or no network) (ppc or no-ppc): This will help you prepare a Lux partition to be used by an Android virtual machine, letting you configure things like video, sound, network, and Play Protect Certification.
lux apply-format <filename>.format: To make things easier for less experienced users, I'm thinking about letting advanced users create .format files. These files would start with enable_lux+ true and then contain a predefined Lux partition layout. Newbies could then use this command to apply that layout without having to understand the Lux syntax themselves.
Important Things to Keep in Mind:

Lux is really for advanced users who want a lot of control. If you're new to this, I'd recommend sticking with GPT or MBR. Building Lux will require compiling it from source, and you might need to tweak some configuration files for your specific computer. Many of the powerful features in Lux will erase your data, so you need to be careful. Also, keep in mind that Lux won't work with every system out there – its compatibility is finite.

My Vision for the Future:

I'm hoping the community will help build Lux into package managers for different Linux distributions. I also have a bigger dream: to build an entire Unix-like operating system using Lux as its partition table. I'm calling it Susmo, and I envision it being something like BSD.

Ultimately, I think Lux is pretty cool. It's about giving power and customization to those who want it. This is all still in the early stages, though, so things might change.
