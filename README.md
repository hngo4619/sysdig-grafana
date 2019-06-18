# Collecting Sysdig Metrics Data from IBM Cloud Cluster to Grafana

This guide will show you how to set up Sysdig monitoring in an IBM Cloud Kubernetes Cluster and collect that data into Grafana.

## Prerequisites
- IBM Cloud Kubernetes Cluster
- Sysdig
- Grafana

## Instructions

1. Go to the [Sysdig Monitoring dashboard](https://cloud.ibm.com/observe/monitoring). If you don't have an instance yet, you can provision one from the [IBM Cloud catalog](https://cloud.ibm.com/catalog/services/ibm-cloud-monitoring-with-sysdig).

2. Click the `Edit sources` button corresponding to your Sysdig Monitoring instance that you want to use to collect data. On the next page, look for the section labeled `Install Sysdig Agent to your cluster`. This should provide you with a command (listed below) that you can execute on your cluster to install the Sysdig agent that will collect metrics from each of your pods.

```
curl -sL https://ibm.biz/install-sysdig-k8s-agent | bash -s -- -a ACCESS_KEY -c COLLECTOR_ENDPOINT
```

`ACCESS_KEY` is the ingestion key for the instance. You can find this by clicking the menu button (3 vertical dots) next to your instance on the [Sysdig Monitoring dashboard](https://cloud.ibm.com/observe/monitoring).

`COLLECTOR_ENDPOINT` is the ingestion URL for the region where the monitoring instance is available. For an instance in the us-south, the endpoint is `ingest.us-south.monitoring.cloud.ibm.com`.

For additional information on configuring Sysdig agent on Linux, Docker, or a Kubernetes cluster, see [here](https://cloud.ibm.com/docs/services/Monitoring-with-Sysdig?topic=Sysdig-config_agent).

3. Now that you have Sysdig installed on your cluster, we have to validate that it's collecting metrics data. In the [Sysdig Monitoring dashboard](https://cloud.ibm.com/observe/monitoring), click the `View Sysdig` button for your instance. This will launch your Sysdig instance dashboard. The first view that you see should show the pods in your Kubernetes cluster. This means that everything is working as intended. If not, please see the additional information linked above for troubleshooting.

4. Staying on the Sysdig dashboard, you need to grab your Sysdig Monitor API Token. At the bottom left corner, click the icon that corresponds to your user profile (usually the top icon in the bottom and will have initials corresponding to your name). A menu should open that has a `Settings` button. Click that.

5. The Settings should open up to a `User Profile` page. You should see a `Sysdig Monitor API` section with a Token. Copy the token.

6. Next, we need to install the Sysdig datasource plugin on your Grafana instance. Depending on how you have it installed, follow the instructions [here](https://github.com/draios/grafana-sysdig-datasource#getting-started).

If you do not have Grafana running, you can deploy it on a Kubernetes cluster (the same one that you have Sysdig deployed on is fine or a different one) using Helm with the charts provided in the `helm` directory in this repo. You'll need to build the Docker image and push it to the IBM Cloud container registry (or equivalent). You'll also need to edit some of the values in `values.yaml` that will be specific to your cluster, registry, and image.

An example command on building, tagging, and pushing a docker image to IBM Cloud container registry:

```
docker build -t registry.ng.bluemix.net/getting-native/grafana-sysdig:latest . && docker push registry.ng.bluemix.net/getting-native/grafana-sysdig:latest
```

7. You should have the Sysdig plugin installed on Grafana. Open your Grafana dashboard. Go to `Add Datasource`. In the following view, select `Sysdig`. In the section labeled `Sysdig API Settings`, click the `Plan` dropdown and select `Pro Software`. For the URL textfield that appears, you need to add the regional endpoint for your Sysdig instance. For a Sysdig instance in the us-south region, the endpoint is `https://us-south.monitoring.cloud.ibm.com`.

8. In the `Auth` section, enter the API Token that you received in Step 5 from your Sysdig settings.

9. Click all the `Import` buttons in the `Sysdig Dashboards` section. Once finished, click `Save & Test` at the bottom. You should now have access to all the Sysdig metrics being collected from an IBM Cloud Kubernetes cluster in your Grafana instance.

10. If you go to your Grafana home view, you should be able to see all the `Sysdig` dashboards in your list of available dashboards. They'll be tagged with `Sysdig`, along with other tags detailing what metrics that dashboard is related to. You can also create your own dashboard that contains all the metrics data that you want.

## Conclusion

You should now be able to visualize and view metrics collected from an IBM Cloud Kubernetes cluster from Sysdig in Grafana!

### Helpful links
- [Configuring Sysdig](https://cloud.ibm.com/docs/services/Monitoring-with-Sysdig?topic=Sysdig-config_agent)
- [Github Repo for Sysdig Datasource Plugin](https://github.com/draios/grafana-sysdig-datasource#getting-started)
