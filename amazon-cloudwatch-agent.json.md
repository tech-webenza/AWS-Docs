# That said, here’s an example JSON config that pushes memory and disk usage for / folder to CloudWatch:
```
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "root"
  },
  "metrics": {
    "append_dimensions": {
      "InstanceId": "${aws:InstanceId}"
    },
    "metrics_collected": {
      "mem": {
        "measurement": [
          "mem_used_percent"
        ],
        "metrics_collection_interval": 60
      },
      "disk": {
        "measurement": [
          "used_percent"
        ],
        "metrics_collection_interval": 60,
        "resources": [
          "/"
        ]
      }
    }
  }
}
```
# Explanation:

- "metrics_collection_interval": 60 → collects every 60 seconds.

- "mem_used_percent" → pushes memory usage percentage.

- "disk" → "used_percent" → pushes disk usage percentage.

- "resources": ["/"] → monitors root filesystem only (/).

  - You can add more, e.g. ["/", "/home", "/var"].

## Where to Apply the 80% Threshold

- After CloudWatch Agent is running with the above config:

  1. Go to CloudWatch → Alarms → Create alarm.

  2. Select the metric:

    - CWAgent → InstanceId → mem_used_percent

    - CWAgent → InstanceId → disk_used_percent

  3. Set condition:

    - Threshold: Greater than 80% for 5 minutes.
