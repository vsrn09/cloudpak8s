---

title: Governance and Risk
weight: 200

---

{:toc}


## Introduction

Here we can define and review policy definitions and see the compliance state of managed clusters.

### Create Simple Policy

Let's create a new policy

1.	Open MCM Web Console https://icp-console.apps.res-cp4mcm.ocp.csplab.local/multicloud/policies/create

2. Navigate to Menu -> Govern risk.  Policies This view displays the policies that have been created and the dashboard of policy compliance for each cluster.

   ![Image]({{ site.github.url }}/assets/img/cp4mcm/PolicyCreate.png)


3. Click on Create policy button.

   ![Image]({{ site.github.url }}/assets/img/cp4mcm/cp4mcm_2.png)

4. Fill the values as specified in the table below:

|      Field Name      | Value                                                        |
| :------------------: | :----------------------------------------------------------- |
|         Name         | policy-namespace                                             |
|      Namespace       | Choose: Namespace-must have namespace ‘prod’ Note: You will modify the name prod to k8demo. Selecting this will provide a template to have custom namespace policy definition |
|    Specifications    | Choose: “Namespace”                                          |
|   Cluster Binding    | Choose name: “local-cluster”                                 |
|      Standards       | Choose: "FISMA"                                              |
|      Categories      | Choose: "SystemAndInformationIntegrity"                      |
|       Controls       | Choose: "Mutation Advisor"                                   |
| Enforce if Supported | Leave as is                                                  |
|    Disable Policy    | Leave as is                                                  |

5. In the yaml file section, on the right, change prod to "k8sdemo".

   Changing the namespace will change the Policy Specifications to Custom Specifications as below. Notice that the policy is set to “inform” rather than “enforce”.

   ![Image](/Users/venkatsnamala/cloudpak8s/assets/img/cp4mcm-5.png)

   

6. Use the button Create,   to create your new policy.

7. If you are no redirected automatically navigate to Menu -> Govern risk to return to the Dashboard.

8. Remember, you didn’t enforce this policy. Instead we specified inform. As such, the Governance and risk view displays a policy violation in our cluster, as illustrated below.

   ![image-20200123141805792](/Users/venkatsnamala/cloudpak8s/assets/img/cp4mcm-3.png)

9. Use the Cluster Violation link   to find which cluster is violating the policy.

   The local-cluster cluster is in violation of the policy which requires a namespace called “k8demo” to exist.
   The local-cluster cluster is our cluster, and the same cluster that verified “k8sdemo” namespace does not exist. Hence it shows that there is no namespace k8demo in the cluster.

10. Verify the k8demo namespace still does not exist.

    ```
    oc get projects
    ```


    There should NOT be a namespace named k8demo listed, which indicates the policy did not ENFORCE it to be created.

11. Change the “policy-namespce” policy to be enforced
    When a policy is in “enforce” mode, the namespace will automatically be created, if it does not exist, thereby enforcing the cluster into compliance.
    a.	In the policies view, select  POLICY VIOLATIONS
    b.	Then, select the policy named “policy-namespace” and go to YAML view
    c.	Click on the   button to go into edit mode.
    d.	Change the value of “remediationAction: inform” to “remediationAction: enforce”
    e.	Click the   to submit the change

12. Select the policy-namespace link. A few seconds later, the policy violation will be gone away.

13. You also can validate the same from the Violations view

14. Run the command oc get ns|grep k8demo and ensure that the k8demo namespace has been created in the cluster.

15. Try deleting the namespace and check how is being created automatically again.



### Namespace Policy

This policy will check the Cluster Selector, and verify if a namespace named “policy-namespace-k8demo” exists, if the Enforce if supported field is true, the namespace will be automatically created on the selected cluster, if false then the violation/compliance of the policy will be reported on the dashboard.

The policy controller will check if the namespace k8demo is present and provides information regarding the current compliance of the policies.

Create policy by setting the following values:

