# Simloud connect to K8s and AWS services

1. Install and enable [Docker](https://docs.docker.com/get-docker/) in your local computer.
2. Install and configure [AWS CLI](https://aws.amazon.com/cli/) in your local computer.
3. Configure aws cli user in your local directory.
4. Create a new cloud user in your Simloud account:

![](/img/onboarding/connect-k8s-aws/image1.png)

5. Add this user to your cloud account and press the save button:

![](/img/onboarding/connect-k8s-aws/image2.png)

6. Go to the deployment which is configured with this cloud account and press the button "Connect K8s" and follow the instructions:

![](/img/onboarding/connect-k8s-aws/image3.png)
![](/img/onboarding/connect-k8s-aws/image4.png)

### How to connect K8s

1. Make sure that your [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) credentials are intalled on your local computer.
2. Enable [Docker](https://docs.docker.com/engine/install/) in your local computer.
3. Copy the following docker commands and run it locally:

   **Windows**

   ```
   docker run --rm -ti -v %HOMEDRIVE%%HOMEPATH%/.kube:/root/.kube -v %HOMEDRIVE%%HOMEPATH%/.ssh:/root/.ssh -v %HOMEDRIVE%%HOMEPATH%/.aws:/root/.aws -v %CD%:/code simloud/aws-tools:1.1.28 /bin/bash
   ```

   **Mac/Linux**

   ```
   docker run --rm -ti -v $HOME/.kube:/root/.kube -v $HOME/.ssh:/root/.ssh -v $HOME/.aws:/root/.aws -v $(pwd):/code simloud/aws-tools:1.1.28 /bin/bash
   ```

4. Copy assume role and run it inside your docker.
   :::note
   Connect string will be generated after deployment creation.
   :::

5. Copy file from/to docker _(Optional)_

   **From**

   ```
   docker cp simloud:tmp/yourfile /tmp/yourfile
   ```

   **To**

   ```
   docker cp /tmp/yourfile simloud:tmp/yourfile
   ```

### How to access jenkins/grafana/k8s-dashboarf

Open deployment menu and choose the service:

![](/img/onboarding/connect-k8s-aws/image6.png)

1. Jenkins - `admin` / `LA#$4zUFl%Xk9!WM`
2. monitoring (grafana) - `admin` / `password`

   **Password:**

   ```
   kubectl get secret --namespace kube-system grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
   ```

3. vault (Hashicorp vault) - token

   **Token:**

   ```
   kubectl get secret --namespace kube-system vault-root-token -o jsonpath="{.data.root_token}" | base64 -d ; echo
   ```

4. K8s dashboard - click on the CPU/Memory graph

Use token.

**Token**

```
kubectl get secret -n kube-system $(kubectl get secret -n kube-system | grep dashboard-token | awk '{ print $1 }') -o jsonpath="{.data.token}" | base64 -d ; echo
```

### How to access AWS services - Cloudwatch logs/System manager

1. Login to your aws console.
2. Click the relevant button on Simloud UI

![](/img/onboarding/connect-k8s-aws/image7.png)

3. System manager:

   - Allows you to ssh to the k8s worker nodes.
   - Once you select this option this service will be opened in your AWS console.
   - It will display the list of available worker nodes.
   - Please select the worker node you would like to connect.

4. Cloudwatch logs:

   - Allows you to connect to Cloudwatch insight to perform centralized log query.
   - Simloud sends all k8s traffic to AWS Cloudwatch log groups - both application and cluster level.
   - In addition lambda functions are sending logs to AWS Cloudwatch log group.
   - Once you press the Cloudwatch logs button this service will be opened in your AWS console with the relevant log groups already selected.
   - Please perform the log query according to AWS insight syntax.

5. SSH to pod

   - Get list of pods:
     ```
     kubectl get pod
     ```
   - Connect to pods:
     ```
     kubectl exec --stdin --tty pod-xx -- /bin/sh
     ```
     ```
     kubectl exec --stdin --tty pod-xx -- /bin/bash
     ```
   - Mount a folder to the local docker linux/mac:

     ```
     docker run --rm -ti -v $HOME/Downloads:/tmp -v $HOME/.kube:/root/.kube -v $HOME/.ssh:/root/.ssh -v $HOME/.aws:/root/.aws -v $(pwd):/code hub.simloudcorp.customers.simloud.com/simloud/simloud-tools:1.1.28 /bin/bash
     ```

   - Mount a folder to the local docker windows:

     ```
     docker run --rm -ti -v %HOMEDRIVE%%HOMEPATH%/.kube:root/.kube -v %HOMEDRIVE%%HOMEPATH%/.ssh:/root/.ssh -v %HOMEDRIVE%%HOMEPATH%/.aws:/root/.aws -v %CD%:/code simloud/aws-tools:1.1.26 /bin/bash
     ```

   - Copy to pod from local docker
     ```
     kubectl cp file pod-xx:/service/srv/tmp
     ```
