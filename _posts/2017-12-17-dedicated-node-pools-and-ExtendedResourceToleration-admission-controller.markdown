---
layout: post
title: "Dedicated node-pools and ExtendedResourceToleration admission controller"
date: 2017-12-17 11:43
comments: true
tags: kubernetes gpu
description: "This post describes the use case for the ExtendedResourceToleration admission controller in Kubernetes."
---

### The problem

Let's say you have a [Kubernetes](https://kubernetes.io/) cluster and you added
nodes containing GPUs to the cluster. Since Kubernetes supports scheduling pods
that request GPUs, your data scientists can run ML workloads that need GPUs on
your kubernetes cluster.  Great!

But you have a problem, sometimes your data scientists come to you and say that
their pods requesting GPUs are not getting scheduled. You debug and find out
that some other pods (that didn't request any GPUs) are scheduled on the nodes
containing GPUs. So, even though all the GPUs are unused, there's not enough CPU
or memory for the pods requesting GPUs and so they are not getting scheduled.
You somehow evict the non-GPU requesting pods from the nodes containing GPUs and
pacify your data scientists.

You have another problem, sometimes there are not enough workloads requiring
GPUs running on the cluster. Since GPU nodes are the most expensive nodes in
your cluster, you want the [cluster
autoscaler](https://github.com/kubernetes/autoscaler/tree/cluster-autoscaler-1.1.0/cluster-autoscaler)
to aggressively downscale these nodes in such a scenario. But that doesn't seem
to happen. You debug and find out the reason for this is the same, some non-GPU
requesting pods are scheduled on these nodes. You want a permanent automated
solution to this problem.

### The solution

[Taints is the Kubernetes concept to create dedicated
nodes.](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/#example-use-cases)
Taints allow a node to repel a set of pods. Taints work together with
tolerations to ensure that pods are not scheduled onto inappropriate nodes.
Taints are applied to a node, and mark that the node should not accept any pods
that do not tolerate those taints. Tolerations are applied to pods, and allow
the pods to schedule onto nodes with matching taints.

So, you decide to taint the nodes containing GPUs and ask your data scientists
to modify their GPU pod specs to tolerate that taint. All good? Maybe not.
Asking your data scientists to modify their pod specs doesn't feel like very
user friendly. You fear the inevitable situations where someone forgets to apply
these tolerations or uses an off-the-shelf manifest that doesn't have these
tolerations.

[ExtendedResourceToleration](https://github.com/kubernetes/kubernetes/pull/55839)
is a new admission controller added in Kubernetes 1.9 that is designed to solve
this exact problem.

If you, as a cluster operator or a cloud provider, want to create dedicated node
pools, you are expected to taint the nodes containing [extended
resources](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#extended-resources)
(like NVIDIA GPUs) with a key equal to the name of the extended resource (like
`nvidia.com/gpu`) and effect equal to `NoSchedule`. If you do that, only pods
that have a toleration for such a taint can be scheduled on those nodes. To
avoid asking your users to modify their pod specs, you can enable the
`ExtendedResourceToleration` admission controller. Then, if your users create a
pod that requests extended resources (like `nvidia.com/gpu`), the admission
controller will automatically add a toleration with key equal to the name of the
extended resouce (like `nvidia.com/gpu`), operator `Exists` and effect
`NoSchedule` to the pod.  Because this is happening automatically, this would be
invisbile to your users.  Their existing pod specs will keep on working. Pods
requesting GPUs would not get blocked by non-GPU requesting pods from getting
scheduled on GPU nodes.  When there are not enough GPU workloads, the cluster
autoscaler will downscale GPU nodes (because nothing would be running on them).
Everyone will be happy!
