## Local Setup

```bash
minikube start

kubectl apply -f k8s/keycloak-pv.yaml

helm install keycloak \
  --values k8s/keycloak-values.yaml \
  codecentric/keycloak --wait

helm install --name kafka incubator/kafka --wait

helm install --name kafka-schema-registry \
  --set kafka.enabled="false" \
  --set kafkaStore.overrideBootstrapServers="PLAINTEXT://kafka-headless:9092" \
  incubator/schema-registry --wait
```

DONT USE "schema-registry" AS RELEASE NAME!!!!

### Setup Keycloak

* Create realm `doit`
* Create confidential client `job-api`
* Create `vendor` role and group
* Create sample vendor user
* Add `http://localhost:8080` as web origin to client settings

### Check events

```
kubectl exec -it kafka-schema-registry-84b9b84dcf-c5nkm -- sh -c "JMX_PORT=6767 kafka-avro-console-consumer --bootstrap-server kafka-headless:9092 --topic item-created-log --from-beginning"
```