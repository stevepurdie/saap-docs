# OAuth User Access

By default, users logged in with OAuth do not have any access. To give them access, you must add them to a group called `admin` that must have the label `team: admin`.

Example

```yaml
kind: Group
apiVersion: user.openshift.io/v1
metadata:
  name: admin
  labels:
    team: admin
users:
  - user1
```
