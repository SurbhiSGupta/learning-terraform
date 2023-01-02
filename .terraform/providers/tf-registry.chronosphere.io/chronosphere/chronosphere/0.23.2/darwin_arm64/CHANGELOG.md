# Releases

## Unreleased

## 0.23.2

Bug fixes:
 - Fix unexpected `send_resolved` diff for notifiers

## 0.23.1

Bug fixes:
 - Fix `chronotf` crash on startup.

## 0.23.0

Features:
- Support for service accounts using `chronosphere_service_account`
- Support multiple match rules in `chronosphere_resource_pools_config`
- Internal: Support for trace-derived metrics using `chronosphere_trace_metrics_rule` (undocumented)
- Internal: Support for bucketless mapping rules in `chronosphere_mapping_rule`
- Internal: Allow dual-owned monitors (collection and bucket) to not require notification policy.
- Internal: Make bucket optional on trace ingestion rules
- Internal: ExportJob test schema has optional bucket_slug
- Internal: Support Team home dashboard resource using `chronosphere_team_home_dashboard`
- Internal: In UNSTABLE mode, use dry-run to validate monitors
- Internal: In UNSTABLE mode, import-state will transfer ownership (update source client to Terraform)

Bug fixes:
 - Fix `chronotf export-config` and `import-state` to support all
   recording rules and rollup rules.
 - Support notification routes with an empty list of notifiers
   so all policies can correctly be exoprted using `chronotf export-config`.
 - Fix conditional drop rules not allowing rate limit thresholds > 1%.
 - Fix conditional drop rules ratio-percent mismatch
 - Validate usage of `route` and `override`, and flag usage of deprecated `rule`, during the `terraform plan` stage
 - Fix `chronotf import-state` support for handling resources in modules.
 - `chronosphere_mapping_rule` validates that at most 1 aggregation is specified.

## 0.22.0
Changes:
- Rename preview feature `value_based_drop_policy` to `value_based_drop`.

Features:
- Improve formatting of drop rules when exporting config.

## 0.21.0
Features:
- Add `value_based_drop_policy` field to `chronosphere_drop_rule`.

Bug fixes:
- Fix importing of buckets nested in a module.
- Fix `chronotf import-state` re-importing resources that are already managed by Terraform
  due to an unknown/updated field requiring the resource to be re-created.
- Internal: update unstable API and fix breaking changes
- Internal: fix handling of objects with diff suppress in TypeSet
- Internal: use static notification_policy_data sentinel value for independent NPs

## 0.20.0
Features:
- Drop empty fields when exporting email alert notifiers in `chronotf export-config`.
- Internal: Allow recording rules with internal labels.
- Internal: Don't re-create independent notification policies when ownership changes.

## 0.19.1
Bug fixes:
- Internal: update to latest unstable API and fix breaking changes

## 0.19.0
Features:
- Add `permissive` field to `chronosphere_rollup_rule`
- Internal: Update `chronosphere_collection` resource to have optional notification policy.
- Internal: Add a `chronosphere_collection_home_dashboard` resource which allows setting the home dashboard of a collection.

Bug fixes:
- Change `chronosphere_notification_policy.route.notifiers` to a TypeSet to prevent ordering diffs.
- Fix export/import for `chronosphere_notification_policy` repeat intervals

## 0.18.1
Bug fixes:
- Fix bug with monitor conditions showing unnecessary diffs.

## 0.18.0
Features:
- Add support for monitors using a `NOT_EXISTS` condition without a value.
- Show better diffs to schedules in `chronosphere_monitor`.
- Show better diffs for changes to complex objects in a list such as
  monitor schedules, mapping rule storage policies, monitor label matchers.
- Internal: Add `chronosphere_derived_metric` to support derived metrics
- Internal: Add `chronosphere_resource_pools_config` for configuring resource pools.
- Internal: Add support for exporting independent policies in `chronotf export-config`.

Bug fixes:
- Fix buckets showing unnecessary diffs to empty labels.
- Don't update the TF state on Update errors.
- Remove incorrect log when importing notification policies with overrides.

Deprecated:
- `chronosphere_notification_policy.rule` is marked as deprecated, use `route` instead.


## 0.17.0
Features:
- Add support for monitors using an `EXISTS` condition without a value.
- Internal: Allow usage of unstable APIs via UNSTABLE flag.
- Internal: Add `chronosphere_dashboard` resource (only enabled with unstable APIs).
- Internal: Allow monitors to be owned by a collection and a bucket.

Bug fixes:
 - Fix notification policy bugs where changes are reported immediately after an apply.
 - Fix `activated_drop_duration` in drop rules rules showing unexpected changes after an apply.

