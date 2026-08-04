# Tutor / Open edX Operations

Day-to-day operations for the Open edX (Tutor, Ulmo release) platform running on the customer EC2 instance. Access the instance via the bastion host:

```bash
ssh <bastion-host>
ssh <platform-ec2>
```

## Restart services

```bash
tutor local status                 # overview of all services
tutor local restart lms            # restart LMS only
tutor local restart cms            # restart Studio only
tutor local restart                # restart everything
tutor local stop && tutor local start -d   # full cold restart
```

Restart `lms`/`cms` after configuration or theme changes. Avoid full restarts during business hours.

## View logs

```bash
tutor local logs --tail=200 lms        # LMS
tutor local logs --tail=200 cms        # Studio
tutor local logs --tail=200 caddy      # edge proxy / TLS
tutor local logs -f lms                # follow
docker ps                              # container overview
```

Also check CloudWatch alarms and ALB target health before deep-diving into logs.

## Backup

| Data | Method | Frequency |
|---|---|---|
| MySQL (RDS) | Automated RDS snapshots + periodic `mysqldump` export to S3 | Daily snapshots; weekly dump |
| MongoDB / DocumentDB | DocumentDB cluster snapshots (or `mongodump` for self-hosted Mongo) | Daily |
| Course content | `tutor local export` per course → S3 (in addition to DocumentDB snapshots) | Weekly, and before any upgrade |
| Static assets | S3 versioning enabled on asset buckets; cross-check with `aws s3api get-bucket-versioning` | Continuous |

MySQL dump example:

```bash
mysqldump -h <rds-endpoint> -u <user> -p --all-databases | gzip > backup-$(date +%F).sql.gz
aws s3 cp backup-$(date +%F).sql.gz s3://<backup-bucket>/mysql/
```

Course export:

```bash
tutor local run cms ./manage.py cms export_course <course-id> /tmp/exports/
aws s3 sync /tmp/exports/ s3://<backup-bucket>/courses/
```

**A backup that hasn't been restored doesn't exist**, run a restore test quarterly.

## Restore

MySQL:

```bash
gunzip < backup-YYYY-MM-DD.sql.gz | mysql -h <rds-endpoint> -u <user> -p
```

DocumentDB: restore the cluster snapshot via the AWS console/CLI into a new cluster, then update the connection string in Secrets Manager and restart services.

Course content:

```bash
aws s3 sync s3://<backup-bucket>/courses/ /tmp/restore/
tutor local run cms ./manage.py cms import_course /tmp/restore/<course-dir>
```

## Upgrade procedure

1. **Backup first**: verify a fresh MySQL dump, DocumentDB snapshot, and course exports exist.
2. **Announce** a maintenance window to the client.
3. Upgrade Tutor on the EC2 instance:

```bash
pip install --upgrade "tutor[full]"
tutor config save            # re-render config for the new version
tutor local stop
tutor local start -d
tutor local upgrade --from=<previous-release>   # runs migrations (e.g. from Teak to Ulmo)
```

4. Verify: `/heartbeat`, login, course playback, Studio save, certificates.
5. **Redeploy MFEs** if the release requires it, rerun the three CodeBuild pipelines (auth, app, admin) with the existing customer env files.
6. Monitor CloudWatch for 24h.

### Rollback

- MFEs: redeploy the previous build, each CodeBuild run syncs from a known git ref; re-run the pipeline pinned to the previous release tag.
- Open edX: restore the pre-upgrade RDS snapshot and DocumentDB snapshot, revert Tutor to the previous version (`pip install tutor==<old-version>`, `tutor config save`, restart). Course exports are the last-resort content restore.
- Infrastructure: `terraform plan`/`apply` from the previous state, never destroy to roll back.

Prefer **restore service first, investigate second** (see [Runbooks](runbooks.md)).
