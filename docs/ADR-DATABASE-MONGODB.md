# ADR: MongoDB with Community Operator

**Status:** Accepted
**Date:** 2024-04-01

## Context

Need document database for unstructured/flexible schema data.

## Decision

Use MongoDB Community Operator for MongoDB management.

## Rationale

| Option | Pros | Cons |
|--------|------|------|
| Cloud DBaaS | Zero ops | Cost, vendor lock-in |
| **Community Operator** | K8s-native, free | Self-managed | **Selected** |
| Enterprise Operator | More features | License cost |

**Key Decision Factors:**
- Kubernetes-native lifecycle
- 3-node replica set with automatic failover
- No license cost
- Schema flexibility for evolving data models

## Features

| Feature | Support |
|---------|---------|
| Replica set | ✅ 3-node |
| Automatic failover | ✅ |
| Rolling updates | ✅ |
| Authentication | ✅ SCRAM |
| TLS | ✅ cert-manager integration |

## Configuration

```yaml
apiVersion: mongodbcommunity.mongodb.com/v1
kind: MongoDBCommunity
metadata:
  name: <tenant>-mongodb
  namespace: databases
spec:
  members: 3
  type: ReplicaSet
  version: "7.0.5"
  security:
    authentication:
      modes: ["SCRAM"]
  users:
    - name: <tenant>-user
      db: admin
      passwordSecretRef:
        name: <tenant>-mongodb-password
      roles:
        - name: readWrite
          db: <tenant>
```

## Consequences

**Positive:** K8s-native, automatic HA, schema flexibility, no license cost
**Negative:** Self-managed backups, operator learning curve

## Related

- [SPEC-MONGODB-CONFIGURATION](./SPEC-MONGODB-CONFIGURATION.md)
- [BLUEPRINT-MONGODB-CLUSTER](./BLUEPRINT-MONGODB-CLUSTER.md)
