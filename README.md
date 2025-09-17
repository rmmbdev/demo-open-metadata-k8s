# Start Minikube with sufficient resources

```bash
  minikube start --memory=8192 --cpus=2
```

# Load images into Minikube

```bash
  minikube image load focker.ir/elasticsearch/elasticsearch:8.11.4 && minikube image load postgres:17.2-bookworm && minikube image load focker.ir/openmetadata/server:1.9.5 && minikube image load focker.ir/openmetadata/ingestion-base:1.9.5 
```

# Apply Elasticsearch configuration

```bash
  kubectl apply -f elasticsearch.yaml
```

# Apply PostgreSQL configurations

```bash
  kubectl apply -f postgres-openmetadata.yaml && kubectl apply -f postgres-db1.yaml
```
```bash
  kubectl create secret generic postgres-secrets --from-literal=openmetadata-postgres-password=postgres
```

# Add OpenMetadata Helm repository

```bash
  helm repo add open-metadata https://helm.open-metadata.org && helm repo update
```

# Install/upgrade OpenMetadata

```bash
  helm upgrade --install openmetadata open-metadata/openmetadata --values values.yaml --timeout 10m
```

# Port-forward to access OpenMetadata

```bash
  kubectl port-forward svc/openmetadata 8585:8585
```

# Access db1

```bash
  kubectl port-forward svc/postgres-db1-service 54321:5432
```

```bash
  psql -h localhost -p 54321 -U postgres -d db1
```

```sql
CREATE TABLE customers
(
    id    SERIAL PRIMARY KEY,
    name  VARCHAR(100),
    email VARCHAR(100)
);
INSERT INTO customers (name, email)
VALUES ('John Doe', 'john@example.com');
```

```bash
  minikube stop && minikube delete
```


# Run ingestion

replace open-metadata jwt token in ingestion-configmap.yaml

```bash
  kubectl apply -f ingestion-configmap.yaml
```

```bash
  kubectl apply -f ingestion-pod.yaml  
```