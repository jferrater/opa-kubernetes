### Open Policy Agent - Kubernetes

#### Create configmap for policy, example.rego
First, create a ConfigMap containing a test policy. The test policy will define a blacklist that rejects:

    - Objects missing a 'customer' label.
    - Pods referring to images outside the corporate registry.

In this case, the policy file does not contain sensitive information so it's fine to store as a ConfigMap. If the file contained sensitive information, then we recommend you store it as a Secret.

`kubectl create configmap example-policy --from-file example.rego`

#### Create Deployment and Service objects
Next, create a Deployment to run OPA. The ConfigMap containing the policy is volume mounted into the container. This allows OPA to load the policy from the file system.

`kubectl create -f deployment-opa.yaml`

At this point OPA is up and running. Create a Service to expose OPA API so that you can query it.
`kubectl create -f service-opa.yaml`

Now you can query OPA's API. If you use the Pod example-pod.json, deny will be true because the Pod refers to image outside the corporate registry.

`curl $OPA_URL/v1/data -d @example-pod.json`

If you update the image to refer to the corporate registry, deny will be false.
