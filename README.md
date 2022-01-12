# xo-archiver
Bash script to automate the archiving of a VM running on Xen Orchestra, on a S3 bucket

AWS S3 is the only supported remote storage for the moment.


## Installation

1. Put the `xo-archiver` in your `PATH`
2. Install [xo-cli](https://github.com/vatesfr/xen-orchestra/tree/master/packages/xo-cli#install)
3. Install and configure `awscli`  
    AWS region, profile, access_keys etc. are not managed by this script.  
    This script considers the `aws` command to be working and able to push objects to an S3 bucket.
4. Install following binaries: `jq`


## Usage

```
$ xo-archiver --help
SYNOPSIS: xo-archiver <COMMAND> <SELECTOR> [OPTIONS]
  Wizard to manipulate Xen Orchestra exports to an s3 bucket

COMMANDS:
  export
        Creates a manual export of a VM in a compressed and restorable format (XVA)
        Also exports some metadata needed to restore the VM to the same location
        To be used with --vm-id or --vm-name

  push
        Pushes exported files (XVA+metadata) to S3.
        To be used with --vm-id or --vm-name
        S3 bucket name should be define as an envvar, see below CONFIG FILE section.
        Files will be pushed to S3 into "s3://bucket_name/$VM-NAME/", like:
            foo-bar-01
            ├── 622870c0-0d21-286b-df2d-05ff2bab6a45.xva
            ├── SR-ID.txt
            └── VM.json

  pull
        Pulls archived files (XVA+metadata) from S3.
        To be used with --archived-vm-name
        S3 bucket name should be define as an envvar, see below CONFIG FILE section.
        Files under "s3://bucket_name/$ARCHIVED-VM-NAME/" will be copied locally, like:
            foo-bar-01
            ├── 622870c0-0d21-286b-df2d-05ff2bab6a45.xva
            ├── SR-ID.txt
            └── VM.json

  delete
        Deletes the Virtual Machine and its associated disks in XOA
        To be used with --vm-id or --vm-name

  archive
        Calls those commands, in this order: export, temporarily-restores-xva, push and delete
        To be used with --vm-id or --vm-name

  import
        Imports the specified XVA file (compressed zstd) into the specified SR.
        To be used with --sr-id and --xva-file

  temporarily-restores-xva
        Restores the given XVA file into the specified Storage Repository,
        and deletes this newly created VM right away.
        This command ensures that the file is restorable by actually restoring it,
        and deleting the VM thus created. VM will stay in Halted state all along.
        To be used with --sr-id and --xva-file

  restore
        Call those commands, in this order: pull, get-metadata-from-local-files, import and clean
        To be used with --archived-vm-name

  get-metadata-from-local-files
        Checks that local files contain mandatory informations: SR-ID and a readable XVA-file.
        To be used with --archived-vm-name or --vm-id or --vm-name

  clean
        Removes local files associated with the provided selector inside LOCAL_TMP_DIR
        This script handles local files properly, but in case of an error,
        it may be necessary to run this command.
        To be used with --archived-vm-name or --vm-id or --vm-name


SELECTORS:
  --vm-id <id>
        XO's VM ID or snapshot ID.
        E.g: --vm-id 622870c0-0d21-286b-df2d-05ff2bab6a45
        If used at the same time as --vm-name, takes precedence

  --vm-name <name_label>
        XO's VM name as printed in the GUI, which refers to name_label objects
        E.g: --vm-name foo-bar-01
        If used in conjunction with --vm-id, then will be ignored
        TODO: handle multiple results. Currently fails in such case.

  --archived-vm-name <name_label>
        Name used as prefix in the S3 bucket, to identify a VM.
        That name was the one printed in the GUI of XO.
        E.g: --archived-vm-name foo-bar-01
        This is different from --vm-name because the VM does not currently exist in XO and
        therefore the two workflows are totally different, as is the name of this selector.

  --sr-id <id>
        The ID of XO's Storage Repository where to import the VM.
        E.g: --sr-id 2505a86a-0d21-286b-df2d-05ff2bab6a45

  --xva-file <full-path>
        Fullpath of the VM file, in xva compressed (zstd) format.
        E.g: --xva-file /mnt/xo-archives/622870c0-0d21-286b-df2d-05ff2bab6a45.xva


OPTIONS:
  -h, --help
        Prints this help


CONFIG FILE:
  All envvar can be set through file "~/.config/.xo-archiver/config" prefixed by 'export'
  E.g:
        $ cat ~/.config/.xo-archiver/config
        export XO_HOST=https://xoa.foo.bar
        export XO_USER=admin@admin.net


  Supported values:

  XO_HOST
        URL of your XOA
        E.g: https://xoa.foo.bar

  XO_USER
        user that can perform exports, import and start VM.
        E.g: admin@admin.net

  XO_CLI_CONFIG
        xo-cli config file path
        Defaults to '~/.config/xo-cli/config.json'

  XO_CLI_EXPIRE
        Duration after which the XOA token expires.
        It uses the "date" binary format
        Defaults to '24hour'

  LOCAL_TMP_DIR
        Local directory in which files will be temporary stored
        Defaults to '/tmp/xo-archiver'

  AWS_BUCKET_XO_ARCHIVES
        AWS's S3 bucket name where files are archived
        E.g: my-xen-archives
        AWS cli also uses other envvars like AWS_ACCESS_KEY_ID, etc.
        All those envvars can be defined inside config file,
        but are not meant to be handled by this script.
```

## License

GPL-3.0 © [BedrockStreaming](https://www.bedrockstreaming.com/)
