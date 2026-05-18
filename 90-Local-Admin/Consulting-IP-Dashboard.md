## Frameworks
```dataview
TABLE status, maturity, last_updated, audience
FROM "07-Frameworks"
SORT last_updated DESC
```

## Playbooks
```dataview
TABLE status, maturity, last_updated, audience
FROM "08-Playbooks"
SORT last_updated DESC
```

## Reference Architectures
```dataview
TABLE status, maturity, last_updated, audience
FROM "09-Reference-Architectures"
SORT last_updated DESC
```

## Executive Assets
```dataview
TABLE status, last_updated, publish, client_safe
FROM "10-Executive"
SORT last_updated DESC
```

## Publish-Ready
```dataview
TABLE artifact_type, domain, last_updated
WHERE publish = true AND status = "publish-ready"
SORT last_updated DESC
```

## Drafts Needing Review
```dataview
TABLE artifact_type, domain, last_updated
WHERE status = "draft" OR status = "review"
SORT last_updated ASC
```