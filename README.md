# Vault Agent Injector Example

This demo requires `Helm V3` and `jq` to be installed.

[Script: Jason O'Donnell - Vault & Kubernetes: Better Together](https://www.hashicorp.com/resources/vault-and-kubernetes-better-together)

## Demo

Run the setup script that installs:

* Vault
* Vault Agent Injector
* CSI Secret Store
* Vault CSI Provider
* PostgreSQL (for example)

:warning: kubectl needs to be available for this (for example Minikube `minikube start --driver=docker` or `minikube start --driver-virtualbox`)
```bash

./setup.sh
```

Vault will automatically [init, unseal, load auth methods, load policies and setup roles](https://github.com/jasonodonnell/vault-agent-demo/blob/hashiconf/configs/bootstrap.sh).

To get the root token or unseal keys for Vault, look in the `/tmp` directory in the `vault-0` pod.

```
kubectl exec --stdin=true --tty=true vault-0 -n vault -- /bin/sh

cat /tmp/outputs
```

To access the Vault GUI you have to port-forward 8200. This is best done in a new Terminal Window.   

```
kubectl port-forward vault-0 -n vault 8200:8200
```

## Namespaces

The demo is running in three different namespaces: `vault`, `postgres` and `app`.

```bash
kubectl get pods -n vault

kubectl get pods -n postgres

# App won't have pods running into the examples are started
kubectl get pods -n app
```

## Static Secret Demo:

```bash
cd ./examples/static-secrets
./run.sh
```

Observe no secrets/sidecars on the app pod:

```bash
kubectl describe pod <name of pod> -n app

kubectl exec -ti <name of app pod> -n app -c app -- ls /vault/secrets
```

Patch the app:

```bash
./patch.sh
```

Observe the secrets at:

```bash
kubectl describe pod <name of pod> -n app

kubectl exec -ti <name of app pod> -n app -c app -- ls /vault/secrets
```

Port forward and open the webpage:

```bash
kubectl port-forward <name of app pod> -n app 8080:8080

open http://127.0.0.1:8080
```

## Dynamic Secret Demo:

```bash
cd ./examples/dynamic-secrets
./run.sh
```

Observe no secrets/sidecars on the app pod:

```bash
kubectl describe pod <name of pod> -n app

kubectl exec -ti <name of app pod> -n app -c app -- ls /vault/secrets
```

Patch the app:

```bash
./patch.sh
```

Observe the secrets at:

```bash
kubectl describe pod <name of pod> -n app

kubectl exec -ti <name of app pod> -n app -c app -- ls /vault/secrets
```

Port forward and open the webpage:

```bash
kubectl port-forward <name of app pod> -n app 8080:8080

open http://127.0.0.1:8080
```

## Transit Demo:

```bash
cd ./examples/transit
./run.sh
```

Patch the app:

```bash
./patch.sh
```

Observe the secrets at:

```bash
kubectl describe pod <name of pod> -n app

kubectl exec -ti <name of app pod> -n app -c app -- ls /vault/secrets
```

Port forward and open the webpage:

```bash
kubectl port-forward <name of app pod> -n app 8080:8080

open http://127.0.0.1:8080
```
