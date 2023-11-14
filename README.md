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

* REQUEST:
	* VERB: Post
	* URL: https://console.cloud.vmware.com/csp/gateway/am/api/auth/api-tokens/authorize?refresh_token={{refreshToken}}
	* HEADERS:
		* Content-Type:  application/x-www-form-urlencoded
* RESPONSE:
	* SUCCESS CODE: 200
	* BODY:
		* "id_token": "eyJhbGciOiJSUz........."
		* NOTE: This is the Bearer Token needed for all subsequent REST calls.




## 5 - Run "Get Cluster List"  in Clusters Folder

* REQUEST:
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
 		* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN>"

* RESPONSE:
	* SUCCESS CODE: 200
	* BODY_TYPE:  application/json
	* BODY:   
		* { "clusters":
			* [...]
		* }
   	          
## 6 - Run "List Cluster Groups"  in Cluster Groups folder

* REQUEST:
	* VERB: Get
	* URL: https://{{baseUrl}}/v1alpha1/clustergroups?searchScope.name=*
	* HEADERS(Required):
		* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"

* RESPONSE:
	* SUCCESS CODE: 200
	* BODY_TYPE:  application/json
	* BODY:   
		* { "clusterGroups":
			* [...]
		* }

## 7 - Run "Attach Existing Namespace"  in Workspaces-Namespaces folder

* REQUEST:
	* VERB: Post
	* URL: https://{{baseUrl}}/v1alpha1/clusters/shared-01/namespaces
	* HEADERS(Required):
 		* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"
   		* "Content-Type":  "application/json"
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

* RESPONSE:
	* SUCCESS CODE: 200
	* BODY_TYPE:  application/json
	* BODY:   
			

## 8 - Run "Create Cluster"  in Create Cluster folder

* REQUEST:
	* VERB: Post
	* URL: https://{{baseUrl}}/v1alpha1/managementclusters/{{managementClusterName}}/provisioners/{{provisionerName}}/tanzukubernetesclusters  
	* HEADERS(Required):
		* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"
		* "Content-Type":  "application/json"
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

* RESPONSE:
	* SUCCESS CODE: 200
	* BODY_TYPE:  application/json
	* BODY:

## 9 - (Work in Progress) Run "Get Kubeconfig for Cluster"  in Clusters folder
* NOTE: Need to provide PEM Cert so the API response can be encrypted for security purposes.
* REQUEST:
	* VERB: Get
	* URL: https://{{baseUrl}}/v1alpha1/clusters/shared-01/adminkubeconfig?fullName.managementClusterName=sc-802-vds&fullName.provisionerName=tanzu-dev01&encryptionKey.PublicKeyPem=LS0tLS1CRUdJTiBSU0EgUFVCT.......tFWS0tLS0tCg==&encryptionKey.timestamp=2023-11-09T20:00:00.0Z
 		* NOTE: Where encryptionKey.PublicKeyPem was created with
	 	* openssl genrsa -out private-key.pem 3072; openssl rsa -in private-key.pem -RSAPublicKey_out -out public-key.pem
	 	* Then need to base64 encode.
	 	* cat ./openssl-pubic-key.pem |base64 -w 0 LS0tLS1CRUdJTiBSU0EgUFVCT.......tFWS0tLS0tCg== 
	* HEADERS(Required):
		* "Authorization" : "Bearer <INSERT_VALUE_FROM_id_token_RETURNED_FROM_LOGIN"
  		* "Content-Type":  "application/json"
* RESPONSE:
	* SUCCESS CODE: 200
	* BODY_TYPE:  application/json
	* BODY:
```javascript
{
    "kubeconfig": "wcDMA67Gf+Gr1AjBAQwAveePqC3HeeGJTtT9f2DfseHEgJOL91S7coY/ZcocwhXP8DDDZlzl8CfU4f2Vfg.......3PXEttBW17/+2OOhLjT0ikSipXb4bP0p4I7iY7pJruCc4EDghuQRYvdofyBcmNzswc2Vmsfn4pHxMAvhZh0A"
}
```
The response above is first encrypted using the PEM key you supplied in the Request and then the encrypted data is Base64 encoded.

1- Copy the value of the "kubeconfig" key so you can BASE64 Decode the PGP encrypted Response.
2- echo "wcDMA67Gf+Gr1AjBAQ.......yBcmNzswc2Vmsfn4pHxMAvhZh0A" | base64 -d
```javascript
        珨-yN`߱ĀTr?e0f\'~N	_.v*^XMe^J
                                                                                     b/v56A
TTSc# ѱBO"hY"b                                                                u@wH\yg)ϴ7/	yg^@qbc\2o_ĕ?04U֛]\!iz
){ɀ#YcKu<u{>Mn'N@{'d7>@࢜](L
                                                        q!gMOX#?GzE:ߧbAN\F+	k>ťl6u4
                                                                                                                  㥳Y2KGM}~
Di&j"$fGST|״&2V!F6
    e
j;beTnJT#.ԔAmt\O
      ;ܖ2'F\HR!skW=VvVWбQ^.5#Xƈ.]uA]	G^Y%NKrKxuNZuɪwDB®-.c"vjMN[}>2.>eeNi?I9Eގr$Lu}3Az6 OOc+2,P<)Ei J$:'mJ<M^윦j΍ږƛݷk$	$̫P4g
                                                  {g=I
'Vl
.MWen{^$hG7J/"Z8œM
                             9x*uOm	I Zf4`NKƣdSXĶdd>AZyO
```
Now to get the kubeconfig, we have to decrypt but this has been tough because we didnt encrypt it wiht PGP, we used openssl and I cant seem to import and openssl RSA Private or Public Key into PGP to be able to decrypt it.
