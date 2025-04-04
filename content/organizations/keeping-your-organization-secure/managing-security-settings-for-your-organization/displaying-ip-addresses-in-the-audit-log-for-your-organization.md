---
title: Displaying IP addresses in the audit log for your organization
intro: You can display the source IP address for events in your organization's audit log.
shortTitle: IP addresses in audit log
permissions: Organization owners can display IP addresses in the audit log for an enterprise.
versions:
  feature: display-ip-org-audit-log
type: how_to
topics:
  - Auditing
  - Organizations
  - Networking
  - Security
---

> [!NOTE]
> Displaying IP addresses in the audit log for an organization is in {% data variables.release-phases.public_preview %} and subject to change.

## About display of IP addresses in the audit log

By default, {% data variables.product.github %} does not display the source IP address for events in your organization's audit log. {% data reusables.audit_log.about-ip-display %} If you enable this setting, the IP address will be displayed for **new and existing events** in the audit log.

You are responsible for meeting any legal obligations that accompany the viewing or storage of IP addresses displayed within your organization's audit log.

{% ifversion enterprise-audit-log-ip-addresses %}

Alternatively, you can configure IP addresses at the enterprise level. For more information, see [Displaying IP addresses in the audit log for your enterprise](/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/displaying-ip-addresses-in-the-audit-log-for-your-enterprise).

{% endif %}

{% data reusables.audit_log.users-agree-to-ip-collection %}

After you enable the feature, you can access the audit log to view events that include IP addresses. For more information, see [AUTOTITLE](/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization).

## Events that display IP addresses in the audit log

{% data variables.product.github %} displays an IP address for each event in the organization audit log that meets these criteria.

* The actor is an organization member or owner
* The target is either an organization-owned repository that is private or internal, or an organization resource that is not a repository, such as a project.

## Enabling display of IP addresses in the audit log

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.audit_log.audit_log_sidebar_for_org_admins %}
{% data reusables.audit_log.enable-ip-disclosure %}
1. Click **Save**.
