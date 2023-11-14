# Sample Readme to record basic workflow for TMC API Automation
#
#

## 1 - TMC API Reference
- Automation of TMC activitites via REST API
- Swagger Spec - https://vdc-download.vmware.com/vmwb-repository/dcr-public/faa99fd1-ee55-452c-9954-0448e2989ec4/d1f1690e-6b2a-404c-b31e-1e25d3369a7a/public-swagger.yaml
- Swaggerhub with TMC Spec Loaded in Visualizer - https://app.swaggerhub.com/apis/tkrausjr/swagger-tanzu_mission_control/0.0.1#


## 2 - Setup Postman and obtain API Token 
- [ ] Download & Install Latest version of Postman
- [ ] Download the updated Postman TMC Collection from postman folder in this REPO.
	- [ ] https://raw.githubusercontent.com/tkrausjr/tmc-api/main/postman/TMC-API-Updated-TK.postman_collection.json
- [ ] Import the Collection into Postman
- [ ] Login to your Account in VMware Cloud Services
	- [ ] https://console.cloud.vmware.com/csp/gateway/portal/#/user/profile
- [ ] Gererate an API Token
	- [ ] Login to your ORG
	- [ ] In Top Right of TMC or Cloud Console, Click your UserName --> Goto "USER SETTINGS" --> My Account
	- [ ] Select the Last Tab called "API Tokens" --> Generate TOKEN --> Copy the token generaged.


## 3 - Configure Postman Variables
- [ ] baseUrl = \<orgname\>.tmc.cloud.vmware.com (Obtained from URL field when you loginto TMC)
- [ ] refreshToken = API Token generated above
- [ ] provisionerName = vSphere Namespace Name 
- [ ] managementClusterName = Name of SC Management Cluster you added in TMC --> Administration
      

## 4 - Run "Get Access Token" POST in Login Folder
- [ ] API Reference
	- [ ] Request
 		- [ ] VERB: POST
		- [ ] URL: https://console.cloud.vmware.com/csp/gateway/am/api/auth/api-tokens/authorize?refresh_token={{refreshToken}}
       		- [ ] Headers:
			- [ ] Content-Type:  application/x-www-form-urlencoded
   	- [ ] Response
      	 	- [ ] Success Code: 200		
     		- [ ] Body:
			- [ ]  "id_token": "eyJhbGciOiJSUz........."  
			- [ ] NOTE: This is the Bearer Token needed for all subsequent REST calls.




## 5 - Run
## 6 - Run
## 7 - Run
## 8 - Run
