1. 创建一个服务账号(A)，并授予以下权限,如果有多个项目需要自动标记，可以将多个项目放置于组织中的某个目录下，在目录层级授予以下权限：
Cloud SQL Editor
Compute Instance Admin(v1)
Pub/Sub Subscriber

2. 在每个要监控的project中启用服务api
gcloud services enable cloudasset.googleapis.com pubsub.googleapis.com cloudfunctions.googleapis.com cloudbuild.googleapis.com --project <project-id>

gcloud services enable compute.googleapis.com container.googleapis.com storage.googleapis.com sqladmin.googleapis.com --project <project-id>


3. 创建cloud asset的feeds，向pub/sub发送资源消息
gcloud asset feeds create "feed-resources-<project-id>" --project=<project-id> --content-type=resource --asset-types="compute.googleapis.com/Instance,sqladmin.googleapis.com/Instance" --pubsub-topic="projects/<project-id>/topics/resource-auto-label-<project-id>"


gcloud asset feeds describe feed-resources-<project-id> --project=<project-id>


4. 部署cloud function

 gcloud functions deploy auto_resource_labeler --region=us-central1 --memory=128MB --runtime python38 --trigger-topic "resource-auto-label-<project-id>" --service-account="<A>@lxd-firebase.iam.gserviceaccount.com" --project <project-id>