| Field Name           | Value                                                        |
| :------------------- | :----------------------------------------------------------- |
| Name                 | policy-namespace                                             |
| Namespace            | Choose: Namespace-must have namespace ‘prod’ Note: You will modify the name prod to k8demo. Selecting this will provide a template to have custom namespace policy definition |
| Specifications       | Choose: “Namespace”                                          |
| Cluster Binding      | Choose name: “local-cluster”                                 |
| Standards            | Choose: "NIST-CSF"                                           |
| Categories           | Choose: "SystemAndInformationIntegrity"                      |
| Controls             | Choose: "Mutation Advisor"                                   |
| Enforce if Supported | Leave as is                                                  |
| Disable Policy       | Leave as is                                                  |


![Image]({{ site.github.url }}/assets/img/cp4mcm/prodnamespace.png)

Here is the YAML that this generates.

![Image]({{ site.github.url }}/assets/img/cp4mcm/prodnamespaceyaml.png)

As we have set this policy to `enforce` this will create a `prod` namespace on our targeted clusters.

```
 oc get namespace | grep -i prod
```

Create some more policies and then explore the console that is used to give a high level view of the cluster compliance with your defined Policies.

Start with a high level view of the cluster policy compliance.

![Image]({{ site.github.url }}/assets/img/cp4mcm/PolicySummary.png)

Then by `category` look at which clusters are found to be not compliant with named policies.

![Image]({{ site.github.url }}/assets/img/cp4mcm/ClusterSummary2.png)


![Image]({{ site.github.url }}/assets/img/cp4mcm/PolicyCollection.png)

Finally, look at all of the policy compliance associated with you collection of `PCI` compliance policies.

![Image]({{ site.github.url }}/assets/img/cp4mcm/SISummary.png)


### Network Policy

The Network Policy is used to control (block) network traffic from other pods.

Configure the new network policy according to the table below

| Field Name           | Value                                                        |
| :------------------- | :----------------------------------------------------------- |
| Name                 | policy-namespace                                             |
| Namespace            | Choose: Namespace-must have namespace ‘prod’ Note: You will modify the name prod to k8demo. Selecting this will provide a template to have custom namespace policy definition |
| Specifications       | Choose: “Audit Policy”                                       |
| Cluster Binding      | Choose name: “local-cluster”                                 |
| Standards            | Choose: "FISMA"                                              |
| Categories           | Choose: "SystemAndInformationIntegrity"                      |
| Controls             | Choose: "Mutation Advisor"                                   |
| Enforce if Supported | Leave as is                                                  |
| Disable Policy       | Leave as is                                                  |



You can validate the network policy that is created on the selected namespace.

 Using the CLI, run the following command to get the network policies for the namespace

	oc get networkpolicies -n <namespace>

This kind of  policy can be used to allow or deny the communication between pods living on different 		namespaces.

### 	 Pod must exist in a given namespace Policy

This kind of policy validates if a pod is present in a given namespace.

Configure the new policy, requiring pod be present, according to the table below

| Field Name           | Value                                                        |
| :------------------- | :----------------------------------------------------------- |
| Name                 | policy-namespace                                             |
| Namespace            | Choose: Namespace-must have namespace ‘prod’ Note: You will modify the name prod to k8demo. Selecting this will provide a template to have custom namespace policy definition |
| Specifications       | Choose: “Audit Policy”                                       |
| Cluster Binding      | Choose name: “local-cluster”                                 |
| Standards            | Choose: "FISMA"                                              |
| Categories           | Choose: "SystemAndInformationIntegrity"                      |
| Controls             | Choose: "Mutation Advisor"                                   |
| Enforce if Supported | Leave as is                                                  |
| Disable Policy       | Leave as is                                                  |

Notice you can change the name of the pod and the image on the yaml section to create any kind of pod, make sure of write a valid value on the image parameter.

 Also you can change in the namespaces section, the namespaces you want your policy takes effect.



