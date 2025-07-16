# Kubernetes

Created by Google which was inspired from Google's Borg.

This industry standard Container Orchestration platform.

It has 2 key components:
- Master Nodes (Control Plane)
	- Kube-API Server: Frontend for K8s control plane.
	- Kube-Scheduler: Watches for newly created pods which aren't assigned to any nodes. It also assigns them to nodes.
	- Kube-Controller: Responsible for the state and it's job is to move from current state to desired state.
	- Etcd — Persist (Shared) Storage between the nodes for cluster config.
- Worker Nodes
	- Runtime
	- Kubelet
	- Kube-Proxy

**Pods** — The most basic/smallest deployable unit in K8s. Each pod has 1+ containers.
- Master Nodes find appropriate Worker Nodes for the pod to run on.
- Worker Nodes run the actual containers inside the pod.