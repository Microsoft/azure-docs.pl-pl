---
title: Azure Key Vault rozwiązanie w Azure Monitor | Microsoft Docs
description: Aby przejrzeć dzienniki Azure Key Vault, można użyć rozwiązania Azure Key Vault w Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 24869841a106a68234cfec600d9a98e519f8c6cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734200"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Rozwiązanie Azure Key Vault Analytics w Azure Monitor

> [!NOTE]
> To rozwiązanie jest przestarzałe. [Zalecamy korzystanie z Azure Monitor Key Vault](./key-vault-insights-overview.md).

![Symbol Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Możesz użyć rozwiązania Azure Key Vault w Azure Monitor do Azure Key Vault przeglądania dzienników AuditEvent.

Aby użyć rozwiązania, należy włączyć rejestrowanie diagnostyki Azure Key Vault i skierować diagnostykę do Log Analytics obszaru roboczego. Nie jest konieczne zapisywanie dzienników w usłudze Azure Blob Storage.

> [!NOTE]
> W styczniu 2017 obsługiwany sposób wysyłania dzienników z Key Vault do Log Analytics zmieniony. Jeśli używane rozwiązanie Key Vault jest widoczne *(przestarzałe)* w tytule, zapoznaj się z artykułem [Migrowanie ze starego rozwiązania Key Vault](#migrating-from-the-old-key-vault-solution) , aby zapoznać się z krokami, które należy wykonać.
>
>

## <a name="install-and-configure-the-solution"></a>Instalowanie i Konfigurowanie rozwiązania
Aby zainstalować i skonfigurować rozwiązanie Azure Key Vault, należy wykonać następujące instrukcje:

1. Aby dodać rozwiązanie Azure Key Vault do obszaru roboczego Log Analytics, Użyj procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](./solutions.md) .
2. Włącz rejestrowanie diagnostyczne dla zasobów Key Vault do monitorowania przy użyciu [portalu](#enable-key-vault-diagnostics-in-the-portal) lub [programu PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Włączanie diagnostyki Key Vault w portalu

1. W Azure Portal przejdź do zasobu Key Vault do monitorowania
2. Wybierz pozycję *Ustawienia diagnostyczne* , aby otworzyć następującą stronę

   ![Zrzut ekranu przedstawiający stronę ustawień diagnostyki dla Key Vault zasobów ContosoKVSCUS. opcja włączenia diagnostyki jest wyróżniona.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kliknij pozycję *Włącz diagnostykę* , aby otworzyć następującą stronę

   ![Zrzut ekranu przedstawiający stronę konfigurowania ustawień diagnostycznych. Wybrane są opcje Wyślij do Log Analytics, dziennik AuditEvent i AllMetrics.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Nadaj nazwę dla ustawienia diagnostycznego.
5. Kliknij pole wyboru *Wyślij do log Analytics*
6. Wybierz istniejący obszar roboczy Log Analytics lub Utwórz obszar roboczy
7. Aby włączyć dzienniki *AuditEvent* , kliknij pole wyboru w obszarze Dziennik.
8. Kliknij przycisk *Zapisz* , aby włączyć rejestrowanie diagnostyki w log Analytics obszarze roboczym.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Włączanie diagnostyki Key Vault przy użyciu programu PowerShell
Poniższy skrypt programu PowerShell zawiera przykład użycia `Set-AzDiagnosticSetting` programu w celu włączenia rejestrowania zasobów dla Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Przejrzyj szczegóły zbierania danych Azure Key Vault
Azure Key Vault rozwiązanie zbiera dzienniki diagnostyczne bezpośrednio z Key Vault.
Nie jest konieczne zapisywanie dzienników w usłudze Azure Blob Storage, a agent nie jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych oraz inne szczegóły dotyczące sposobu zbierania danych dla Azure Key Vault.

| Platforma | Agent bezpośredni | Agent programu System Center Operations Manager | Azure | Operations Manager jest wymagane? | Dane agenta Operations Manager wysyłane przez grupę zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | przy nadejściu |

## <a name="use-azure-key-vault"></a>Korzystanie z rozwiązania Azure Key Vault
Po [zainstalowaniu rozwiązania](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview)Wyświetl Key Vault dane, klikając kafelek **Key Vault Analytics** na stronie **Przegląd** Azure monitor. Otwórz Tę stronę z menu **Azure monitor** , klikając pozycję **więcej** w sekcji **szczegółowe informacje** . 

![Zrzut ekranu kafelka Key Vault Analytics na stronie Przegląd Azure Monitor przedstawiający wykres wolumenu operacji magazynu kluczy w czasie.](media/azure-key-vault/log-analytics-keyvault-tile.png)

Po kliknięciu kafelka **Key Vault Analytics** można wyświetlić podsumowania dzienników, a następnie przejść do szczegółów dla następujących kategorii:

* Wolumin wszystkich operacji magazynu kluczy w czasie
* Woluminy operacji zakończonych niepowodzeniem w czasie
* Średnie opóźnienie operacyjne według operacji
* Jakość usługi dla operacji z liczbą operacji, które mają więcej niż 1000 MS i listę operacji, które mają więcej niż 1000 MS

![Zrzut ekranu pulpitu nawigacyjnego Azure Key Vault przedstawiający kafelki z danymi graficznymi dotyczącymi wszystkich operacji, operacji zakończonych niepowodzeniem i średniego opóźnienia operacyjnego.](media/azure-key-vault/log-analytics-keyvault01.png)

![Zrzut ekranu przedstawiający pulpit nawigacyjny Azure Key Vault pokazujący kafelki z danymi na potrzeby średniego opóźnienia operacyjnego, Quality of Service i zalecanych wyszukiwań.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Aby wyświetlić szczegóły dla każdej operacji
1. Na stronie **Przegląd** kliknij kafelek **Key Vault Analytics** .
2. Na pulpicie nawigacyjnym **Azure Key Vault** Przejrzyj informacje podsumowujące w jednej z bloków, a następnie kliknij jeden, aby wyświetlić szczegółowe informacje na jego temat na stronie przeszukiwanie dzienników.

    Na dowolnej stronie przeszukiwania dzienników można wyświetlać wyniki według czasu, szczegółowych wyników i historii przeszukiwania dzienników. Możesz również filtrować według aspektów, aby zawęzić wyniki.

## <a name="azure-monitor-log-records"></a>Azure Monitor rekordy dziennika
Azure Key Vault rozwiązanie analizuje rekordy, które mają typy **magazynów** kluczy, które są zbierane z [dzienników AuditEvent](../../key-vault/general/logging.md) w Diagnostyka Azure.  Właściwości tych rekordów znajdują się w poniższej tabeli:  

| Właściwość | Opis |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Adres IP klienta, który wykonał żądanie |
| `Category` | *AuditEvent* |
| `CorrelationId` |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| `DurationMs` |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Ta godzina nie obejmuje opóźnienia sieci, dlatego czas pomiaru po stronie klienta może nie być zgodny z tym czasem. |
| `httpStatusCode_d` |Kod stanu HTTP zwrócony przez żądanie (na przykład *200*) |
| `id_s` |Unikatowy identyfikator żądania |
| `identity_claim_appid_g` | Identyfikator GUID dla identyfikatora aplikacji |
| `OperationName` |Nazwa operacji, zgodnie z opisem w [Azure Key Vault rejestrowania](../../key-vault/general/logging.md) |
| `OperationVersion` |Wersja interfejsu API REST żądana przez klienta (na przykład *2015-06-01*) |
| `requestUri_s` |Identyfikator URI żądania |
| `Resource` |Nazwa magazynu kluczy |
| `ResourceGroup` |Grupa zasobów magazynu kluczy |
| `ResourceId` |Identyfikator zasobu usługi Azure Resource Manager W przypadku dzienników Key Vault jest to Key Vault identyfikator zasobu. |
| `ResourceProvider` |*Programu. KEYVAULT* |
| `ResourceType` | *MAGAZYNÓW* |
| `ResultSignature` |Stan HTTP (na przykład *OK*) |
| `ResultType` |Wynik żądania interfejsu API REST (na przykład *sukcesu*) |
| `SubscriptionId` |Identyfikator subskrypcji platformy Azure dla subskrypcji zawierającej Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrowanie ze starego rozwiązania Key Vault
W styczniu 2017 obsługiwany sposób wysyłania dzienników z Key Vault do Log Analytics zmieniony. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio w obszarze roboczym Log Analytics bez konieczności korzystania z konta magazynu
+ Mniejsze opóźnienie od momentu, w którym dzienniki są generowane w celu udostępnienia w Log Analytics
+ Mniej czynności konfiguracyjnych
+ Typowy format dla wszystkich typów diagnostyki platformy Azure

Aby użyć zaktualizowanego rozwiązania:

1. [Skonfiguruj diagnostykę do wysłania bezpośrednio do obszaru roboczego Log Analytics z Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Włącz rozwiązanie Azure Key Vault przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](./solutions.md)
3. Zaktualizuj wszystkie zapisane zapytania, pulpity nawigacyjne lub alerty, aby użyć nowego typu danych
   + Typ jest zmieniany z: magazyny kluczy do AzureDiagnostics. Do filtrowania dzienników Key Vault można użyć elementu ResourceType.
   + Zamiast: `KeyVaults` , użyj `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Pola: (nazwy pól są rozróżniane wielkości liter)
   + Dla każdego pola, które ma sufiks \_ s, \_ d lub \_ g w nazwie, Zmień pierwszy znak na małe litery
   + Dla każdego pola, które ma sufiks \_ o wartości w nazwie, dane są dzielone na poszczególne pola na podstawie zagnieżdżonych nazw pól. Na przykład nazwa UPN obiektu wywołującego jest przechowywana w polu `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Zmieniono CallerIpAddress pola na CallerIPAddress
   + Pole RemoteIPCountry nie jest już obecne
4. Usuń rozwiązanie *Key Vault Analytics (przestarzałe)* . Jeśli używasz programu PowerShell, użyj polecenia `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Dane zbierane przed zmianą nie będą widoczne w nowym rozwiązaniu. Można kontynuować zapytania o te dane przy użyciu starych typów i nazw pól.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dzienników w Azure monitor](../logs/log-query-overview.md) , aby wyświetlić szczegółowe dane Azure Key Vault.

