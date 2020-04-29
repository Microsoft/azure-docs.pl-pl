---
title: Zasoby Azure Automation w rozwiązaniach do zarządzania | Microsoft Docs
description: Rozwiązania do zarządzania zwykle obejmują elementy Runbook w Azure Automation do automatyzowania procesów, takich jak gromadzenie i przetwarzanie danych monitorowania.  W tym artykule opisano, jak uwzględnić elementy Runbook i powiązane z nimi zasoby w rozwiązaniu.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef9f27546e9db95d5a41769e1b5bc7bc0c2f851
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663066"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Dodawanie Azure Automation zasobów do rozwiązania do zarządzania (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja dotycząca tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.   


[Rozwiązania do zarządzania]( solutions.md) zwykle obejmują elementy runbook w Azure Automation do automatyzowania procesów, takich jak gromadzenie i przetwarzanie danych monitorowania.  Oprócz elementów Runbook konta usługi Automation zawierają zasoby, takie jak zmienne i harmonogramy obsługujące elementy Runbook używane w rozwiązaniu.  W tym artykule opisano, jak uwzględnić elementy Runbook i powiązane z nimi zasoby w rozwiązaniu.

> [!NOTE]
> W przykładach w tym artykule są używane parametry i zmienne, które są wymagane lub wspólne dla rozwiązań do zarządzania, a także opisane w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz już poniższe informacje.

