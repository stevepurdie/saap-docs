# Cluster Update Process

We can update an OpenShift Container Platform 4 cluster with a single operation by using the web console or the OpenShift CLI (oc).

## Upgrade Channel

With upgrade channels, we can choose an upgrade strategy. Upgrade channels are specific to a minor version of OpenShift Container Platform. Upgrade channels only control release selection and do not impact the version of the cluster that you install.

A channel name consists of three parts: the tier (release candidate, fast, and stable), the major version (4), and the minor version (.2). Example channel names include: stable-4.10, fast-4.10, and candidate-4.10. Each channel delivers patches for a given cluster version.

### Candidate Channel

The candidate channel delivers updates for testing feature acceptance in the next version of OpenShift Container Platform. The release candidate versions are subject to further checks and are promoted to the fast or stable channels when they meet the quality standards.

### Fast Channel

The fast channel delivers updates as soon as they are available. This channel is best suited for development and QA environments. You can use the fast-4.10 channel to upgrade from a previous minor version of OpenShift Container Platform.

### Stable Channel

The stable channel contains delayed updates, which means that it delivers only minor updates for a given cluster version and is better suited for production environments.

Red Hat support and site reliability engineering (SRE) teams monitor operational clusters with new fast updates. If operational clusters pass additional testing and validation, updates in the fast channel are enabled in the stable channel.

If Red Hat observes operational issues from a fast channel update, then that update is skipped in the stable channel. The stable channel delay provides time to observe unforeseen problems in actual OpenShift clusters that testing did not reveal.

## Describing Upgrade Paths

The following describes how these upgrade paths would apply to Red Hat OpenShift Container Platform version 4.10:

- When using the stable-4.10 channel, you can upgrade your cluster from 4.10.0 to 4.10.1 or 4.10.2. If an issue is discovered     in the 4.10.3 release, then you cannot upgrade to that version. When a patch becomes available in the 4.10.4 release, you can update your cluster to that version.

    This channel is suited for production environments, as the releases in that channel are tested by Red Hat SREs and support services.
- The fast-4.10 channel can deliver 4.10.1 and 4.10.2 updates but not 4.11.1. This channel is also supported by Red Hat and can be applied to production environments.

    Administrators must specifically choose a different minor version channel, such as fast-4.11, in order to upgrade to a new release in a new minor version.
- The candidate-4.10 channel allows you to install the latest features of OpenShift. With this channel, you can upgrade to all z-stream releases, such as 4.10.1, 4.10.2, 4.10.3, and so on.

    You use this channel to have access to the latest features of the product as they get released. This channel is suited for development and pre-production environments.

The stable and fast channels are classified as General Availability (GA), whereas the candidate channel (release candidate channel) is not supported by Red Hat.

To ensure the stability of the cluster and the proper level of support, we should only switch from a stable channel to a fast channel, and vice versa. Although it is possible to switch from a stable channel or fast channel to a candidate channel, it is not recommended. The candidate channel is best suited for testing feature acceptance and assisting in qualifying the next version of OpenShift Container Platform.

## Changing Update Channel

We can change the update channel to stable-4.10, fast-4.10, or candidate-4.10 using the web console or the OpenShift CLI client:

- In the web console, navigate to the `Administration → Cluster Settings` page on the details tab, and then click the pencil icon.

![Updating Channel](./images/updates-channel-1.png)

A window displays options to select an update channel.

![Updating Channel](./images/updates-channel-2.png)

- Execute the following command to switch to another update channel using the oc client.

```yaml
oc adm upgrade channel <channel>
```

For example, to set the channel to stable-4.12:

```yaml
oc adm upgrade channel fast-4.10
```
