# splunk-operator-ocp

---

Splunk Enterprise Standalone installation on OCP via Splunk Operator

* Download the splunk operator YAML

`wget -O splunk-operator-cluster.yaml https://github.com/splunk/splunk-operator/releases/download/2.5.2/splunk-operator-cluster.yaml`

* Deploy the operator

`oc create -f splunk-operator-cluster.yaml`


* Allow the SA for the Splunk Operator to use the 'nonroot-v2' and 'anyuid' SCC

`oc -n splunk-operator adm policy add-scc-to-user nonroot-v2 -z splunk-operator-controller-manager`

`oc -n splunk-operator adm policy add-scc-to-user anyuid -z splunk-operator-controller-manager`

* wait until the operator is running

* Deploy a standalone instance

`oc create namespace splunk`

`oc -n splunk adm policy add-scc-to-user nonroot-v2 -z default`

`oc -n splunk create -f enterprisesplunk.yaml`

* expose the Splunk web UI

`oc -n splunk expose svc splunk-s1-standalone-service`

* get the admin password for the Splunk web UI

`oc -n splunk get secret splunk-splunk-secret -o json | jq -r '.data.password' | base64 -d`

### Local Cluster Connection
* To connect ACS to Splunk where both ACS Central and Splunk are in the same OpenShift cluster, you'll need to take two steps:
* Log in to the Splunk UI, and navigate to the Data Inputs in the top right. Find the HTTP Event Collector (HEC). You'll need the token value from the HEC page.
* In ACS, you'll need to create an integration for Splunk from the Platform Configuration -> Integrations menu. You'll need the HTTP Event collector token. For the endpoint, use the HTTP Event Collector destination, and note that the port (:8088)  is required:

`https://splunk-s1-standalone-service.splunk.svc:8088/services/collector/event`

### Remote Cluster Connection
* To connect ACS Central to Splunk running in a different OpenShift cluster, you'll need to expose Splunk using a Route
* An example Route is provided in the file hec-route.yaml

`oc -n splunk create -f hec-route.yaml` to create the Route

* To create an integration in ACS, you'll need the Splunk HEC, and the hostname from the Route:

`oc -n splunk get route splunk-hec` to see the Route hostname which should have the form:

`https://splunk-hec-splunk.apps.cluster.example.com`

* Note that in the ACS integration you do *not* need the port number since the Route is listening on port 443.
