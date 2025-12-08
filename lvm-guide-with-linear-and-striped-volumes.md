# LVM guide (with linear and striped volumes)

LVM offers several advantages over a traditional, single volume. There are
essentially two schemes one can use:

- **Linear volume**, which allows for dynamically adding and removing disks
  from the pool. In practice, it will fill one whole disk before moving on to
  the next disk.
- **Striped volume**, which allows for better parallel performance. This
  functions somewhat like a software-defined RAID 0.

## What is LVM?

LVM stands for Logical Volume Management, and is a system built into most
modern Linux distributions. It allows you to combine disks of various sizes,
capacities, and types into different types of “logical volumes”, allowing you
to distribute and manage your data much more easily than traditional
partitions.

There are essentially three layers to LVM:

1. Physical volumes (PV)
2. Volume groups (VG)
3. Logical volumes (LV)

Let’s look at each one in turn.

### Physical volumes (PV)

Physical volumes (PV's) are any sort of physically-bound disk space. PV’s are
usually something like a disk or a partition on a disk, but could also be a
RAID or an iSCSI target.

There’s not much abstraction yet at this level, as they represent the physical
representation and not any sort of logical representation. You can add a disk
(or partition, etc.) to LVM by using the pvcreate command. This will format the
device so LVM can use it.

### Volume groups (VG)

Volume groups (VG’s) combine one or more physical volumes (PV’s) together into
a logical grouping. A PV can only be a member of one VG at a time. Volume
groups allow you to combine disks of varying capacities, types, etc., just as
long as they are formatted as LVM physical volumes, into a single pool of disk
resources.

### Logical volumes (LV)

A logical volume (LV) is sort of like a “partition” that you create within a
volume group (VG). It can be as big or as small as you want, just as long as
you don’t exceed the capacity of the VG*.

You can then create a filesystem on a logical volume in much the same way that
you would a traditional disk partition. The filesystem behaves just like any
other filesystem, but with the added flexibility of being stored on multiple
physical volumes.

*You can bypass this restriction by creating a “thin” logical volume, which
will dynamically expand as needed.

## Why use LVM?

Logical volume management (LVM) provides several advantages, both for
flexibility and performance.

### LVM improves flexibility

Because LVM abstracts away the physical properties of the underlying disks, you
are free to add, remove, or change the underlying disk structures without
affecting the filesystem(s) that live on them. For example, you can attach new
disks to the system, add them to a volume group, then expand the logical volume
(and, optionally, filesystem) to take up that new space, all without needing to
take the filesystem offline. You can also reduce or swap out disks as needed
within the volume group without the filesystem ever knowing that something has
changed.

### LVM improves performance

By creating striped volumes, you can perform your reads and writes in parallel,
by accessing two or more disks at a time. This is especially beneficial for
something like and XCP-ng virtual machine, as disk I/O is performed in a
single-threaded process called `tapdisk`. XCP-ng creates one `tapdisk` process
per virtual disk, meaning the I/O, when using a single VDI, is limited to a
single stream of data. Using striped volumes with LVM allows you to spin up
multiple `tapdisk` processes and use them in parallel.

## Creating and using an LVM volume

### 1. Attach disks / physical volumes (PVs) to the VM

- If using XCP-ng, ensure the VM has a DVD drive before continuing. Why?
  - XCP-ng expects the DVD drive to be at position 3. If you start adding VDIs
    without a DVD drive present, a VDI may be attached at position 3, making it
    difficult to add a DVD drive afterward.
  - To add a DVD drive, simply attach an ISO to the VM and (if necessary)
    power-cycle the VM.
- Calculate the number of VDIs you’re going to need.

  ```txt
  round($total_space_needed / 0.75 / 64) = num_of_vdis
  ```

  - Try to not go over 75% utilization.
- Split your total space needed into 64 GiB VDIs.
  - If creating a striped volume, look for a multiple of 2, 3, or 4. This will
    be your number of stripes. Performance benefits diminish with more than 4
    stripes for a volume.

    ```txt
    2  disks -- ok
    3  disks -- ok
    4  disks -- ok
    5  disks -- BAD
    6  disks -- ok
    7  disks -- BAD
    8  disks -- ok
    9  disks -- ok
    10 disks -- ok
    11 disks -- BAD
    12 disks -- ok
    13 disks -- BAD
    ```

- Create and attach enough VDIs to accommodate all the data.

### 2. Pool the PVs into a volume group (VG)

- Create a volume group (VG). The VG is named vg0 in the following examples.
  Replace {b,c,e,f,g,h} with whatever drive letters you want to use in the
  following commands.
  - For linear volumes:

    ```sh
    vgcreate vg0 /dev/xvd{b,c,e,f,g,h}
    ```

  - For striped volumes, specify a physical extent size of 64k with `-s 64k`:

    ```sh
    vgcreate -s 64k vg0 /dev/xvd{b,c,e,f,g,h}
    ```

- Troubleshooting
  - Trying to do this with more than ~ 20 disks?
    - Create the volume group first

      ```sh
      vgcreate vg0 /dev/xvdb
      # or, if creating a striped volume
      vgcreate -s 64k vg0 /dev/xvdb
      ```

    - Then add disks one by one or in batches:

      ```sh
      vgextend vg0 /dev/xvdc
      vgextend vg0 /dev/xvd{e,f}
      ```

    - Why? Some commands (like `vgcreate` or `vgextend`) can handle a maximum of
      24 arguments. If you try to include more than this number of disks in any
      of the commands, it will refuse to add any of them with the output of "foo
      was excluded by a filter" (or something along those lines).
  - Don’t you need to create a PV first, then add it to the VG?
    - Assuming the disks are empty, `vgcreate` and `vgextend` will do this for
      you automatically. If they are not empty, then yes, you may need to use
      `pvcreate` manually before using `vgcreate` or `vgextend`.

### 3. Create a logical volume (LV)

Create a logical volume (LV) within the volume group (VG).

- For a linear volume:

  ```sh
  lvcreate --extents +100%FREE --name srv vg0
  ```

- For a striped volume:

  ```sh
  lvcreate --stripes $stripes --extents +100%FREE --stripesize 64 --name srv vg0
  ```

  - Your `$stripes` should be 2, 3, or 4, depending on the number of VDIs you
    chose.
  - Going above 4 stripes does not bring any additional benefits, it only
    increases complexity.

### 4. Add a filesystem to the logical volume (LV)

Create an ext4 (or whatever) filesystem on the logical volume (LV).

- For a linear volume

  ```sh
  mkfs.ext4 /dev/vg0/srv
  ```

- For a striped volume
  - First, calculate your stripe width. `stripe_width = 16 * $num_of_stripes`
  - Then, create the filesystem.

    ```sh
    mkfs.ext4 -b 4096 -E stride=16,stripe_width=${stripe_width} /dev/vg0/srv
    ```

  - Troubleshooting
    - The stride is 16. Why?
      - `stride = stripe_size / block_size`
      - In our `lvcreate` command above, we used a stripe size of 64 KiB
        (`--stripesize 64`) and a block size of 4 KiB (`-b 4096`), so
        `stripe_size = 64` and `block_size = 4`.
      - `stride = 64 / 4`
      - `stride = 16`
    - The stripe width is the stride multiplied by the number of stripes.
      - `stripe_width = stride * num_of_stripes`
      - `stripe_width = 16 * num_of_stripes`

### 5. Configure the mount point

See the output of `blkid` to get the UUID of the new filesystem. You can then
add it to `/etc/fstab` as you would any other filesystem.

## Cookbook

### Add an additional disk to a linear LV

> [!IMPORTANT]
> This only applies to linear LV’s.

- Create another 64 GiB (or whatever size) virtual disk and attach it to the
  VM.
- Follow the steps above to prepare the disk to be added to the volume group.
- Add the new PV to the LVM Volume Group (VG). For example, if the new PV is
  `/dev/xvdn`, and our VG is `vg0`:

  ```sh
  vgextend vg0 /dev/xvdn
  ```

- Extend the LV to include the new free space. Resize the underlying filesystem
  as well with the `-r` flag. In the example below, the VG name is `vg0` and
  the LV name is `srv`.

  ```sh
  lvresize -l +100%FREE -r vg0/srv
  ```

### Remove a physical volume (PV) from a volume group (VG)

> [!IMPORTANT]
> This only applies to linear LV’s.

Let’s say you added too many disks to a system, and you need to reclaim some of
that space. We’ll remove a PV at `/dev/xvdo` from the volume group `vg0`.

Assuming there’s a single logical volume (LV) which uses up the whole volume
group (VG), shrink the filesystem and LV by the amount you need to remove. For
example, if you need to remove a 64 GiB physical volume (PV) from the `vg0` VG,
you would use the following `lvresize` command:

```sh
lvresize --size -64G --resizefs vg0/srv
```

Ensure there is no data on the physical volume (PV) with `pvmove`:

```sh
pvmove /dev/xvdo
```

Now you can remove the physical volume (PV) from the volume group (VG) with
`vgreduce`:

```sh
vgreduce vg0 /dev/xvdo
```

Now expand the logical volume (LV) to take up whatever leftover space there is
in the volume group (VG) with `lvresize`:

```sh
lvresize --extents +100%FREE --resizefs vg0/srv
```
