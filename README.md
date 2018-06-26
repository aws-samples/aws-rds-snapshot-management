# aws-rds-snapshot-management
A serverless notification mechanism to manage Amazon RDS manual snapshots for both RDS Instances and Aurora Clusters. The solution manages creatiton of manual snapshot, deletion of old snapshots, and finally send a notification email. Email subscribers are notified with a list of newly created manual snapshots and older deleted snapshots.