### Limits memory range for a namespace Policy

Configure the new policy, enforcing quota limits, according to the table below

| Field Name           | Value                                                        |
| :------------------- | :----------------------------------------------------------- |
| Name                 | policy-namespace                                             |
| Namespace            | Choose: Namespace-must have namespace ‘prod’ Note: You will modify the name prod to k8demo. Selecting this will provide a template to have custom namespace policy definition |
| Specifications       | Choose: “Audit Policy”                                       |
| Cluster Binding      | Choose name: “local-cluster”                                 |
| Standards            | Choose: "FISMA"                                              |
| Categories           | Choose: "SystemAndInformationIntegrity"                      |
| Controls             | Choose: "Mutation Advisor"                                   |
| Enforce if Supported | Leave as is                                                  |
| Disable Policy       | Leave as is                                                  |

 To validate the quota is created on the selected namespace

	oc get networkpolicies -n <namespace>
	
	oc -n <namespace> get limits
	
	oc -n <namespace> get limits –o yaml



### Mutation Policy

A mutation policy contains the specifications of which pods to monitor and what action to take if a mutation is detected, for example if this policy is created and configured for an specific namespace, and you change something (such as edit or delete a file) in a running pod of that namespace, a violation will be notified, if the policy is enforced, the pod will be restarted.

| Field Name           | Value                                                        |
| :------------------- | :----------------------------------------------------------- |
| Name                 | policy-namespace                                             |
| Namespace            | Choose: Namespace-must have namespace ‘prod’ Note: You will modify the name prod to k8demo. Selecting this will provide a template to have custom namespace policy definition |
| Specifications       | Choose: “Audit Policy”                                       |
| Cluster Binding      | Choose name: “local-cluster”                                 |
| Standards            | Choose: "FISMA"                                              |
| Categories           | Choose: "SystemAndInformationIntegrity"                      |
| Controls             | Choose: "Mutation Advisor"                                   |
| Enforce if Supported | Leave as is                                                  |
| Disable Policy       | Leave as is                                                  |

## Use Cases

Here are some use cases we are going to review to understand the MCM product in more detail.

### Use Case 1

In this use case we are going to create a policy with the following rules.

1. Create a Namespace enforcement

2. Set Quotas to the namespace for min, max and default values for the CPU and Memory by the pod

3. Create a policy with the specifications: Namespace, Pod and LimitRange.

4. Edit the generated yaml:

5. Final yaml looks as below:

6. After enforcing the policy you can validate it works by checking the following

7. Check **limits** are created  





### Use Case 2

In this use case we are going to create a Mutation policy with the following rules.

1. Use the  previous exercise for the monitoring the drift in the container in ModResort application

2. Login  to the running container

3. Remove the file server.xml (/opt/ibm/wlp/usr/servers/defaultServer/server.xml)

4. Validate the policy violation

5. Modify the policy to enforce

6. Validate the pod is recreated so that drift in gone

7. Create a policy with the specifications: MutationPolicy

8. Edit the generated yaml:

9.  The yaml looks like this:

10. Before enforcing the policy you need to force a mutation on the modresort pod.

11. Force **mutation** by login to the running pod

    oc -n fs2020 exec -it modresort-pod bash

12. Go to the defaultServer directory and delete/edit the file server.xml, and exit

      `cd /opt/ibm/wlp/usr/servers/defaultServer`
    `rm server.xml`
    `exit`

### Use Case 3

In this use case we are going to create a Network policy with the following rules.

1. Define a Network policy and validate the micro services connectivity between services
2.  We are going to create an application which has 2 pods and one pod service needs to connect to the other pod's  service ( For example: We are going  to use the Quote of the Day application for our use case)
3. Define the policy that deny all ingress and egress traffic to all the pods of the app ( For the Quote of the Day application)
4.    	Deploy the network policy created in previous step
5.    	Once we enforce the above network policy application stops working
6.    	Edit the policy defination to make application work again
