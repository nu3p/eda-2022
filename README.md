# Event-driven Architecture Day 2022

## Install KEDA v2

Deploying with Operator Hub - https://keda.sh/docs/2.6/deploy/#operatorhub

## knative-evnenting namespace

export KO_DOCKER_REPO=YOUR_CONTAINER_REGISTRY

For example, `export KO_DOCKER_REPO=quay.io/danieloh30`

ko apply -f quarkus-eda-knative-demo/kube/01-autoscaler-keda.yaml

## case 1: scale with hpa
oc new-project kafka-hpa
install operator redhat amq streams
create kafka cluster "my-cluster"
oc apply -f kube/04-kafka-cluster.yaml
cd quarkus-eda-demo
oc apply -f kube/01-kafka-topic.yaml
quarkus build --no-tests
oc autoscale dc/quarkus-eda-demo --min=1 --max=10 --cpu-percent=75
oc apply -f kube/99-load-job.yaml

## case 2: scale with keda
oc new-project kafka-keda
install operator keda, create keda controller
create kafka cluster "my-cluster"
oc apply -f kube/05-kafka-cluster-keda.yaml
cd quarkus-eda-demo
oc apply -f kube/01-kafka-topic.yaml
oc apply -f kube/02-keda-deployment.yaml
oc apply -f kube/03-keda-scalers.yaml

## case 3: knative with kafka
install operator serverless, 
project knative-serving, create knative serving
project knative-eventing, create knative eventing
cd quarkus-eda-knative-demo
oc new-project kafka-knative
oc apply -f kube/06-kafka-cluster-knative.yaml
oc apply -f kube/07-kafka-topic.yaml
oc apply -f kube/02-knativekafka.yaml
quarkus build --no-tests
oc apply -f kube/03-kafkasource.yaml
oc apply -f kube/99-load-job.yaml

## case 4: keda & knative integration with kafka
oc new-project kafka-keda-knative
oc apply -f kube/08-kafka-cluster-knative-keda.yaml
oc apply -f kube/07-kafka-topic.yaml
enable Knative configurations in application.properties
change quarkus.container-image.group=kafka-knative to kafka-keda-knative
quarkus build --no-tests
oc apply -f kube/05-kafkasource-keda-knative.yaml
oc apply -f kube/99-load-job.yaml
