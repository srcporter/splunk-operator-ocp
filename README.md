# splunk-operator-ocp

---

Splunk Enterprise Standalone installation on OCP via Splunk Operator

* Download the splunk operator YAML

`wget -O splunk-operator-install.yaml https://github.com/splunk/splunk-operator/releases/download/1.1.0/splunk-operator-install.yaml`

* Deploy the operator

`oc create -f splunk-operator-install.yaml`

* Allow the SA for the Splunk Operator to use the 'nonroot' SCC

`oc -n splunk-operator adm policy add-scc-to-user nonroot -z splunk-operator-controller-manager`

* wait until the operator is running

* Deploy a standalone instance

`oc create namespace splunk`

`oc -n splunk adm policy add-scc-to-user nonroot -z default`

`oc -n splunk create -f enterprisesplunk.yaml`

* expose the Splunk web UI

`oc -n splunk expose svc splunk-s1-standalone-service`

* get the admin password for the Splunk web UI

`oc -n splunk get secret splunk-splunk-secret -o json | jq -r '.data.password' | base64 -d`

