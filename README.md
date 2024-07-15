# Terraform 
#Terraform concepts  terraform configuration file- tells terraform how to manage the infrastruct  

terraform{
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      vision = "x.xx.x"
     } 
  } 
}  
provicer "azurerm" {
  subscription_id = ""
  client_id       = ""
  client_secret   = ""
  tenant_id       = ""
  features {}
}

resource "" "" {
  name= ""
  location=""
}


provider - azure or amazon web services.

#build confi file first; terrform "plan command", creat execution plan base on the config have; "apply commad", execute action; "destroy command" destroy infra object base on the config
install terraform and visual studio code 

C:\apps>terraform version
Terraform v1.9.2
on windows_amd64


#get provider from azure from terraform application on windows. 

#create recource account
resource ""
