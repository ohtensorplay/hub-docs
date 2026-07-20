# Organization Storage Regions

Organization Storage Regions let Team and Enterprise administrators choose the
default location for new model, dataset, and Space repositories. The chosen
region helps an organization meet its data-residency and workload-planning
needs while keeping the normal Hub repository workflow.

## Before you begin

Only organization administrators can change these defaults. Open
**Organization Settings → Storage Regions** and confirm that the organization
has an eligible plan. The settings page shows the regions currently available
for that organization.

## Set defaults for new repositories

Choose a default region separately for models, datasets, and Spaces, then save
the policy. Members creating a new repository of that type use the selected
default.

Changing a default does not move existing repositories or their files. Review
an existing repository's location in the audit list and plan a separate,
explicit migration when a location change is required.

## Audit existing repositories

The same page lists the storage region for each organization model, dataset,
and Space. Use the list to review the current distribution before changing a
creation policy or planning new workloads.

For Spaces, the selected repository region is also the region used by its
runtime. Available hardware and compute options can vary by region, so confirm
the choices shown in Space settings before publishing.

## Work safely

- Decide the data-residency policy before creating a sensitive repository.
- Use a repository tag or commit to identify the release involved in a
  migration.
- Keep two organization administrators who can review or correct the policy.
- Do not treat a region default as a replacement for repository visibility,
  membership, or [Resource Groups](/docs/hub/resource-groups).

See [Organizations](/docs/hub/organizations) for roles and plan eligibility,
[Storage Buckets](/docs/hub/storage-buckets) for mutable artifacts, and
[Xet](/docs/xet/index) for large versioned repository artifacts.
