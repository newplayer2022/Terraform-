# Terraform 
#Terraform concepts 

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

resource "" ""
