# Reproducing kubernetes issue 87369

## Steps to reproduce

```
kind create cluster --config=cluster.yaml
kubectl get namespaces --kubeconfig=kubeconfig.yaml --token=admin-token
```
Note that `kubeconfig.yaml` includes a (broken) exec credential plugin which should not get called since a token is present in the request and should take precedence.

## Expected behavior

Namespaces are listed as provided token is valid.

## Actual behavior

Exec credential plugin is called to retrieve possible client certifcates.
Note that this is only triggered if the server advertised client certificate auth capabilities in the TLS handshake.

```
$ kubectl get namespaces --kubeconfig=kubeconfig.yaml --token=admin-token
Unable to connect to the server: decoding stdout: couldn't get version/kind; json parse error: json: cannot unmarshal string into Go value of type struct { APIVersion string "json:\"apiVersion,omitempty\""; Kind string "json:\"kind,omitempty\"" }
```