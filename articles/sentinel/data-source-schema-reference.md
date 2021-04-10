---
title: Odwołanie do schematu źródła danych wskaźnikowego platformy Azure
description: W tym artykule przedstawiono schemat źródła danych platformy Azure i innych firm, które są obsługiwane przez wskaźnik produktów platformy Azure, z linkami do dokumentacji referencyjnej.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 4601f2d6eddbbe8809dfd46a7e0cc5aa3c40c722
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209574"
---
# <a name="data-source-schema-reference"></a>Odwołanie do schematu źródła danych

W tym artykule przedstawiono obsługiwane schematy źródła danych platformy Azure i innych firm oraz linki do dokumentacji referencyjnej.

## <a name="azure-data-sources"></a>Źródła danych na platformie Azure

| Typ                             | Źródło danych             | Log Analytics tabeli | Odwołanie do schematu |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Właściwości logowania do raportów aktywności usługi Azure AD](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor odwołanie AuditLogs](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Informacje dotyczące Azure Monitor platformy Azure](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | Pakiet Office         | Schematy interfejsu API działania zarządzania pakietu Office 365: <br>- [Wspólny schemat ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Schemat administracyjny programu Exchange ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Schemat skrzynki pocztowej programu Exchange](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Schemat bazowy programu SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Operacje na plikach programu SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor odwołanie AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | Dziennik systemu                 | [Dokumentacja dziennika systemu Azure Monitor](/azure/azure-monitor/reference/tables/syslog) |
| **Sieć**                          | Dzienniki usług IIS               | W3CIISLog              | [Azure Monitor odwołanie W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Sieć**                          | VMinsights             | VMConnection           | [Azure Monitor odwołanie VMConnection](/azure/azure-monitor/reference/tables/vmconnection) |
| **Sieć**                          | Rozwiązanie do przesyłania danych     | Typowe               | [Azure Monitor odwołanie typowe](/azure/azure-monitor/reference/tables/wiredata) |
| **Sieć**                          | Dzienniki przepływu sieciowej grupy zabezpieczeń          | AzureNetworkAnalytics  | [Agregacja schematu i danych w Analiza ruchu](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Aby uzyskać więcej informacji, zapoznaj się z całym [Azure monitor odwołaniem do danych](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>źródła danych dostawców innych firm

W poniższej tabeli znajduje się lista obsługiwanych dostawców innych firm, ich dziennik systemowy lub CEF (Common Event format) dla różnych obsługiwanych typów dzienników, które zawierają mapowania pól CEF i dzienniki przykładowe dla każdego typu kategorii.

| Typ |    Dostawca |    Produkt | Log Analytics tabeli | CEF — odwołanie do mapowania pól  |
| ----- | ----- | ----- | ----- |----- |
| **Sieć** | Palo Alto   | KADRUJ SYSTEM OPERACYJNY    | CommonSecurityLog |   [Przewodnik integracji wspólnego formatu zdarzeń systemu Pan-OS 9,0](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (wyszukiwanie *formatów dzienników CEF*) |
| **Sieć** | Check Point  |ALL   | CommonSecurityLog | [Opis pól dziennika](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Sieć** | Fortigate   | ALL   | CommonSecurityLog | [Struktura schematu dziennika](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Sieć** | Barracuda | Web Application Firewall |  CommonSecurityLog   | [Jak skonfigurować dziennik systemowy i inne dzienniki](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Sieć** | Cisco | ASA | CommonSecurityLog | [Komunikaty dziennika systemowego z serii Cisco ASA](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Sieć** | Cisco | Firepower   | CommonSecurityLog | [Komunikaty dziennika systemowego ochrony Cisco Firepower Threat](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.html)    |
| **Sieć** | Cisco   | Macierzyst  | Tabela dzienników niestandardowych  | [Formaty i przechowywanie wersji dzienników](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Sieć**   | Cisco | Meraki    | CommonSecurityLog |   [Typy zdarzeń dziennika systemu i przykłady dzienników](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Sieć**   | Zscaler | Usługa przesyłania strumieniowego nano (NSS)|   CommonSecurityLog | [Formatowanie kanałów NSS](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (tylko w sieci Web, zapory, DNS i dzienników tunelu) |
| **Sieć**   |F5 | BigIP LTM|    CommonSecurityLog|  [Komunikaty o zdarzeniach i typy ataków](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Sieć** | F5  | BigIP ASM|    CommonSecurityLog|  [Rejestrowanie zdarzeń zabezpieczeń aplikacji](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Sieć** | Citrix  |Zapora aplikacji sieci Web   | CommonSecurityLog|    [Obsługa rejestrowania w formacie Common Event format (CEF) w zaporze aplikacji](https://support.citrix.com/article/CTX136146) <br>  [Dokumentacja dziennika systemowego 12,0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Ustawienia rejestrowania zewnętrznego i poziomów ważności zdarzeń dzienników dla Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |Wszystko |CommonSecurityLog | [Mapowanie zawartości dziennika systemowego — CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz również [Mapowanie pól CEF i CommonSecurityLog](cef-name-mapping.md).
> 
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o obsługiwanych łącznikach wskaźnikowych platformy Azure, takich jak CEF, dziennik systemowy, bezpośrednie, Agent i łączniki niestandardowe:

- [Łączenie ze źródłami danych](connect-data-sources.md)

- [System dziennika systemu Azure, CEF i inne łączniki innych firm](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)
