---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "ovirt_disk_attachments Resource - terraform-provider-ovirt"
subcategory: ""
description: |-
  The ovirtdiskattachments resource attaches multiple disks to a single VM in one operation. It also allows for removing all attachments that are not declared in an attachment block. This is useful for removing attachments that have been added from the template.
  ~> Do not use this resource on the same VM as ovirtdiskattachment (singular). It will cause a ping-pong effect of resources being created and removed on each Terraform run.
---

# ovirt_disk_attachments (Resource)

The ovirt_disk_attachments resource attaches multiple disks to a single VM in one operation. It also allows for removing all attachments that are not declared in an attachment block. This is useful for removing attachments that have been added from the template.

~> Do not use this resource on the same VM as ovirt_disk_attachment (singular). It will cause a ping-pong effect of resources being created and removed on each Terraform run.

## Example Usage

```terraform
resource "ovirt_disk" "test" {
  storagedomain_id = var.storagedomain_id
  format           = "raw"
  size             = 512
  alias            = "test"
  sparse           = true
}

resource "ovirt_vm" "test" {
  name        = "hello_world"
  comment     = "Hello world!"
  cluster_id  = var.cluster_id
  template_id = "00000000-0000-0000-0000-000000000000"
}

resource "ovirt_disk_attachments" "test" {
  vm_id            = ovirt_vm.test.id
  # Set the following to true to completely remove non-listed attached disks. This can be used to wipe disks from the
  # template.
  remove_unmanaged = false

  # You can repeat this section as many times as you need.
  attachment {
    disk_id        = ovirt_disk.test.id
    disk_interface = "virtio_scsi"
  }
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- **attachment** (Block Set, Min: 1) (see [below for nested schema](#nestedblock--attachment))
- **vm_id** (String) ID of the VM the disks should be attached to.

### Optional

- **detach_unmanaged** (Boolean) Detach unmanaged disks from the VM. This is useful for detaching disks that have been inherited from the template or added manually. The detached disks will not be removed and can be used. To remove the disks instead, use `remove_unmanaged`.
- **remove_unmanaged** (Boolean) Completely remove attached disks that are not listed in this resources. This is useful for removing disks that have been inherited from the template or added manually.

~> Use with care! This option will delete all disks attached to the current VM that are not managed, not just detach them!

### Read-Only

- **id** (String) Meta-identifier for the disk attachments. Will always be the same as the VM ID after apply.

<a id="nestedblock--attachment"></a>
### Nested Schema for `attachment`

Required:

- **disk_id** (String) ID of the disk to attach. This disk must either be shared or not yet attached to a different VM.
- **disk_interface** (String) Type of interface to use for attaching disk. One of: `ide`, `sata`, `spapr_vscsi`, `virtio`, `virtio_scsi`.

Read-Only:

- **id** (String) The ID of this resource.

## Import

Import is supported using the following syntax:

```shell
# Import a disk attachment using the VM ID from the oVirt Engine.
terraform import ovirt_disk_attachments.test 3b940b57-d3a5-448e-9bb3-0d73b76fbb08
```
