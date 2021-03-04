# ceph

Vagrantfile for testing Ceph installation with `cephadm`.

## Testing

Enable the `disks` experimental feature in Vagrant:

```bash
export VAGRANT_EXPERIMENTAL="disks"
```

```fish
set -x VAGRANT_EXPERIMENTAL "disks"
```

Yet to be automated steps:

```bash
ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph-mon-a
ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph-mon-b
ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph-mon-c

/usr/sbin/cephadm shell --fsid foo -c /etc/ceph/ceph.conf -k /etc/ceph/ceph.client.admin.keyring

ceph orch host add ceph-mon-b
ceph orch host add ceph-mon-c

ceph orch host label add ceph-mon-a mon
ceph orch host label add ceph-mon-b mon
ceph orch host label add ceph-mon-c mon
ceph orch host ls

ceph orch apply mon label:mon
```
