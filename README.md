# Distributed Image Processing in Cloud DataProc



## Set up
1. Make sure the default computer Service Account is present and has the editor role.
2. Create a vm: Computer Engine > VM Instances > Create.
3. Name: devhost; Series: N1; Machine Type: 2vCPUS; Identity and API Access: Allow full access to all Cloud APIs.

## Set up Scala and sbt
* `sudo apt-get install -y dirmngr unzip` 
* `sudo apt-get update`
* `sudo apt-get install -y apt-transport-https`
* `echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list`
* `sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823` 
* `sudo apt-get update`
* `sudo apt-get install -y bc scala sbt`

See each directories README for more information.


## Set up Feature Detector Files and Launch build
* `sudo apt-get update`
* `gsutil cp gs://spls/gsp124/cloud-dataproc.zip .`
* `unzip cloud-dataproc.zip`
* `cd cloud-dataproc/codelabs/opencv-haarcascade`
* `sbt assembly`

## Create a Cloud Storage and collect images
* `GCP_PROJECT=$(gcloud config get-value core/project)`
* `MYBUCKET="${USER//google}-image-${RANDOM}"`
* `echo MYBUCKET=${MYBUCKET}`
* `gsutil mb gs://${MYBUCKET}`
* `curl https://www.publicdomainpictures.net/pictures/20000/velka/family-of-three-871290963799xUk.jpg | gsutil cp - gs://${MYBUCKET}/imgs/family-of-three.jpg
* `curl https://www.publicdomainpictures.net/pictures/10000/velka/african-woman-331287912508yqXc.jpg | gsutil cp - gs://${MYBUCKET}/imgs/african-woman.jpg`
* `curl https://www.publicdomainpictures.net/pictures/10000/velka/296-1246658839vCW7.jpg | gsutil cp - gs://${MYBUCKET}/imgs/classroom.jpg`
* `gsutil ls -R gs://${MYBUCKET}`

## Create a Cloud Dataproc cluster
* `MYCLUSTER="${USER/_/-}-qwiklab"`
* `echo MYCLUSTER=${MYCLUSTER}`
* `gcloud config set dataproc/region us-central1`
* `gcloud dataproc clusters create ${MYCLUSTER} --bucket=${MYBUCKET} --worker-machine-type=n1-standard-2 --master-machine-type=n1-standard-2 --initialization-actions=gs://spls/gsp010/install-libgtk.sh --image-version=2.0 `

## Submit job to Cloud Dataproc
* `curl https://raw.githubusercontent.com/opencv/opencv/master/data/haarcascades/haarcascade_frontalface_default.xml | gsutil cp - gs://${MYBUCKET}/haarcascade_frontalface_default.xml`
`cd ~/cloud-dataproc/codelabs/opencv-haarcascade`
``` 
gcloud dataproc jobs submit spark \
--cluster ${MYCLUSTER} \
--jar target/scala-2.12/feature_detector-assembly-1.0.jar -- \
gs://${MYBUCKET}/haarcascade_frontalface_default.xml \
gs://${MYBUCKET}/imgs/ \
gs://${MYBUCKET}/out/
```

You can find more Dataproc resources in these github repositories:

### Dataproc projects
* [Dataproc initialization
  actions](https://github.com/GoogleCloudPlatform/dataproc-initialization-actions)
* [GCP Token Broker](https://github.com/GoogleCloudPlatform/gcp-token-broker)
* [Dataproc Custom Images](https://github.com/GoogleCloudPlatform/dataproc-custom-images)
* [Dataproc Spawner](https://github.com/GoogleCloudPlatform/dataprocspawner)

### Connectors
* [Hadoop/Spark GCS Connector](https://github.com/GoogleCloudPlatform/bigdata-interop/tree/master/gcs)
* [Spark BigQuery Connector](https://github.com/GoogleCloudPlatform/spark-bigquery-connector)
* [Hadoop BigQuery Connector](https://github.com/GoogleCloudPlatform/bigdata-interop/tree/master/bigquery)
* [Spark Pubsub Connector](https://github.com/GoogleCloudPlatform/bigdata-interop/tree/master/pubsub)
* [Spark Spanner Connector](https://github.com/GoogleCloudPlatform/cloud-spanner-spark-connector)
* [Hive Bigquery Storage Handler](https://github.com/GoogleCloudPlatform/hive-bigquery-storage-handler)

### Kubernetes Operators
* [Spark kubernetes operator](https://github.com/GoogleCloudPlatform/spark-on-k8s-operator)
* [Flink kubernetes operator](https://github.com/GoogleCloudPlatform/flink-on-k8s-operator)

### Examples
* [Dataproc Python
  examples](https://github.com/GoogleCloudPlatform/python-docs-samples/tree/master/dataproc)
* [Dataproc Pubsub Spark Streaming example](https://github.com/GoogleCloudPlatform/dataproc-pubsub-spark-streaming)
* [Dataproc Java Bigtable sample](https://github.com/GoogleCloudPlatform/cloud-bigtable-examples/tree/master/java/dataproc-wordcount)
* [Dataproc Spark-Bigtable samples](https://github.com/GoogleCloudPlatform/cloud-bigtable-examples/tree/master/scala)

## For more information
For more information, review the [Dataproc
documentation](https://cloud.google.com/dataproc/docs/). You can also
pose questions to the [Stack
Overflow](http://stackoverflow.com/questions/tagged/google-cloud-dataproc) community
with the tag `google-cloud-dataproc`.
See our other [Google Cloud Platform github
repos](https://github.com/GoogleCloudPlatform) for sample applications and
scaffolding for other frameworks and use cases.

## Contributing changes

* See [CONTRIBUTING.md](CONTRIBUTING.md)

## Licensing

* See [LICENSE](LICENSE)
