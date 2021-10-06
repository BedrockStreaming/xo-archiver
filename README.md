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
        Creates a manual export of a VM in a compressed and restorable format
        To be used with --vm-id or --vm-name

  push
        Pushes the given export to S3.
        To be used with --vm-id or --vm-name
        S3 bucket name should be define as an envvar, see below CONFIG FILE section.

  archive
        Same as above export + push commands.

  delete
        Deletes the Virtual Machine and its associated disks in XOA
        To be used with --vm-id or --vm-name


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


OPTIONS:
  -h, --help
        Prints this help


CONFIG FILE:
  All envvar can be set through file "~./config/.xo-archiver/config" prefixed by 'export'
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

  LOCAL_XVA_DIR
        Local directory in which XVA will be temporary stored
        Defaults to '/tmp/xva_dir'

  AWS_BUCKET_XVA_ARCHIVES
        AWS's S3 bucket name where XVA files are archived
        E.g: my-xen-archives
        AWS cli also uses other envvars like AWS_ACCESS_KEY_ID, etc.
        All those envvars can be defined inside config file,
        but are not meant to be handled by this script.
```

## License

GPL-3.0 © [BedrockStreaming](https://www.bedrockstreaming.com/)