## 0.16.0
Features:
 - Add `chronosphere_team` to manage teams
 - Add support for slug in all notifiers
 - Add support for slug in data buckets
 - Support slug based matching in `chronotf import-state` for monitors, teams,
   drop rules, maping rules, rollup rules and all notifiers.
 - Make `storage_policies` optional for rollup rules.
 - Support `DELTA` rollup rules without a name.
 - Internal: Add support for `chronosphere_collection` (enabled via
   feature-flag in internal environments)
 - Internal: Allow monitors to be owned by a collection
 - Internal: Allow dashboards to be owned by a collection

Bug fixes:
 - Handle 404 as success when deleting an already-deleted resource.

## 0.15.0

Features:
- Add support for slug in `chronosphere_bucket`.
- Add drop_raw field to recording_rule

## 0.14.1

Bug fixes:
- Fixes an issue creating notifiers with a bearer token

## 0.14.0

Features:
- Add metric_name field to recording_rule
- Support repeat_interval for notification policies

## 0.13.0

Features:
- Add support for trace root span rule
- Ability to detach notification policy lifecycle from buckets in Terraform

## 0.12.0

Features:
- Import and export support for mapping rules.

## 0.11.1

Bug fixes:

- Fixes an issue sending an incorrect representation of a `chronosphere_export_job` on updates.

## 0.11.0

Features:

- New `chronosphere_export_job` namespaces.

## 0.10.0

Features:

- New `chronosphere_mapping_rule` resource.

## 0.9.0

Features:

- Allow selecting a notification policy for a monitor independent of the bucket.
- Support for new stat namespace in `chronosphere_export_job` resource.

Bug fixes:

- Import and export support for buckets without a notification policy.

## 0.8.0

Features:

- New `chronosphere_drop_rule` resource.

Bug fixes:

- Support specifying durations in Prometheus format (support for d/w/y suffixes).
- Fixes panic when org name is incorrectly specified.
- Fixes `chronotf import-state` errors regarding unresolved references with newer Terraform versions

## 0.7.0

Features:

- New `chronosphere_export_job` resource.

## 0.6.0

Bug fixes:

- Fixes an issue where objects deleted outside of Terraform were not handled correctly
- Fixes a bug with marshalling attributes with special characters (like quotes) for `chronotf export-config`
- Fixes invalid TF output when marshalling a pagerduty notifier using a RoutingKey in `chronotf export-config`
- Sanitizes slug names when converting to terraform IDs to avoid invalid terraform output in `chronotf export-config`

Features:
- Support Opsgenie notifiers in `chronotf export-config`


## 0.5.3

Features:

- New `chronosphere_rollup_rule` resource.

## 0.5.2

Bug fixes:

- Fixes a bug with escaping quotes for `chronotf export-config`

## 0.5.1

Features:

- New `chronosphere_recording_rule` resource.

## 0.5.0

Features:

- New `chronosphere_monitor` resource.
- Advanced alert notification routing with `chronosphere_notification_policy` support for routes and route overrides.

## 0.4.1

Bug Fixes:

- On `chronosphere_opsgenie_alert_notifier`, the `type` field within a `responder` is required.

## 0.4.0

Features:

- New `chronosphere_grafana_dashboard` resource for managing dashboards.
- Setting `notification_policy_data` on `chronosphere_bucket` is optional for buckets without alerts.

## 0.3.2

Features:

- Alerts can have schedules.
- Email notifier support.
- Opsgenie notifier support.

## 0.3.1

Bug Fixes:

- Prevent duplicate slug errors when notifiers have long names (36 or more characters).

## 0.3.0

This release cannot be used interchangeably with prior releases. It automatically migrates bucket state in Terraform
state files when `terraform apply` is first run. After this older versions of the provider will not be able to use the
state files.

Features:

- Webhook notifier support.
- Alert resources can have `EQ` and `NEQ` as threshold op values.

Improvements:

- Groundwork supporting renaming notifiers in the future.

## 0.2.4

Features:

- `chronotf export-config` supports new alert notifier fields.

Bug Fixes:

- Fixed case where service key was used in place of routing key for PagerDuty alert notifiers.

## 0.2.3

Features:

- Add support for all configurable options for PagerDuty alert notifiers.
- Add support for all configurable options for Slack alert notifiers.

## 0.2.2

Bug fixes:

- Avoid errors when creating or updating many alerts within a single bucket.

## 0.2.1

Improvements:

- Bucket create fails if bucket exists instead of updating.

Bug fixes:

- Related to bucket creation no longer being an upsert, failure to attempt creating a bucket will no longer delete an
  existing bucket.

## 0.2.0

Features:

- Documentation
- `chronosphere_bucket` data source.
- `chronotf export-config` to generate Terraform from existing resources.
- `chronotf import-state` to import existing resources into Terraform state.

Improvements:

- Better diff for notification policy changes.

## 0.1.0

Features:

- Initial release of the Terraform plugin.
