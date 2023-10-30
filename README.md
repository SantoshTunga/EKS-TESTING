# EKS-TESTING
EKS testing
To create an HPA, you will need to specify the following:

The target CPU utilization percentage: This is the percentage of CPU utilization that the HPA will try to maintain.
The minimum and maximum number of replicas: These are the minimum and maximum number of pods that the HPA will allow in the deployment.
You can create an HPA using the following command:

kubectl autoscale deployment <deployment-name> --cpu-percent=80 --min=1 --max=10
This will create an HPA that targets 80% CPU utilization for the deployment, with a minimum of one pod and a maximum of ten pods.

Once the HPA is created, it will start monitoring the CPU utilization of the pods in the deployment. If the average CPU utilization across all the pods exceeds 80%, the HPA will spin up additional pods. The number of additional pods spun up is calculated as follows:

desiredReplicas = ceil[currentReplicas * ( currentMetricValue / desiredMetricValue )]
For example, if the HPA is targeting 80% CPU utilization and the average CPU utilization across all the pods is 90%, the HPA will spin up an additional pod.

The HPA will continue to monitor the CPU utilization of the pods and adjust the number of pods as needed to maintain the target CPU utilization.

Here is an example of an HPA manifest:

YAML
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: busybox-1
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: busybox-1
  minReplicas: 3
  maxReplicas: 4
  targetCPUUtilizationPercentage: 80

------------------------

To apply this manifest, you can use the following command:

kubectl apply -f hpa.yaml
This will create an HPA for the busybox-1 deployment.

You can view the status of the HPA using the following command:

kubectl get hpa
This will show you the current number of replicas in the deployment, the target CPU utilization, and the minimum and maximum number of replicas.

You can also use the kubectl describe hpa <hpa-name> command to get more detailed information about the HPA.

Example 2

apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 80
    
apiVersion specifies the Kubernetes API version used for the HPA resource.
kind defines the resource type as "HorizontalPodAutoscaler."
metadata includes the name of the HPA resource.
Inside the spec section:

scaleTargetRef specifies the target deployment that the HPA should scale. Make sure to replace "my-app" with the name of your actual deployment.
minReplicas and maxReplicas set the minimum and maximum number of replicas for the deployment.
Under the metrics section, you specify the metric to use for autoscaling. In this case, we're using CPU utilization (Resource metric type). The targetAverageUtilization is set to 80, which means the HPA will aim to maintain an average CPU utilization of 80%.
Make sure to customize the name and other values as per your actual deployment and requirements. Apply this YAML configuration using the kubectl apply -f hpa.yaml command to create the HPA resource.
