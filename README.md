# ♦️ Premiere Protocol API

**The setup below works and site is up in beta, but project is currently unmaintained**

## Usage

[Docs](https://api.premiere.sh/docs) (OpenAPI format)

## Business Logic

<table>
  <tbody>
    <tr>
      <td>The ability to set up an account using an email</td>
      <td>✅</td>
    </tr>
    <tr>
      <td>Tournament CRUD</td>
      <td>✅</td>
    </tr>
    <tr>
      <td>The ability to deposit fiat through Stripe or Crypto</td>
      <td></td>
    </tr>
    <tr>
      <td>
        Take credit off users accounts on joining tounaments, pay out winnings
      </td>
      <td></td>
    </tr>
    <tr>
      <td>
        Send out notifications to users when tournaments are created with unique
        code to join
      </td>
      <td></td>
    </tr>
    <tr>
      <td>
        Most popular tournament on stream on the home page of each game
      </td>
      <td></td>
    </tr>
    <tr>
      <td>Ability to withdraw</td>
      <td></td>
    </tr>
  </tbody>
</table>

## Stack

FastAPI with PostgreSQL database, with user authentication and endpoints for
CRUD operations on tournaments and games. Running on Terraform-provisioned
Linode Kubernetes Engine cluster with NGINX node balancing with TLS.

## Deployment

### Networking

After provisioning with terraform (requires the `terraform.tfvars` file) and
getting the `kubeconfig.yml`, let's expose the cluster with an ingress:

```sh
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

Then get the ipv4 of the ingress and point the domain (in this case
`api.premiere.sh`) to it. Note that the hostname has to be included in the
`manifest.yml`. Next, get cert:

```sh
kubectl apply -f \
  https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.yaml
```

This should leave to `api.premiere.sh` being accessible both via `HTTP/HTTPS`
and returning 503 status from nginx.

### Services

Having exposed the cluster, deploy the resorces.

`.env` file contents:

```sh
POSTGRES_USERNAME=***
POSTGRES_PASSWORD=***
POSTGRES_HOST=***
```

```bash
kubectl create secret generic premiere-secrets --from-env-file=./.env
```

```sh
kubectl apply -f manifest.yaml
```
