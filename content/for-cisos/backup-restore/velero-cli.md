# Velero CLI setup

For Velero related tasks/sample. It's better to setup the Velero CLI. It can perform various task against the Velero server deployed on the cluster.

Download the appropriate release from here <https://github.com/vmware-tanzu/velero/releases> and then follow the steps:

Note: Below commands are Linux specific

1. Open a command line and change directory to the Velero CLI download.
1. To unzip the download file:

    ```sh
    tar -xzf velero-<VERSION>-<PLATFORM>.tar.gz 
    cd velero-<VERSION>-<PLATFORM>/
    ```

1. To change the permissions and put the Velero CLI in the system path:

    ```sh
    chmod +x velero
    ```

1. To make the Velero CLI globally available, move the CLI to the system path:

    ```sh
    cp velero /usr/local/bin/velero
    ```

1. Verify the installation:

    ```sh
    velero version
    ```

    Should see something like:

    ```sh
    velero version

    Client:
        Version: v1.4.2
    ```
