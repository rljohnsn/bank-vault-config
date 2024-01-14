
# What is this repo?

A configuration helm chart to setup Vault within Kubernetes using [Bank Vaults](https://bank-vaults.dev/docs/) Vault Operator. It will render any number of [Vault](https://bank-vaults.dev/docs/operator/reference/#vaultspec) custom resource definitions that the Bank Vault operator will use to install instances of Vault.

# Sample Configuration

The provided examples demonstrate setting up Vault with the following characteristics

* [TLS enabled](/values-test-ssl.yaml#L54-L59) for all traffic
* [Integrated Storage (raft)](/values-test-ssl.yaml#L73-L79)
* Raft cluster [auto discover / joining](/values-test-ssl.yaml#L95-L101) using K8s service discovery
* Vault [Engine](/values-test-ssl.yaml#L30-L35) install
* Vault [Policy](/values-test-ssl.yaml#L15-L20) install
* Vault [Auth method](/values-test-ssl.yaml#L21-L29) install

The chart data structure uses an array of Vault configuration blocks plus a default block. This allows for installing any number of distinct Vault instances.

To see configuration samples / references check the [Bank Vault Operator repo](https://github.com/bank-vaults/vault-operator/tree/main/deploy/examples) 

## [Defaults](/vault-operator-config/values.yaml#L2-L21)

All defaults are overridable in the `vaults` array elements.

```yaml
vaultOperator:
  defaults:
    apiVersion: "vault.banzaicloud.com/v1alpha1"
    bankVaultsImage: ghcr.io/bank-vaults/bank-vaults:latest
# Explicitly set namespace
# or let it pick it up from helm install
#    namespace: "vault"
    size: 1
    version: "1.15.4"
    istioEnabled: false
    serviceAccount: "vault-sa"
    serviceMonitorEnabled: false
    serviceRegistrationEnabled: false
    serviceType: ClusterIP
    statsdDisabled: true
    veleroEnabled: false
    vaultEnvsConfig:
      - name: POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
```

## Array of Vault installs

```yaml
vaultOperator:
  vaults:
    - name: "vault01"
      size: 3
      version: "1.15.4"
      namespace: altvault
      caNamespaces:
        - "*"
    
    - name: "vault02"
      size: 1
      version: "1.14.4"
      namespace: "testvault

```

## Networking

The [Bank Vaults](https://bank-vaults.dev/docs/) operator supports creating a [single ingress](/values-test-ssl.yaml#L11-L35). This chart supports that config in addtion there is the ability to create any number of native K8s [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) objects and or [Istio Virtual Services](https://istio.io/latest/docs/reference/config/networking/virtual-service/) objects.

See [ingress](/values-test-ingress.yaml) and [istio](values-test-istio.yaml) sample files. 

```yaml
vaultOperator:
  networks:
    ingress:
      - name: vault-public
        labels: []
        annotations: []
        spec: {}
      - name: vault-internal
        labels: []
        annotations: []
        spec: {}
    vservice:
      - name: vault-public
        labels: []
        annotations: []
      - name: vault-internal
        labels: []
        annotations: []
        spec: {}
```