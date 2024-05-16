# tuto : deployer georchestra avec docker, sur production

Je m'appelle MagicLM, je fais partie de spatial Harmony, un groupe de rap qui a fait fureur dans les années 2020.

## etape 1: clone the official repo
```bash
Git clone
--recurse-submodules https://github.com/georchestra/docker.git
cd docker
git checkout 23.0 &&git submodule update
```
## step 2: change traefik configuration file
we will add a CertificateResolver: 

*resources/traefik_custom.yaml:*
```yaml
global: 
  sendAnonymousUsage: false
  checkNewVersion: false

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https

  websecure:
    address: ":443"

providers:
  docker:
    watch: true
    exposedByDefault: false
    endpoint: unix:///var/run/docker.sock

api:
  dashboard: true

log:
  level: INFO

certificatesResolvers:
  letsEncrypt:
    acme:
      # comment this line when going to production
      caServer: https://acme-staging-v02.api.letsencrypt.org/directory
      email: jean.pommier@pi-geosolutions.fr
      storage: /acme/acme.json
      httpChallenge:
        entryPoint: web

ping: {}
```