!SLIDE[bg=_images/backgrounds/white_bg.png]

# How We Run this in "Production"

* Runs on the IBM Container Service, but can easily be shipped to GKE or your
  managed Kubernetes cluster platform of choice.
* New images are built and assigned a UUID based on the hash of the latest
  git commit to the repo (just a docker tag).
* Secrets for accessing the twitch API are stored in a configMap to keep
  configuration artifacts decoupled from image builds.
* TLS with letsencrypt handled by kube-lego to get us https.
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
    kubelego:
      legoEmail: mctaylor@us.ibm.com
      legoUrl: https://acme-v01.api.letsencrypt.org/directory


!SLIDE[bg=_images/backgrounds/white_bg.png] commandline incremental

# Edit values.yaml and hit deploy!

    $ helm install ~/src/rotisserie/helm/rotisserie
    $ kubectl get pods
      NAME                                                   READY     STATUS    RESTARTS   AGE
      flabby-opossum-rotisserie-app-5697497954-77wz4         1/1       Running   0          3d
      flabby-opossum-rotisserie-kube-lego-66447f4bf5-9zdmd   1/1       Running   0          3d
      flabby-opossum-rotisserie-nginx-78798955c-d8wm2        1/1       Running   0          3d
      flabby-opossum-rotisserie-ocr-5b58c59fb4-t898x         1/1       Running   0          3d
      flabby-opossum-rotisserie-static-66f4bc95db-rg4rq      1/1       Running   0          3d
