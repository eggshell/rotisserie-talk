!SLIDE[bg=_images/backgrounds/white_bg.png]

# How We Run this in "Production"

* Runs on the IBM Kubernetes Service, but can easily be shipped to GKE or your
  managed Kubernetes cluster platform of choice.
* New images are built and assigned a UUID based on the hash of the latest
  git commit to the repo (just a docker tag).
* Secrets for accessing the twitch API are stored in a configMap to keep
  configuration artifacts decoupled from image builds.
* TLS with letsencrypt handled by cert-manager to get us https.
* nginx does reverse-proxying.
* Ingress controller handles external access and provides load-balancing.

!SLIDE[bg=_images/backgrounds/white_bg.png]

# helm

* Rotisserie is shipped as software packaged in a Kubernetes chart using helm.
* Helm is the self-labeled Kubernetes package manager.
* Allows us to:
    * Ensure our builds are reproducible.
    * Have multiple deployments of the app in the same Kubernetes namespace.
    * Keep track of our production releases with a helm-generated name.
    * Easily manage our Kubernetes manifest files by only needing to edit one
      file instead of n-number of YAML files related to Kubernetes deployments.

!SLIDE[bg=_images/backgrounds/white_bg.png] code

# values.yaml

    @@@ yaml
    imageRepository: registry.ng.bluemix.net/rotisserie
    imageTag: bac5a0c
    ingress:
      hostnames:
        - someURL.biz
        - www.someURL.biz
    secrets:
      token: YOUR_TWITCH_TOKEN_IN_BASE64
      clientID: YOUR_CLIENTID_IN_BASE_64
    certManager:
      email: mctaylor@us.ibm.com
      url: https://acme-v01.api.letsencrypt.org/directory


!SLIDE[bg=_images/backgrounds/white_bg.png] commandline incremental

# Edit values.yaml and hit deploy!

    $ helm install ~/src/rotisserie/helm/rotisserie
    $ kubectl get pod
    NAME                                               READY     STATUS    RESTARTS   AGE
    fortnite-prte-prod-rotisserie-ocr-6bf88d6c4d-lstfw 1/1       Running   0          50d
    fortnite-prod-rotisserie-static-865d955b78-jsbqw   1/1       Running   0          50d
    logdna-agent-4cqgd                                 1/1       Running   1          34d
    logdna-agent-krmfj                                 1/1       Running   0          34d
    logdna-agent-lj9hx                                 1/1       Running   1          34d
    rotisserie-dev-app-586dc9b775-vtl9v                1/1       Running   5          48d
    rotisserie-dev-ocr-6b58f8d75f-n7jz8                1/1       Running   1          48d
    rotisserie-dev-static-65fb9c4dc-b4ct7              1/1       Running   1          48d
    rotisserie-prod-app-6c7b9984c5-blrbp               1/1       Running   9          55d
    rotisserie-prod-ocr-6bbd96788c-k6hkv               1/1       Running   1          55d
    rotisserie-prod-static-86f6c59779-jqrfc            1/1       Running   1          55d
