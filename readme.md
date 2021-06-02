
# Modulo - Application Gateway
[![Avanade](https://img.shields.io/badge/create%20by-Avanade-orange)](https://www.avanade.com/pt-br/about-avanade) [![HCL](https://img.shields.io/badge/language-HCL-blueviolet)](https://www.terraform.io/)
[![Azure](https://img.shields.io/badge/provider-Azure-blue)](https://registry.terraform.io/providers/hashicorp/azurerm/latest)

Modulo desenvolvido para facilitar a criação de Applications Gateways

## Compatibilidade de Versão

| Versão do Modulo | Versão Terraform | Versão AzureRM |
|----------------|-------------------| --------------- |
| >= 1.x.x       | 0.14.x            | >= 2.46         |

## Especificando versão

Para evitar que seu código receba atualizações automáticas do modulo, é preciso informar na chave `source` do bloco do module a versão desejada, utilizando o parametro `?ref=***` no final da url. conforme pode ser visto no exemplo abaixo.

## Exemplo de uso


```hcl
module "teste_app_gw" {
  source   = "git::https://github.com/mgsj2006/azurerm-module-app-gateway.git?ref=v1.0.0"
  name                  = "teste_app_gw"
  rg_name               = "resource_group"
  location              = "brazilsouth"
  pip_id                = data.azurerm_public_ip.pip.id
  frontend_ip_conf_name = "ateste_app_gw_FrontendIp"
  frontend_port_settings = [{
    name = "port_80"
    port = 80
  }]
  gw_ip_conf_name = "teste_app_gw_IpConfig"
  subnet_id       = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/..."
  appgw_http_listeners = [{
    name                           = "teste_app_gw_appservice"
    frontend_ip_configuration_name = "teste_app_gw_FrontendIp"
    frontend_port_name             = "port_80"
    host_name                      = "teste_app_gw.com.br"
    protocol                       = "Http"
    require_sni                    = false
  }]
  appgw_backend_pool = [{
    name = "teste_app_gw_appservice"
    fqdns = ["teste_app_gw.azurewebsites.net"]
  }]
  appgw_backend_http_settings = [{
     name                                = "teste_app_gw_appservice"
     port                                = 80
     affinity_cookie_name                = "ApplicationGatewayAffinity"
     cookie_based_affinity               = "Enabled"
     probe_name                          = "teste_app_gw_portal"
     protocol                            = "Http"
     request_timeout                     = 20
     pick_host_name_from_backend_address = true
  }]
  appgw_probe = [{
    interval                                  = 30
    minimum_servers                           = 0
    name                                      = "teste_app_gw_portal"
    pick_host_name_from_backend_http_settings = true
    protocol                                  = "Http"
    path                                      = "/"
    timeout                                   = 30
    unhealthy_threshold                       = 3
  }]
  appgw_routings = [{
    backend_address_pool_name  = "teste_app_gw_appservice"
    backend_http_settings_name = "teste_app_gw_appservice"
    http_listener_name         = "teste_app_gw_appservice"
    name                       = "teste_app_gw_appservice"
    rule_type                  = "Basic"
  }]
  #waf
  waf_mode = "Detection"
  request_body_check = true
  rule_set_type = "OWASP"
  #rule_set_version  = "3.2"
}
```

## Entrada de Valores

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| name | Nome dado ao recurso | `string` | n/a | yes |
| rg_name | nome do resource group onde os recursos serão alocados | `string` | n/a | yes |
| tags | Tags adicionais | `map(string)` | `{}` | No |
| location | Região do Azure | `string` | n/a | yes |
| sku | define o tipo de Gateway  | `string` | WAF_v2 | No |
| zone | * | `list(string)` | ["1","2","3"] | No |
| enable_http2 | * | `bool` | true | No |
| pip_id | * | `string` | n/a | yes |
| frontend_ip_conf_name | * | `string` | n/a | yes |
| frontend_port_settings | * | `list(map(string))` | n/a | yes |
| autoscaling_parameters | * | `map(string)` | null | No |
| appgw_backend_http_settings | * | `any` | n/a | yes |
| appgw_backend_pool | * | `any` | n/a | yes |
| appgw_http_listeners | * | `any` | n/a | yes |
| appgw_probe | * | `any` | n/a | yes |
| appgw_routings | * | `any` | n/a | yes |
| gw_ip_conf_name | * | `string` | n/a | yes |
| subnet_id | * | `string` | n/a | yes |
| enable_waf | * | `bool` | true | No |
| file_upload_limit_mb | * | `number` | 100 | No |
| waf_mode | * | `string` | Prevention | No |
| max_request_body_size_kb | * | `number` | 128 | No |
| request_body_check | * | `bool` | true | No |
| rule_set_type | * | `string` | OWASP | No |
| rule_set_version | * | `string` | 3.0 | yes |
| disabled_rule_group_settings | * | `list(object({rule_group_name = string rules = list(string)}))` | n/a | yes |
| disable_waf_rules_for_dev_portal | * | `bool` | false | No |
| waf_exclusion_settings | * | `list(map(string))` | [] | No |

## Saída de Valores

| Name | Description |
|------|-------------|
| ids | ID gerada para cada um dos grupos |

## Documentação de Referência

Terraform Application Gateway: [https://www.terraform.io/providers/azurerm/latest/docs/resources/application_gateway](https://www.terraform.io/providers/azurerm/latest/docs/resources/application_gateway)
