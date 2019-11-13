# Mutate pods with `emptyDir` and `hostPath` with `safe-to-evict`

The Kubernetes cluster autoscaler does not evict pods that use `hostPath` or `emptyDir` volumes. To allow eviction of these pods, the following annotation must be added to the pods:

````yaml
cluster-autoscaler.kubernetes.io/safe-to-evict: true
````

This policy matches and mutates pods with `emptyDir` and `hostPath` volumes, to add the `safe-to-evict` annotation if it is not specified.

## Policy YAML

[add_safe_to_evict_annotation.yaml](best_practices/add_safe-to-evict_annotation.yaml)

````yaml
apiVersion: "kyverno.io/v1alpha1"
kind: "ClusterPolicy"
metadata: 
  name: "annotate-emptydir-hostpath"
spec: 
  rules: 
  - name: "empty-dir-add-safe-to-evict"
    match: 
      resources: 
        kinds: 
        - "Pod"
    mutate: 
      overlay:
        metadata:
          annotations:
            +(cluster-autoscaler.kubernetes.io/safe-to-evict): true
        spec:          
          volumes: 
          - (emptyDir): {}
  - name: "host-path-add-safe-to-evict"
    match: 
      resources: 
        kinds: 
        - "Pod"
    mutate: 
      overlay:
        metadata:
          annotations:
            +(cluster-autoscaler.kubernetes.io/safe-to-evict): true
        spec:          
          volumes: 
          - (hostPath):
              path: "*"

````
