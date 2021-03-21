---
title: Ustawienia reguły wykrywania inteligentnego — Application Insights platformy Azure
description: Automatyzowanie zarządzania i konfiguracji Application Insights reguł wykrywania inteligentnego na platformie Azure przy użyciu szablonów Azure Resource Manager
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726125"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Zarządzanie Application Insights regułami wykrywania inteligentnego przy użyciu szablonów Azure Resource Manager

Reguły inteligentnego wykrywania w Application Insights mogą być zarządzane i konfigurowane przy użyciu [szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
Tej metody można użyć podczas wdrażania nowych zasobów Application Insights z automatyzacją Azure Resource Manager lub do modyfikowania ustawień istniejących zasobów.

## <a name="smart-detection-rule-configuration"></a>Konfiguracja reguły wykrywania inteligentnego

Dla reguły wykrywania inteligentnego można skonfigurować następujące ustawienia:
- Jeśli reguła jest włączona (wartość domyślna to **true**).
- Jeśli wiadomości e-mail powinny być wysyłane do użytkowników skojarzonych z [czytnikiem monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) subskrypcji i [monitorować role współautor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) po znalezieniu wykrywania (wartość domyślna to **true**).
- Wszyscy dodatkowi Adresaci poczty e-mail, którzy powinni otrzymać powiadomienie po znalezieniu wykrywania.
    -  Konfiguracja poczty e-mail nie jest dostępna dla reguł wykrywania inteligentnego oznaczonych jako _wersja zapoznawcza_.

Aby umożliwić Konfigurowanie ustawień reguły za pośrednictwem Azure Resource Manager, Konfiguracja reguły wykrywania inteligentnego jest teraz dostępna jako zasób wewnętrzny w ramach zasobu Application Insights o nazwie **ProactiveDetectionConfigs**.
W celu uzyskania maksymalnej elastyczności Każda reguła wykrywania inteligentnego może być skonfigurowana przy użyciu unikatowych ustawień powiadomień.

## <a name="examples"></a>Przykłady

Poniżej przedstawiono kilka przykładów, które pokazują, jak skonfigurować ustawienia reguł wykrywania inteligentnego za pomocą szablonów Azure Resource Manager.
Wszystkie przykłady odnoszą się do zasobu Application Insights o nazwie _"moja aplikacja"_ i do "reguły wykrywania" długiego czasu trwania zależności ", która jest wewnętrznie nazywana _" longdependencyduration "_.
Pamiętaj, aby zastąpić nazwę zasobu Application Insights i określić odpowiednią nazwę wewnętrzną reguły wykrywania inteligentnego. Zapoznaj się z poniższą tabelą, aby wyświetlić listę odpowiednich wewnętrznych nazw Azure Resource Manager dla każdej reguły wykrywania inteligentnego.

### <a name="disable-a-smart-detection-rule"></a>Wyłącz regułę wykrywania inteligentnego

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Wyłącz wysyłanie powiadomień e-mail dla reguły wykrywania inteligentnego

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Dodawanie dodatkowych odbiorców poczty e-mail dla reguły wykrywania inteligentnego

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Nazwy reguł wykrywania inteligentnego

Poniżej znajduje się tabela nazw reguł wykrywania inteligentnego, która pojawia się w portalu wraz z nazwami wewnętrznymi, które powinny być używane w szablonie Azure Resource Manager.

> [!NOTE]
> Reguły inteligentnego wykrywania oznaczone jako _wersja zapoznawcza_ nie obsługują powiadomień e-mail. W związku z tym można ustawić tylko właściwość _Enabled_ dla tych reguł. 

| Nazwa reguły Azure Portal | Nazwa wewnętrzna
|:---|:---|
| Długi czas ładowania strony | slowpageloadtime |
| Wolny czas odpowiedzi serwera | slowserverresponsetime |
| Czas trwania długiej zależności | longdependencyduration |
| Spadek czasu odpowiedzi serwera | degradationinserverresponsetime |
| Pogorszenie czasu trwania zależności | degradationindependencyduration |
| Spadek współczynnika ważności śledzenia (wersja zapoznawcza) | extension_traceseveritydetector |
| Nietypowy wzrost w woluminie wyjątku (wersja zapoznawcza) | extension_exceptionchangeextension |
| Wykryto Potencjalny przeciek pamięci (wersja zapoznawcza) | extension_memoryleakextension |
| Wykryto potencjalny problem z zabezpieczeniami (wersja zapoznawcza) | extension_securityextensionspackage |
| Nietypowy wzrost ilości danych dziennych (wersja zapoznawcza) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Reguła alertu dotyczącego anomalii niepowodzeń

Ten szablon Azure Resource Manager ilustruje konfigurowanie reguły alertów o anomalii awarii o ważności 2.

> [!NOTE]
> Anomalie błędów to usługa globalna, dlatego w lokalizacji globalnej jest tworzona lokalizacja reguły.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Ten szablon Azure Resource Manager jest unikatowy dla reguły alertu dotyczącego anomalii awarii i różni się od innych klasycznych reguł wykrywania inteligentnego opisanych w tym artykule. Jeśli chcesz zarządzać anomaliami błędów ręcznie, odbywa się to w Azure Monitor alertach, a wszystkie inne reguły wykrywania inteligentnego są zarządzane w okienku Inteligentne wykrywanie interfejsu użytkownika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat automatycznego wykrywania:

- [Anomalie błędów](./proactive-failure-diagnostics.md)
- [Przecieki pamięci](./proactive-potential-memory-leak.md)
- [Anomalie wydajności](./proactive-performance-diagnostics.md)

