# Sample Readme to record basic workflow for TMC API Automation
#
#

## 1 - TMC API Reference
- Automation of TMC activitites via REST API
- Swagger Spec - https://vdc-download.vmware.com/vmwb-repository/dcr-public/faa99fd1-ee55-452c-9954-0448e2989ec4/d1f1690e-6b2a-404c-b31e-1e25d3369a7a/public-swagger.yaml
- Swaggerhub with TMC Spec Loaded in Visualizer - https://app.swaggerhub.com/apis/tkrausjr/swagger-tanzu_mission_control/0.0.1#


## 2 - Setup Postman and obtain API Token 
* Download & Install Latest version of Postman
* Download the updated Postman TMC Collection from postman folder in this REPO.
	* https://raw.githubusercontent.com/tkrausjr/tmc-api/main/postman/TMC-API-Updated-TK.postman_collection.json
* Import the Collection into Postman
* Login to your Account in VMware Cloud Services
	* https://console.cloud.vmware.com/csp/gateway/portal/#/user/profile
* Gererate an API Token
	* Login to your ORG
	* In Top Right of TMC or Cloud Console, Click your UserName --> Goto "USER SETTINGS" --> My Account
	* Select the Last Tab called "API Tokens" --> Generate TOKEN --> Copy the token generaged.


## 3 - Configure Postman Variables
* baseUrl = \<orgname\>.tmc.cloud.vmware.com (Obtained from URL field when you loginto TMC)
* refreshToken = API Token generated above
* provisionerName = vSphere Namespace Name 
* managementClusterName = Name of SC Management Cluster you added in TMC --> Administration
      

## 4 - Run "Get Access Token"  in Login Folder
* API Reference
	* Request:
 		* VERB: Post
		* URL: https://console.cloud.vmware.com/csp/gateway/am/api/auth/api-tokens/authorize?refresh_token={{refreshToken}}
  		* HEADERS:
			* Content-Type:  application/x-www-form-urlencoded
   	* Response:
   		* SUCCESS CODE: 200
   	    	* BODY:
   	     		* "id_token": "eyJhbGciOiJSUz........."
   	      		* NOTE: This is the Bearer Token needed for all subsequent REST calls.




## 5 - Run "Get Cluster List"  in Clusters Folder
* API Reference
	* Request:
 		* VERB: Get
		* URL: (below shows different searchScopes to filter)
  			* https://{{baseUrl}}/v1alpha1/clusters?searchScope.name=*
				* Returns all clusters in the ORG you have permissions for.  
			* https://{{baseUrl}}/v1alpha1/clusters?searchScope.name=<cluster-name>
				* Returns just clusters that match that name
			* https://{{baseUrl}}/v1alpha1/clusters?searchScope.provisionerName=spvn
				* Returns just clusters that are part of the specified provisioner (TKGs Namespace)
			* https://{{baseUrl}}/v1alpha1/clusters?searchScope.managementClusterName=tpmlab-host66-sc
				* Returns all Clusters under a given Management Cluster.
    
  		* HEADERS(Required):
    			* \"Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN>"

   	* Response:
   		* SUCCESS CODE: 200
		* BODY_TYPE:  application/json
		* BODY:   
			* { "clusters":
				* [...]
			* }
   	          
## 6 - Run "List Cluster Groups"  in Cluster Groups folder
* API Reference
	* Request:
 		* VERB: Get
		* URL: https://{{baseUrl}}/v1alpha1/clustergroups?searchScope.name=*
  		* HEADERS(Required):
    			* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"

   	* Response:
   		* SUCCESS CODE: 200
		* BODY_TYPE:  application/json
		* BODY:   
			* { "clusterGroups":
				* [...]
			* }

## 7 - Run "Attach Existing Namespace"  in Workspaces-Namespaces folder
* API Reference
	* Request:
 		* VERB: Post
		* URL: https://{{baseUrl}}/v1alpha1/clusters/shared-01/namespaces
  		* HEADERS(Required):
    			* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"
      			* "Content-Type:  application/json"
      		* BODY:
```javascript
{
    "namespace": {
      "fullName": {
        "managementClusterName": "sc-802-vds",
        "provisionerName": "tanzu-dev01",
        "clusterName": "shared-01",
        "name": "local-kubectl-test-tmc-3"
      },
      "meta": {},
      "spec": {
        "workspaceName": "sort-iot",
        "attach": true
      }
    }
  }
```

   	* Response:
		* SUCCESS CODE: 200
		* BODY_TYPE:  application/json
		* BODY:   
			

## 8 - Run "Create Cluster"  in Create Cluster folder
* API Reference
	* Request:
 		* VERB: Post
		* URL: https://{{baseUrl}}/v1alpha1/managementclusters/{{managementClusterName}}/provisioners/{{provisionerName}}/tanzukubernetesclusters  
  		* HEADERS(Required):
    			* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"
      			* "Content-Type:  application/json"
      		* BODY:
```javascript
{ "tanzuKubernetesCluster":
  {"fullName": {
    "orgId": "b00b7417-7997-48e7-a763-ac3c8d04d323",
    "managementClusterName": "sc-802-vds",
    "provisionerName": "tanzu-dev01",
    "name": "test-tmc-api-w-labels"
  },
  "meta": {
   "labels": {
        "owner": "tkraus"
        } },
  "spec": {
    "clusterGroupName": "sort-edge-clusters",
    "tmcManaged": true,
    "topology": {
      "version": "v1.26.5+vmware.2-fips.1-tkg.1",
      "clusterClass": "tanzukubernetescluster",
      "controlPlane": {
        "replicas": 1,
        "metadata": {},
        "osImage": {
          "name": "ubuntu",
          "version": "20.04",
          "arch": "amd64"
        }
      },
      "nodePools": [
        {
          "spec": {
            "class": "node-pool",
            "replicas": 1,
            "overrides": [
              {
                "name": "storageClass",
                "value": "nfs-datastores"
              }
            ],
            "metadata": {},
            "osImage": {
              "name": "ubuntu",
              "version": "20.04",
              "arch": "amd64"
            }
          },
          "info": {
            "name": "api-md-0"
          }
        }
      ],
      "variables": [
        {
          "name": "defaultStorageClass",
          "value": "nfs-datastores"
        },
        {
          "name": "defaultVolumeSnapshotClass",
          "value": "volumesnapshotclass-delete"
        },
        {
          "name": "vmClass",
          "value": "best-effort-small"
        },
        {
          "name": "storageClass",
          "value": "nfs-datastores"
        },
        {
          "name": "controlPlaneCertificateRotation",
          "value": {
            "activate": false,
            "daysBefore": 90
          }
        }
      ],
      "network": {
        "pods": {
          "cidrBlocks": [
            "172.20.0.0/16"
          ]
        },
        "services": {
          "cidrBlocks": [
            "10.96.0.0/16"
          ]
        },
        "serviceDomain": "cluster.local"
        }
      }
    }
  }
}
```

   	* Response:
		* SUCCESS CODE: 200
		* BODY_TYPE:  application/json
		* BODY:   
