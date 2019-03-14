
# Installation

* developed with Debian 9.8, Kubernetes 1.13.3 (CNI [0.3.1](https://github.com/containernetworking/cni/blob/spec-v0.3.1/SPEC.md), CRI [v1alpha](https://github.com/kubernetes/kubernetes/blob/release-1.13/pkg/kubelet/apis/cri/runtime/v1alpha2/api.proto)), Python 3.5.3, systemd 232
* choose `/path` as suitable

```
# apt-get install python3-dev systemd-dev
# cd /path
# git clone https://github.com/tiptop-labs/systemd-shim.git
# cd systemd-shim
# python3 -m venv venv
# python3 -m pip install grpcio
# python3 -m pip install grpcio-tools
# python3 -m pip install systemd
# python3 -m pip install semver
# cat >/etc/systemd/system/systemd-shim.service <<EOF
[Service]
# Environment=GRPC_TRACE=all
# Environment=GRPC_VERBOSITY=DEBUG
ExecStart=/path/systemd-shim/venv/bin/python3 /path/systemd-shim/systemd-shim
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=10
[Install]
WantedBy=multi-user.target
EOF
# cat >/etc/systemd/system/kubelet.service.d/20-systemd-shim.conf <<EOF
[Service]
ExecStart=
ExecStart=/usr/bin/kubelet --container-runtime-endpoint=unix://var/run/systemd-shim.sock --container-runtime=remote $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS
EOF
# systemctl daemon-reload
# systemctl start systemd-shim
# systemctl restart kubelet
```
