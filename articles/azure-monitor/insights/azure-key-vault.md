---
title: Rozwiązanie usługi Azure Key Vault w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą rozwiązania usługi Azure Key Vault w usłudze Azure Monitor można przejrzeć dzienniki usługi Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7da2fa2ddfbd9c71563dd8bd2e17b14c6dee62b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455465"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Rozwiązanie usługi Azure Key Vault Analytics w usłudze Azure Monitor

![Symbol przechowalni kluczy](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Za pomocą rozwiązania usługi Azure Key Vault w usłudze Azure Monitor można przejrzeć dzienniki zdarzeń inspekcji usługi Azure Key Vault.

Aby skorzystać z rozwiązania, należy włączyć rejestrowanie diagnostyki usługi Azure Key Vault i kierować diagnostykę do obszaru roboczego usługi Log Analytics. Nie jest konieczne, aby zapisać dzienniki do magazynu obiektów Blob platformy Azure.

> [!NOTE]
> W styczniu 2017 r. zmieniono obsługiwany sposób wysyłania dzienników z usługi Key Vault do usługi Log Analytics. Jeśli rozwiązanie usługi Key Vault, którego używasz, pokazuje *(przestarzałe)* w tytule, zapoznaj się [ze migracją ze starego rozwiązania usługi Key Vault,](#migrating-from-the-old-key-vault-solution) aby uzyskać kroki, które należy wykonać.
>
>

## <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Aby zainstalować i skonfigurować rozwiązanie usługi Azure Key Vault, skorzystaj z następujących instrukcji:

1. Użyj procesu opisanego w [add Azure Monitor solutions from the Solutions Gallery,](../../azure-monitor/insights/solutions.md) aby dodać rozwiązanie usługi Azure Key Vault do obszaru roboczego usługi Log Analytics.
2. Włącz rejestrowanie diagnostyki zasobów magazynu kluczy do monitorowania przy użyciu [portalu](#enable-key-vault-diagnostics-in-the-portal) lub [programu PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Włącz diagnostykę magazynu kluczy w portalu

1. W witrynie Azure portal przejdź do zasobu usługi Key Vault w celu monitorowania
2. Wybierz *ustawienia diagnostyki,* aby otworzyć następną stronę

   ![obraz kafelka usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kliknij *pozycję Włącz diagnostykę,* aby otworzyć następną stronę

   ![obraz kafelka usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Nadaj nazwę ustawieniu diagnostycznemu.
5. Kliknij pole wyboru *Wyślij do usługi Usługa Log Analytics*
6. Wybieranie istniejącego obszaru roboczego usługi Log Analytics lub tworzenie obszaru roboczego
7. Aby włączyć dzienniki *Inspekcji,* kliknij pole wyboru w obszarze Dziennik
8. Kliknij *przycisk Zapisz,* aby włączyć rejestrowanie diagnostyki w obszarze roboczym usługi Log Analytics.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Włączanie diagnostyki magazynu kluczy przy użyciu programu PowerShell
Następujący skrypt programu PowerShell zawiera przykład `Set-AzDiagnosticSetting` sposobu włączania rejestrowania zasobów dla usługi Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Przejrzyj szczegóły zbierania danych usługi Azure Key Vault
Rozwiązanie usługi Azure Key Vault zbiera dzienniki diagnostyczne bezpośrednio z usługi Key Vault.
Nie jest konieczne, aby zapisać dzienniki do magazynu obiektów Blob platformy Azure i nie agent jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych dla usługi Azure Key Vault.

| Platforma | Agent bezpośredni | Agent programu Systems Center Operations Manager | Azure | Wymagany jest program Operations Manager? | Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | po przyjeździe |

## <a name="use-azure-key-vault"></a>Korzystanie z rozwiązania Azure Key Vault
Po [zainstalowaniu rozwiązania](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)wyświetl dane usługi Key Vault, klikając kafelek **Analizy usługi Key Vault** na stronie **Przegląd** usługi Azure Monitor. Otwórz tę stronę z menu **Monitor platformy Azure,** klikając pozycję **Więcej** w sekcji **Insights.** 

![obraz kafelka usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Po kliknięciu kafelka **analizy usługi Key Vault Analytics** można wyświetlić podsumowania dzienników, a następnie przejść do szczegółów następujących kategorii:

* Wolumen wszystkich operacji magazynu kluczy w czasie
* Woluminy operacji nie powiodły się w czasie
* Średnie opóźnienie operacyjne w po operacji
* Jakość usług dla operacji z liczbą operacji, które zajmują więcej niż 1000 ms i lista operacji, które zajmują więcej niż 1000 ms

![obraz pulpitu nawigacyjnego usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![obraz pulpitu nawigacyjnego usługi Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Aby wyświetlić szczegółowe informacje o każdej operacji
1. Na stronie **Przegląd** kliknij kafelek **Analizy magazynu kluczy.**
2. Na pulpicie nawigacyjnym **usługi Azure Key Vault** przejrzyj informacje podsumowujące w jednym z bloków, a następnie kliknij jeden z nich, aby wyświetlić szczegółowe informacje o tym na stronie wyszukiwania dziennika.

    Na dowolnej stronie wyszukiwania dziennika można wyświetlać wyniki według czasu, szczegółowych wyników i historii wyszukiwania w dzienniku. Można również filtrować według aspektów, aby zawęzić wyniki.

## <a name="azure-monitor-log-records"></a>Rekordy dziennika usługi Azure Monitor
Rozwiązanie usługi Azure Key Vault analizuje rekordy, które mają typ **keyvaults,** które są zbierane z [dzienników Inspekcjiwydarzenia](../../key-vault/general/logging.md) w usłudze Azure Diagnostics.  Właściwości tych rekordów znajdują się w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Adres IP klienta, który złożył wniosek |
| `Category` | *Badanie audytu* |
| `CorrelationId` |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| `DurationMs` |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Ten czas nie obejmuje opóźnienia sieci, więc czas, który można zmierzyć po stronie klienta może nie odpowiadać tym razem. |
| `httpStatusCode_d` |Kod stanu HTTP zwrócony przez żądanie (na przykład *200*) |
| `id_s` |Unikatowy identyfikator żądania |
| `identity_claim_appid_g` | Identyfikator GUID dla identyfikatora aplikacji |
| `OperationName` |Nazwa operacji, zgodnie z dokumentami w [rejestrowaniu usługi Azure Key Vault](../../key-vault/general/logging.md) |
| `OperationVersion` |Wersja interfejsu API REST żądana przez klienta (na przykład *2015-06-01*) |
| `requestUri_s` |Uri żądania |
| `Resource` |Nazwa magazynu kluczy |
| `ResourceGroup` |Grupa zasobów magazynu kluczy |
| `ResourceId` |Identyfikator zasobu usługi Azure Resource Manager W przypadku dzienników usługi Key Vault jest to identyfikator zasobu magazynu kluczy. |
| `ResourceProvider` |*Microsoft. KLAWISZE KEYVAULT* |
| `ResourceType` | *Sklepienia* |
| `ResultSignature` |Stan HTTP (na przykład *OK)* |
| `ResultType` |Wynik żądania interfejsu API REST (na przykład *Sukces)* |
| `SubscriptionId` |Identyfikator subskrypcji platformy Azure w ramach subskrypcji zawierającej usługę Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migracja ze starego rozwiązania usługi Key Vault
W styczniu 2017 r. zmieniono obsługiwany sposób wysyłania dzienników z usługi Key Vault do usługi Log Analytics. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio w obszarze roboczym usługi Log Analytics bez konieczności używania konta magazynu
+ Mniejsze opóźnienia w stosunku do czasu generowania dzienników w usłudze Log Analytics
+ Mniej kroków konfiguracji
+ Typowy format dla wszystkich typów diagnostyki platformy Azure

Aby użyć zaktualizowanego rozwiązania:

1. [Konfigurowanie diagnostyki do wysyłania bezpośrednio do obszaru roboczego usługi Log Analytics z usługi Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Włącz rozwiązanie usługi Azure Key Vault przy użyciu procesu opisanego w [dodatku rozwiązań usługi Azure Monitor z Galerii rozwiązań rozwiązań](../../azure-monitor/insights/solutions.md)
3. Aktualizowanie wszystkich zapisanych zapytań, pulpitów nawigacyjnych lub alertów w celu użycia nowego typu danych
   + Typ jest zmiana z: KeyVaults do AzureDiagnostics. Za pomocą typu zasobu można filtrować do dzienników magazynu kluczy.
   + Zamiast: `KeyVaults`, użyj`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Pola: (w nazwach pól rozróżniana jest wielkość liter)
   + Dla każdego pola, które ma \_sufiks s, \_d lub \_g w nazwie, zmień pierwszy znak na małe litery
   + W przypadku każdego pola, które \_ma sufiks o nazwie, dane są dzielone na poszczególne pola na podstawie nazw pól zagnieżdżonych. Na przykład nazwa UPN osoby dzwoniącej jest przechowywana w polu`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Field CallerIpAddress zmieniono na CallerIPAddress
   + Pole RemoteIPCountry nie jest już obecny
4. Usuń rozwiązanie *analizy usługi Key Vault Analytics (przestarzałe).* Jeśli korzystasz z programu PowerShell, użyj`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Dane zebrane przed zmianą nie są widoczne w nowym rozwiązaniu. Można kontynuować kwerendę dla tych danych przy użyciu starych nazw typów i pól.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dziennika w usłudze Azure Monitor,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe dane usługi Azure Key Vault.
