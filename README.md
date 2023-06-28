# Kubernetes Helm Charts for [huggingface/text-generation-inference](https://github.com/huggingface/text-generation-inference)

[![License](https://img.shields.io/badge/License-APACHE-blue.svg)](https://opensource.org/licenses/APACHE) ![Release Charts](https://github.com/louis030195/text-generation-inference-helm/workflows/Release%20Charts/badge.svg?branch=main)

This functionality is in beta and is subject to change. The code is provided as-is with no warranties.

## Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add the repo as follows:

```bash
helm repo add louis030195 https://louis030195.github.io/text-generation-inference-helm
```

```bash
PROJECT_ID="<your gcp project>"
# create a cluster
gcloud container --project "$PROJECT_ID" clusters create-auto "autopilot-cluster-1" --region "us-central1" --release-channel "regular" --network "projects/$PROJECT_ID/global/networks/default" --subnetwork "projects/$PROJECT_ID/regions/us-central1/subnetworks/default" --cluster-ipv4-cidr "/17" --services-ipv4-cidr "/22"

# get the kubeconfig
gcloud container clusters get-credentials autopilot-cluster-1 --region us-central1

# create a global static ip
gcloud compute addresses create text-generation-inference-ip --global

# create a A record in your domain pointing to the ip returned in this command
gcloud compute addresses describe text-generation-inference-ip --global

# deploy a model (default to bigscience/bloomz-7b1 on a100 gpu)
helm install text-generation-inference louis030195/text-generation-inference-helm --set ingress.host=your.domain.com

# query the model
curl https://your.domain.com/bloomz-7b1 \
    -X POST \
    -d '{"inputs":"What is Deep Learning?","parameters":{"max_new_tokens":17}}' \
    -H 'Content-Type: application/json'
```


### Clean up

```bash
gcloud container clusters delete autopilot-cluster-1 --region us-central1
gcloud compute addresses delete text-generation-inference-ip --global
```

## Limitations

- Currently does not support multi-model under the same domain due to the limitation of the GKE ingress controller (does not support URL rewrite), can probably fix this with some hack.

## Contributing

We'd love to have you contribute!

## License

<!-- Keep full URL links to repo files because this README syncs from main to gh-pages.  -->
[MIT License](https://github.com/louis030195/text-generation-inference-helm/blob/main/LICENSE).

## Helm charts build status

![Release Charts](https://github.com/louis030195/text-generation-inference-helm/workflows/Release%20Charts/badge.svg?branch=main)
