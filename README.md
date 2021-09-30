# xo-archiver
Bash script to automate the archiving of a VM running on Xen Orchestra

AWS S3 is the only supported remote storage for the moment.


## Installation

1. Put the `xo-archiver` in your `PATH`
2. Install [xo-cli](https://github.com/vatesfr/xen-orchestra/tree/master/packages/xo-cli#install)
3. Install and configure `awscli`
    AWS region, profile, access_keys etc. are not managed by this script.
    This script considers the `aws` command to ba working and able to push objects to an S3 Bucket.
4. Install following binaries: `jq`


## License

GPL-3.0 © [BedrockStreaming](https://www.bedrockstreaming.com/)
