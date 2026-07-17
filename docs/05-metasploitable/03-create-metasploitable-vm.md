# Create Metasploitable VM

Metasploitable is usually distributed as a virtual machine disk. Download it from a trusted source, extract it, and register it in VirtualBox on the homelab.

Do not publish Metasploitable credentials in your GitHub documentation.

## Place The Disk File

Put the extracted Metasploitable disk under:

```text
/home/<HOMELAB_USER>/vms/metasploitable2/
```

In the commands below, the disk path is written as:

```text
<METASPLOITABLE_VMDK_PATH>
```

## Create The VM

Run:

```bash
VBoxManage createvm \
  --name metasploitable2 \
  --ostype Ubuntu \
  --register
```

## Attach The Disk

Run:

```bash
VBoxManage storagectl metasploitable2 \
  --name "IDE Controller" \
  --add ide

VBoxManage storageattach metasploitable2 \
  --storagectl "IDE Controller" \
  --port 0 \
  --device 0 \
  --type hdd \
  --medium <METASPLOITABLE_VMDK_PATH>
```

## Set Basic Resources

Run:

```bash
VBoxManage modifyvm metasploitable2 \
  --memory 1024 \
  --cpus 1 \
  --audio none \
  --usb off
```

Metasploitable is old and lightweight. It does not need much memory.

## Verify The VM Exists

Run:

```bash
VBoxManage list vms
VBoxManage showvminfo metasploitable2
```

Expected result:

- `metasploitable2` appears in the VM list
- the virtual disk is attached
- the VM is ready for network configuration

## Next Step

Continue to [Host Only Network](./04-host-only-network.md).
