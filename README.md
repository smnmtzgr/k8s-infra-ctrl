# k8s-infra

# prereq
```
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>
export GITHUB_REPO=<repository-name>
```

# steps for new cluster
```
mkdir clusters/<stage>/<cluster_name>
<create only kustomization.yaml or also patches if needed>

flux check --pre

flux bootstrap github \
    --context=<kubectl context of the cluster> \
    --owner=${GITHUB_USER} \
    --repository=${GITHUB_REPO} \
    --branch=main \
    --personal \
    --path=clusters/<clustername>

watch flux get helmreleases --all-namespaces
```

# commands
```
flux reconcile kustomization infrastructure --with-source
flux get kustomizations [-A]
flux get helmreleases
k get helmreleases.helm.toolkit.fluxcd.io
k get helmreleases.helm.toolkit.fluxcd.io --all-namespaces
```

# links
* add monitoring: https://toolkit.fluxcd.io/guides/monitoring/
* add notifications: https://toolkit.fluxcd.io/guides/notifications/

# howto define/overwrite versions/values per stage or per cluster
* versions/values defined in infrastructure/base folder: used/valid for all kind of clusters independent of the cluster stage
* versions/values defined in infrastructure/<stage>/patches (e.g. infrastructure/dev/patches) folder: used/valid for all kind of clusters with stage=dev (overwrites base versions/values)
* versions/values defined in clusters/<stage>/<cluster_name>/patches (e.g. clusters/dev/customer1/patches) folder: used/valid only for the cluster customer1 (overwrites base and stage versions/values)
