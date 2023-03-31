# External downtime alerting

Stakater App Agility Platform provides downtime notifications for Applications via [IngressMonitorController](https://github.com/stakater/IngressMonitorController) which out of the box integrates with [UptimeRobot](https://uptimerobot.com) and many other services. For this guide we will configure a Slack channel for receiving the alerts; but you can configure any medium supported by the service (email, PagerDuty, etc.).

To configure downtime alerting do following:

1. Configure incoming webhook in Slack
1. Create alert contact on UptimeRobot with webhook
1. Update IMC configuration
1. Enable EndpointMonitor in the application
1. Validate downtime notification

## 1. Configuring incoming webhook in Slack

- While in your Slack workspace, left-click the name of your workspace, and pick `Administration` > `Manage Apps` from the dropdown Menu.
- A new browser window should appear in which you can customize your workspace. From here, navigate to `Custom Integrations` and then to `Incoming WebHooks`.
- Now you can configure a new `Incoming WebHook` by clicking the big, green `Add Configuration button`.
- The first form lets you pick any existing channel or user on your workspace to notify when the WebHook is called. A new channel can also be created here.
- After picking a channel or user to be notified, click the `Add Incoming WebHooks Integration` Button. The most important part on the next screen is the `WebHook URL`. Make sure you copy this URL and send it to Stakater Support
- Near the bottom of this page, you may further customize the Incoming WebHook you just created. Give it a name, description and perhaps a custom icon.

### Items to be provided to Stakater Support

- `Incoming WebHook URL`

## 2. Create alert contact on UptimeRobot with webhook

Create alert contact on UptimeRobot

## 3. Update IMC configuration

Update IngressMonitorController configuration

## 4. Enable EndpointMonitor in the application

Stakater Helm application chart supports [`endpointMonitor`](https://github.com/stakater-charts/application/blob/master/application/values.yaml#L465-L475); just enable it i.e.

```yaml
endpointMonitor:
  enabled: true
```

## 5. Validate downtime notification

Reduce replicas to zero; and you should receive downtime notification!

```yaml
  deployment:
    replicas: 0
```