- Jak [utworzyć rozwiązanie do zarządzania]( solutions-creating.md).
- Struktura [pliku rozwiązania]( solutions-solution-file.md).
- Jak [tworzyć szablony Menedżer zasobów](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Konto usługi Automation
Wszystkie zasoby w Azure Automation są zawarte na [koncie usługi Automation](../../automation/automation-security-overview.md#automation-account-overview).  Zgodnie z opisem w [obszarze roboczym log Analytics i koncie usługi Automation]( solutions.md#log-analytics-workspace-and-automation-account) konto usługi Automation nie jest dołączone do rozwiązania do zarządzania, ale musi istnieć przed zainstalowaniem rozwiązania.  Jeśli nie jest dostępny, instalacja rozwiązania zakończy się niepowodzeniem.

Nazwa każdego zasobu usługi Automation obejmuje nazwę konta usługi Automation.  Jest to realizowane w rozwiązaniu z parametrem **AccountName** , jak w poniższym przykładzie zasobu elementu Runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Elementy Runbook
Należy uwzględnić wszystkie elementy Runbook używane przez rozwiązanie w pliku rozwiązania, aby zostały utworzone po zainstalowaniu rozwiązania.  Nie można w tym szablonie umieścić treści elementu Runbook, dlatego należy opublikować element Runbook w lokalizacji publicznej, w której będzie on dostępny dla dowolnego użytkownika instalującego Twoje rozwiązanie.

[Azure Automation zasobów elementu Runbook](../../automation/automation-runbook-types.md) ma typ **Microsoft. Automation/automationAccounts/Runbook** i ma następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Właściwości elementów Runbook są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| runbookType |Określa typy elementów Runbook. <br><br> Skrypt — skrypt programu PowerShell <br>PowerShell — przepływ pracy programu PowerShell <br> GraphPowerShell — element Runbook skryptu programu PowerShell <br> GraphPowerShellWorkflow — element Runbook przepływu pracy programu PowerShell |
| logProgress |Określa, czy [rekordy postępu](../../automation/automation-runbook-output-and-messages.md) mają być generowane dla elementu Runbook. |
| logVerbose |Określa, czy dla elementu Runbook mają być generowane [pełne rekordy](../../automation/automation-runbook-output-and-messages.md) . |
| description |Opcjonalny opis elementu Runbook. |
| publishContentLink |Określa zawartość elementu Runbook. <br><br>Identyfikator URI-URI do zawartości elementu Runbook.  Będzie to plik. ps1 dla programu PowerShell i elementów Runbook skryptów oraz wyeksportowany plik graficzny elementu Runbook dla elementu Runbook programu Graph.  <br> wersja elementu Runbook dla własnego śledzenia. |


## <a name="automation-jobs"></a>Zadania automatyzacji
Po uruchomieniu elementu Runbook w Azure Automation zostanie utworzone zadanie automatyzacji.  Do rozwiązania można dodać zasób zadania usługi Automation, aby automatycznie uruchomić element Runbook, gdy zainstalowano rozwiązanie do zarządzania.  Ta metoda jest zwykle używana do uruchamiania elementów Runbook, które są używane do wstępnej konfiguracji rozwiązania.  Aby uruchomić element Runbook w regularnych odstępach czasu, Utwórz [harmonogram](#schedules) i [harmonogram zadań](#job-schedules)

Zasoby zadań mają typ **Microsoft. Automation/automationAccounts/Jobs** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

W poniższej tabeli opisano właściwości zadań automatyzacji.

| Właściwość | Opis |
|:--- |:--- |
| element Runbook |Jednostka o pojedynczej nazwie o nazwie elementu Runbook do uruchomienia. |
| parameters |Jednostka dla każdej wartości parametru wymaganej przez element Runbook. |

Zadanie zawiera nazwę elementu Runbook i wszystkie wartości parametrów do wysłania do elementu Runbook.  Zadanie powinno [zależeć od]( solutions-solution-file.md#resources) elementu Runbook, który jest uruchamiany od momentu utworzenia elementu Runbook przed zadaniem.  Jeśli masz wiele elementów Runbook, które należy uruchomić, możesz zdefiniować ich kolejność, aby zadanie zależało od innych zadań, które powinny być uruchamiane jako pierwsze.

Nazwa zasobu zadania musi zawierać identyfikator GUID, który zwykle jest przypisywany przez parametr.  Więcej informacji na temat parametrów GUID można znaleźć w temacie [Tworzenie pliku rozwiązania do zarządzania na platformie Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certyfikaty
[Certyfikaty Azure Automation](../../automation/automation-certificates.md) mają typ **Microsoft. Automation/automationAccounts/Certificates** i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



Właściwości zasobów certyfikatów są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| base64Value |Podstawowa wartość 64 dla certyfikatu. |
| odcisk palca |Odcisk palca certyfikatu. |



## <a name="credentials"></a>Poświadczenia
[Poświadczenia Azure Automation](../../automation/automation-credentials.md) mają typ **Microsoft. Automation/automationAccounts/Credentials** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

Właściwości zasobów poświadczeń są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| userName |Nazwa użytkownika dla poświadczenia. |
| hasło |Hasło dla poświadczenia. |


## <a name="schedules"></a>Harmonogramy
[Harmonogramy Azure Automation](../../automation/automation-schedules.md) mają typ **Microsoft. Automation/automationAccounts/** Schedules i mają następującą strukturę. Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Właściwości zasobów harmonogramu są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| description |Opcjonalny opis harmonogramu. |
| startTime |Określa godzinę rozpoczęcia harmonogramu jako obiekt DateTime. Ciąg można podać, jeśli można go przekonwertować na prawidłową datę i godzinę. |
| isEnabled |Określa, czy harmonogram jest włączony. |
| interval |Typ interwału harmonogramu.<br><br>dzień<br>godzina |
| frequency |Częstotliwość, z jaką harmonogram powinien zostać uruchomiony w ciągu kilku dni lub godzin. |

Harmonogramy muszą mieć czas rozpoczęcia o wartości większej niż bieżąca godzina.  Nie można podać tej wartości za pomocą zmiennej, ponieważ nie ma możliwości znajomości, kiedy ma być zainstalowana.

Użyj jednej z następujących dwóch strategii podczas korzystania z harmonogramu zasobów w rozwiązaniu.

- Użyj parametru czasu rozpoczęcia harmonogramu.  Spowoduje to wyświetlenie monitu o podanie wartości podczas instalacji rozwiązania.  Jeśli masz wiele harmonogramów, możesz użyć jednej wartości parametru dla więcej niż jednego z nich.
- Utwórz harmonogramy przy użyciu elementu Runbook, który jest uruchamiany, gdy rozwiązanie zostanie zainstalowane.  Spowoduje to usunięcie wymagania użytkownika w celu określenia czasu, ale nie może zawierać harmonogramu w rozwiązaniu, więc zostanie usunięte po usunięciu rozwiązania.


### <a name="job-schedules"></a>Harmonogramy zadań
Zasoby harmonogramu zadań łączą element Runbook z harmonogramem.  Mają one typ **Microsoft. Automation/automationAccounts/jobSchedules** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


W poniższej tabeli opisano właściwości harmonogramów zadań.

| Właściwość | Opis |
|:--- |:--- |
| Nazwa harmonogramu |Jednostka o pojedynczej **nazwie** o nazwie harmonogramu. |
| Nazwa elementu Runbook  |Jednostka o pojedynczej **nazwie** o nazwie elementu Runbook.  |



## <a name="variables"></a>Zmienne
[Zmienne Azure Automation](../../automation/automation-variables.md) mają typ **Microsoft. Automation/automationAccounts/zmienne** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

Właściwości zasobów zmiennych są opisane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| description | Opcjonalny opis zmiennej. |
| isEncrypted | Określa, czy zmienna powinna być szyfrowana. |
| type | Ta właściwość obecnie nie ma żadnego wpływu.  Typ danych zmiennej zostanie określony przez wartość początkową. |
| value | Wartość dla zmiennej. |

> [!NOTE]
> Właściwość **Type** nie ma obecnie wpływu na utworzoną zmienną.  Typ danych dla zmiennej zostanie określony przez wartość.  

Jeśli ustawisz początkową wartość dla zmiennej, należy ją skonfigurować jako prawidłowy typ danych.  W poniższej tabeli przedstawiono różne typy danych, które są dozwolone i ich składni.  Należy pamiętać, że wartości w formacie JSON powinny być zawsze ujęte w cudzysłowy ze wszystkimi znakami specjalnymi w cudzysłowie.  Na przykład wartość ciągu może być określona przez cudzysłowy wokół ciągu (przy użyciu znaku ucieczki (\\)), podczas gdy wartość liczbowa zostanie określona z jednym zestawem cudzysłowów.

| Typ danych | Opis | Przykład | Jest rozpoznawana jako |
|:--|:--|:--|:--|
| ciąg   | Ujmij wartość w podwójne cudzysłowy.  | "\"Hello World\"" | "Hello World" |
| numeryczne  | Wartość liczbowa z pojedynczymi cudzysłowami.| "64" | 64 |
| wartość logiczna  | **wartość true** lub **false** w cudzysłowie.  Należy pamiętać, że ta wartość musi być małymi literami. | oznacza | true |
| datetime | Serializowana wartość daty.<br>Aby wygenerować tę wartość dla konkretnej daty, można użyć polecenia cmdlet ConvertTo-JSON w programie PowerShell.<br>Przykład: Get-Date "5/24/2017 13:14:57" \| ConvertTo-JSON | "\\/Date (1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduły
Twoje rozwiązanie do zarządzania nie musi definiować [modułów globalnych](../../automation/automation-integration-modules.md) używanych przez elementy Runbook, ponieważ będą zawsze dostępne na koncie usługi Automation.  Musisz dołączyć zasób dla dowolnego innego modułu używanego przez elementy Runbook.

[Moduły integracji](../../automation/automation-integration-modules.md) mają typ **Microsoft. Automation/automationAccounts/moduły** i mają następującą strukturę.  Obejmuje to typowe zmienne i parametry, dzięki czemu można skopiować i wkleić fragment kodu do pliku rozwiązania i zmienić nazwy parametrów.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


W poniższej tabeli opisano właściwości zasobów modułu.

| Właściwość | Opis |
|:--- |:--- |
| contentLink |Określa zawartość modułu. <br><br>Identyfikator URI-URI do zawartości modułu.  Będzie to plik. ps1 dla programu PowerShell i elementów Runbook skryptów oraz wyeksportowany plik graficzny elementu Runbook dla elementu Runbook programu Graph.  <br> wersja — wersja modułu do własnego śledzenia. |

Element Runbook powinien zależeć od zasobu modułu, aby upewnić się, że został utworzony przed elementem Runbook.

### <a name="updating-modules"></a>Aktualizowanie modułów
W przypadku zaktualizowania rozwiązania do zarządzania, które zawiera element Runbook, który korzysta z harmonogramu, a nowa wersja rozwiązania ma nowy moduł używany przez ten element Runbook, element Runbook może używać starej wersji modułu.  W rozwiązaniu należy uwzględnić następujące elementy Runbook i utworzyć zadanie uruchamiania ich przed innymi elementami Runbook.  Dzięki temu wszystkie moduły zostaną zaktualizowane zgodnie z wymaganiami przed załadowaniem elementów Runbook.

* [Aktualizacja-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) zapewnia najnowszą wersję wszystkich modułów używanych przez elementy Runbook w rozwiązaniu.  
* [ReRegisterAutomationSchedule — funkcja zarządzania usługą MS](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) rejestruje wszystkie zasoby harmonogramu, aby upewnić się, że elementy Runbook są z nimi powiązane.




## <a name="sample"></a>Przykład
Poniżej znajduje się przykład rozwiązania, które obejmuje następujące zasoby:

- Runbook.  To jest przykładowy element Runbook przechowywany w publicznym repozytorium GitHub.
- Zadanie automatyzacji, które uruchamia element Runbook po zainstalowaniu rozwiązania.
- Harmonogram i harmonogram zadań, aby uruchomić element Runbook w regularnych odstępach czasu.
- Certyfikatu.
- Poświadczeń.
- Zmiennej.
- Elementu.  Jest to [moduł OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) służący do zapisywania danych w log Analytics. 

W przykładzie użyto [standardowych zmiennych parametrów rozwiązania]( solutions-solution-file.md#parameters) , które zwykle są używane w rozwiązaniu, a nie do wartości zakodowana w definicjach zasobów.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Następne kroki
* [Dodaj widok do rozwiązania]( solutions-resources-views.md) , aby wizualizować zebrane dane.
