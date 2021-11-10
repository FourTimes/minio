#### minio

```bash
# installation of minio
MINIOUSER=minioadmin
MINIOPASSWORD=minioadmin

kubectl create ns minio-system
helm repo add minio https://helm.min.io/
helm upgrade --install minio minio/minio \
    --set accessKey=${MINIOUSER} \
    --set secretKey=${MINIOPASSWORD} \
    --namespace minio-system

```


ingress rule for istio

```yml
# ingres.yml
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: minio
  namespace: minio-system
spec:
  gateways:
    - istio-system/seldon-gateway
  hosts:
    - '*'
  http:
    - match:
        - uri:
            prefix: /minio/
      route:
        - destination:
            host: minio
            port:
              number: 9000
```

```bash
kubectl apply -f ingress.yml

# port-forward
kubectl port-forward -n minio-system svc/minio 9000:9000
```
