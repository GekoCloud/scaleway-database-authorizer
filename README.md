# scaleway-database-authorizer

This is a simple micro-service that runs as a DaemonSet and authorizes every new node in a kubernetes cluster in the scaleway elements managed database ACL rules.

## Getting started

### Method 1: ENV variables

You can see an example deamonset in the file "database-authorizer-deamonset.yaml"

You need to configure the following env variables:

    - SCALEWAY_ACCESS_KEY => Your scaleway access key
    - SCALEWAY_SECRET_KEY => Your scaleway secret key
    - SCALEWAY_ORG_ID => Your scaleway org id
    - SCALEWAY_INSTANCE_ID => Your scaleway elements manages database instance id
    - SCALEWAY_DATABASE_REGION => Your scaleway elements manages database region (fr-par/nl-ams)

### Method 2: Kubernetes secret (Recommended)

To use a Kubernetes secrets for credentials first create an ENV file called scaleway-credentials:

```yaml
SCALEWAY_ACCESS_KEY=<your-access-key>
SCALEWAY_SECRET_KEY=<your-secret-key>
SCALEWAY_ORG_ID=<your-org-id>
```

Now create the k8s secret:

```yaml
kubectl create secret generic scaleway-credentials --from-env-file=scaleway_credentials --namespace <your_namespace>
```

And finally create the Daemonset using the file "database-authorizer-deamonset-with-secrets.yaml":

```yaml
kubectl apply -f database-authorizer-deamonset-with-secrets.yaml --namespace <your_namespace>
```

## Troubleshooting

### Log shows "404 HTTP Not found" error

This probably means that your instance ID is wrong. Double-check it.

If you are using Terraform be aware that the output for instance_id will return the id with the region appended in front of it in the form of "fr-par/xxxxxxxxxxxxxxxxxxxxx". You must strip the region for this to work properly (you can use terraform functions like slice).

### Log shows HTTP "403 Forbidden" error

This means that your credentials are wrong. Check your ACCESS_KEY, SECRET_KEY and ORG_ID that you are using in your env vars or in your secret.
