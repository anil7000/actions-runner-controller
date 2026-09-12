# Runner scale-set readiness and queue triage

Use this checklist for modern autoscaling runner scale sets. Do not mix their
configuration with the legacy RunnerDeployment autoscaling mode.

## Review the configuration boundary

Compare your configuration with
[scale-set values](charts/gha-runner-scale-set/values.yaml).

| Setting | Review question |
| --- | --- |
| githubConfigUrl | Is this the intended repository, organization or enterprise? |
| githubConfigSecret | Does the referenced authentication configuration match that scope? |
| runnerScaleSetName | Does the workflow target the intended scale set? |
| minRunners and maxRunners | Are idle capacity and maximum capacity intentional? |
| proxy and certificate settings | Can controller, listener and runner reach required endpoints? |
| runner pod template | Are resource requests, mounts and privileges appropriate? |

Keep GitHub tokens and App private keys out of values files committed to Git.
Prefer the documented existing-secret mechanism and restrict access to it.

## Follow a queued job

1. Check workflow runs-on selection and runner-group access.
2. Check controller and listener health in their actual namespaces.
3. Check whether a runner pod was requested.
4. If pending, inspect scheduling events, quotas and resource requests.
5. If starting fails, inspect image-pull, proxy and certificate errors.
6. If online but idle, recheck routing and scale-set configuration.

For an authorized context, use these read-only examples after replacing
arc-runners with the installation's runner namespace:

```sh
kubectl config current-context
kubectl -n arc-runners get pods
kubectl -n arc-runners get events --sort-by=.metadata.creationTimestamp
```

Do not grant privileged execution or mount the host Docker socket simply to
clear a queue. Treat untrusted workflow code as a separate trust boundary.
Keep public pull-request execution away from sensitive self-hosted environments
unless a reviewed isolation and approval design exists.

See [README](README.md) for the modern scale-set documentation links.

## Development note

This operations checklist was added with AI assistance. Upstream code, licenses
and contributor attribution remain unchanged.
