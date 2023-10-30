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

*******************************


Currently, it is not possible to combine CPU and memory percentage in the same HPA YAML. HPAs can only scale based on a single metric.

However, there are a few workarounds that you can use to achieve similar results. For example, you could create two separate HPAs, one for CPU and one for memory. You could then configure the HPAs to scale the deployment up or down based on the average of the CPU and memory utilization metrics.

Another workaround is to use a third-party tool such as Prometheus or Grafana to monitor the CPU and memory utilization metrics and trigger scaling events based on the combined utilization.

It is important to note that using these workarounds can be more complex and less reliable than using a single HPA. It is also important to carefully consider the scaling needs of your application before choosing a solution.

# Here is an example of a two-HPA workaround:

YAML
# CPU HPA
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: cpu-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 3
  maxReplicas: 5
  targetCPUUtilizationPercentage: 80

# Memory HPA
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: memory-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 3
  maxReplicas: 5
  targetMemoryUtilizationPercentage: 80
  
Use code with caution. Learn more
To trigger scaling events based on the combined CPU and memory utilization, you could use a third-party tool such as Prometheus or Grafana to create a custom alert rule that triggers when the combined utilization exceeds a certain threshold. For example, the following alert rule would trigger when the combined CPU and memory utilization exceeds 90%:

ALERT combined_utilization_high
IF (avg(rate(container_cpu_usage_seconds_total{namespace="my-namespace", pod="my-deployment"}[5m])) * 100 > 80) AND
(avg(container_memory_usage_bytes{namespace="my-namespace", pod="my-deployment"}[5m]) / container_memory_limit_bytes{namespace="my-namespace", pod="my-deployment"}) * 100 > 80
FOR 5m
LABELS: severity="high"
ANNOTATIONS: description="Combined CPU and memory utilization is high"

Once the alert is triggered, you could configure the third-party tool to scale the deployment up or down based on the alert.

It is important to note that this is just a simple example. You may need to modify the alert rule and scaling configuration to meet the specific needs of your application.
