<h1>Projected Volumes</h1>
 
* Projected Volumes is a process that allows you to map several existing volume sources into one singular directory
* The following volume sources are currently supported
  - [secret](https://eoyebami.github.io/k8s/2024-01-15-secrets.html)
  - [downwardAPI]()
  - [configMap](https://eoyebami.github.io/k8s/2024-01-14-configmaps.html)
  - [serviceAccountToken](https://eoyebami.github.io/k8s/2024-01-29-service-accounts.html)

```yml
spec:
  serviceAccount: jenkins
  serviceAccountName: jenkins
  containers: 
  - name: nginx
    image: nginx
    resources:
      requests:
        memory: 1Gi
        cpu: 500m
    volumeMounts:
    - name: all-for-one
      mountPath: /projected-volume
    - name: jenkins-token
      mountPath: /var/run/secrets/tokens
  volumes:
  - name: jenkins-token # the token for the serviceAccount will be automounted and rotated at expiration by Token Request API
       projected:
         sources:
         - serviceAccountToken:
             path: jenkins-token
             expirationSeconds: 7200
  - name: all-for-one
    projected:
      sources:
      - secret:
          name: mysecret # secret object name
          items:
          - key: username
            path: secrets/username # determines the path where the data will be located at the mountPath of the pod
      - downwardAPI:
          items:
          - path: labels # determines the path where the data will be located at the mountPath of the pod
            fieldRef:
              fieldPath: metadata.labels
          - path: cpu_requests # determines the path where the data will be located at the mountPath of the pod
            resourceFieldRef:
              containerName: nginx
                resource: requests.cpu
      - configMap:
          name: myconfigmap
          items:
          - key: config
            path: configmap/config # determines the path where the data will be located at the mountPath of the pod
```

