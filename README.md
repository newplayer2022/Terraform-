# Terraform 
#https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_network


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


#create storage account
resource "azurerm_storage_account" "storage_account" {
  name                     = "storageaccountname"
  resource_group_name      = "azurerm_resource_group.example.name"
  location                 = "azurerm_resource_group.example.location"
  account_tire             = "Standard"
  account_replication_type = "GRS"

#create container -- data storage---config file
all_blob_prublic_access = true

#creat a container
resource "azurerm_storage_container" "data" {
  name                   = "data"                         #name of the container
  storage_account_name   = "terraformstore1"              #name of the storage account
  container_access_type  = "private"                      #contain access level
}

#upload local file to container
resource "azurerm_storage_blob" "sample" {
  name                   = "sample.txt"
  storage_account_name   = ""
  storage_container_name = "data"
  type                   = "Block"
  source                 = "sample.txt"
}
#make sure it has correct dependencies, one resource is depended
depends_on = [
  azurerm_storage_container.data
]

#destroy resource 
terrforam destroy

#using variables before resource block
variable "storage_account_name" {
  type = "string"
  description = "please enter the storage account name"
}

#creat virtual network
resource "azurerm_network_security_group" "example_name" {
  name                = "example-security-group"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
  dns_servers         = ["10.0.0.4", "10.0.0.5"]

  subnet {
    name           = "subnetA"
    address_prefix = "10.0.1.0/24"
  }

  subnet {
    name           = "subnetB"
    address_prefix = "10.0.2.0/24"
    security_group = azurerm_network_security_group.example.id
  }

  tags = {
    environment = "Production"
  }
}

#creae virtual machine
resource "azurerm_windows_virtual_machine" "example" {
  name                = "example-machine"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  size                = "Standard_F2"
  admin_username      = "adminuser"
  admin_password      = "1111"
  network_interface_ids = [
    azurerm_network_interface.example.id,
  ]

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "MicrosoftWindowsServer"
    offer     = "WindowsServer"
    sku       = "2016-Datacenter"
    version   = "latest"
  }
}

#add public ip address
resource "azurerm_windows_virtual_machine" "app_vm" {
  name                     = "appvm"
  resource_group_name      = local.resource_group
  location                 = local.location
  size                     = "Standard_D2s_v3"
  admin_username           = "demousr"
  admin_pssword            = "xxx"
  network_interface_ids [
    azurerm_network_interface.app_interface.id,
  ]
}

#add data disk
resource "azurerm_managed_disk" "data_disk" {
  name                     = "data-disk"
  resource_group_name      = "local.resource_group"
  location                 = "local.location"
  storage_account_type     = "Standard_LRS"
  create_option            = "Empty"
  disk_size_gb             = 16
}

#attach the data disk to virtual machin
reource "azurerm_virtual_machine_data_disk_attachment" "disk_attach" {
  managed_disk_id         = azurerm_managed_disk.data_disk_id
  virtual_machine_id      = azurerm_windows_virtual_machine.app_vm.id
  lun                     = "0"
  chaching                = "Readwrite"
  depends_on = [
    azurerm_windows_virtual_machine.appvm
    azurerm_managed_disk.data_disk
  ]
}

#virtual machine need availablity set, this need to be created first, then the virtual machine,An availability set is a logical grouping of VMs that allows Azure to understand how your application is built to provide for redundancy and availability,Availability sets place VMs in different fault domains for better reliability,

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_availability_set" "example" {
  name                = "example-aset"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  #network security groups
  resource "azurerm_network_security_group" "example" {
  name                = "acceptanceTestSecurityGroup1"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  security_rule {
    name                       = "test123"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "*"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }


#storage cert, pw, key vault
provider "azurerm" {
  features {
    key_vault {
      purge_soft_delete_on_destroy    = true
      recover_soft_deleted_key_vaults = true
    }
  }
}

data "azurerm_client_config" "current" {}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "North America"
}

resource "azurerm_key_vault" "example" {
  name                        = "examplekeyvault"
  location                    = azurerm_resource_group.example.location
  resource_group_name         = azurerm_resource_group.example.name
  enabled_for_disk_encryption = true
  tenant_id                   = data.azurerm_client_config.current.tenant_id
  soft_delete_retention_days  = 7
  purge_protection_enabled    = false

  sku_name = "standard"

  access_policy {
    tenant_id = data.azurerm_client_config.current.tenant_id
    object_id = data.azurerm_client_config.current.object_id

    key_permissions = [
      "Get",
    ]

    secret_permissions = [
      "Get",
    ]

    storage_permissions = [
      "Get",
    ]
  }
}
resourace "azurerm_key_vault_secret" "vmpassword" {
  name                  = "vmpassword"
  value                 = "aaa123"
  key_valut_id          =  azurerm_key_vault.app_vault.id 
  depends_on            = [ azurerm_key_vault.app_vault ]
}


#linux vm, same as regular windows vm
change resource type, azurerm_linux_virtual_machine, linux_vm, username: linuxusr

admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
#disable_password_authentication - (Optional) Should Password Authentication be disabled on this Virtual Machine? Defaults to true. Changing this forces a new resource to be created.


#add web service
esource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_app_service_plan" "example" {
  name                = "example-appserviceplan"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  sku {
    tier = "Standard"                                            #how much a month, feature
    size = "S1"
  }
}

resource "azurerm_app_service" "example" {
  name                = "example-app-service"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  app_service_plan_id = azurerm_app_service_plan.example.id

  site_config {
    dotnet_framework_version = "v4.0"
    scm_type                 = "LocalGit"
  }

#other settings
  app_settings = {
    "SOME_KEY" = "some-value"
  }

  connection_string {
    name  = "Database"
    type  = "SQLServer"
    value = "Server=some-server.mydomain.com;Integrated Security=SSPI"
  }
}
