# Event-driven Architecture Day 2022


## case 1: scale with hpa

install operator redhat amq streams [Red Hat Integration - AMQ Streams 2.2.x only]
install Quarkus CLI [Ref: https://quarkus.io/guides/cli-tooling]

create kafka cluster "my-cluster"

```bash
cd quarkus-eda-demo
oc new-project kafka-hpa
oc apply -f kube/04-kafka-cluster.yaml
oc apply -f kube/01-kafka-topic.yaml
quarkus build --no-tests
oc autoscale dc/quarkus-eda-demo --min=1 --max=10 --cpu-percent=75
oc apply -f kube/99-load-job.yaml
```

## case 2: scale with keda

install operator keda, create keda controller

create kafka cluster "my-cluster"

```bash
cd quarkus-eda-demo
oc new-project kafka-keda
oc apply -f kube/05-kafka-cluster-keda.yaml
oc apply -f kube/01-kafka-topic.yaml
oc apply -f kube/02-keda-deployment.yaml
oc apply -f kube/03-keda-scalers.yaml
oc apply -f kube/99-load-job.yaml
```

## case 3: knative with kafka

install operator serverless, 

project knative-serving, create knative serving

project knative-eventing, create knative eventing

```bash
cd quarkus-eda-knative-demo
oc new-project kafka-knative
oc apply -f kube/06-kafka-cluster-knative.yaml
oc apply -f kube/07-kafka-topic.yaml
oc apply -f kube/02-knativekafka.yaml
quarkus build --no-tests
oc apply -f kube/03-kafkasource.yaml
oc apply -f kube/99-load-job.yaml
```

## case 4: keda & knative integration with kafka

enable Knative configurations in application.properties

change quarkus.container-image.group=kafka-knative to kafka-keda-knative

```bash
oc new-project kafka-keda-knative
oc apply -f kube/08-kafka-cluster-knative-keda.yaml
oc apply -f kube/07-kafka-topic.yaml
quarkus build --no-tests
oc apply -f kube/05-kafkasource-keda-knative.yaml
oc apply -f kube/99-load-job.yaml
```
