# k8s-connection-reset
Sharing code to reproduce an issue with Kubernetes

## Requisites
To install the following code you'll need

* A kubernetes cluster.
* `kubectl`.

## Install steps to reproduce the issue

### Deploying with skaffold

If you have [skaffold](https://github.com/GoogleContainerTools/skaffold)
installed.

```bash
$ skaffold run
# or
$ skaffold dev
```

### Deploying with kubectl

```bash
$ kubectl apply -f kube
```

## :warning: Important note :warning:

Some of the clients pod must be scheduled on a different node
(it can be just one different node) to reproduce the issue.

We identified that when everything was scheduled on only one node the
problem was harder to reproduce.

## Identifying the issue

Check on Stackdriver logging the log from the server and client.
on the search bar you can set `text:'reset by peer'` and you'll see the
following errors:

```bash
# Error reported by the server
E  [info] 11#11: *21885 client 10.20.6.249 closed keepalive connection (104: Connection reset by peer)
# Error reported by the client
E  curl: (56) Recv failure: Connection reset by peer
```

Both line should follow each other. If the client and the server are not
reporting at the same time a `Connection reset by peer` you can ignore the
event as it might be something else.

## Troubleshooting

If the clients seem stuck feel free to restart the pods like the following
`kubectl delete pod -l app=client`

## Links

Relates to:
* [docker/libnetwork#1090](https://github.com/docker/libnetwork/issues/1090)
* [kubernetes/kubernetes#74839](https://github.com/kubernetes/kubernetes/issues/74839)
