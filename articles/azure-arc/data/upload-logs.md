---
title: Przekazywanie dzienników do usługi Azure Monitor
description: Przekazywanie dzienników dla usług danych z obsługą usługi Azure Arc do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92378147"
---
# <a name="upload-logs-to-azure-monitor"></a>Przekazywanie dzienników do usługi Azure Monitor

Okresowo możesz wyeksportować dzienniki, a następnie przekazać je do platformy Azure. Eksportowanie i przekazywanie dzienników powoduje także utworzenie i zaktualizowanie danych na platformie Azure oraz zasobów grupy serwerów SQL i PostgreSQL.

> [!NOTE] 
> W okresie zapoznawczym nie ma kosztu korzystania z usług danych z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed przekazaniem dzienników należy: 

1. [Tworzenie obszaru roboczego usługi log Analytics](#create-a-log-analytics-workspace)
1. [Przypisz identyfikator i klucz współużytkowany do zmiennych środowiskowych](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi log Analytics

Aby utworzyć obszar roboczy usługi log Analytics, wykonaj następujące polecenia, aby utworzyć obszar roboczy Log Analytics i ustawić informacje o dostępie do zmiennych środowiskowych.

> [!NOTE]
> Pomiń ten krok, jeśli masz już obszar roboczy.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Przykładowe dane wyjściowe:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Przypisz identyfikator i klucz współużytkowany do zmiennych środowiskowych

Zapisz analizę obszaru roboczego dziennika `customerId` jako zmienną środowiskową do użycia później:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

To polecenie zwraca klucze dostępu wymagane do nawiązania połączenia z obszarem roboczym usługi log Analytics:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Przykładowe dane wyjściowe:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Zapisz klucz podstawowy w zmiennej środowiskowej, która ma zostać użyta później:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Ustaw końcowe zmienne środowiskowe i Potwierdź

Ustaw adres URL urzędu nazw SPN w zmiennej środowiskowej:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Weryfikuj zmienne środowiskowe

Upewnij się, że wszystkie wymagane zmienne środowiskowe są ustawione, jeśli chcesz:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Po ustawieniu zmiennych środowiskowych można przekazać dzienniki do obszaru roboczego dziennika. 

## <a name="upload-logs-to-azure-monitor"></a>Przekazywanie dzienników do usługi Azure Monitor

 Aby przekazać dzienniki dla wystąpień zarządzanych SQL z włączonym funkcją Azure Arc i AzureArc z włączonymi grupami serwerów PostgreSQL w celu uruchomienia następujących poleceń interfejsu wiersza polecenia —

1. Zaloguj się do kontrolera danych usługi Azure ARC przy użyciu programu [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Postępuj zgodnie z monitami, aby ustawić przestrzeń nazw, nazwę użytkownika i hasło administratora. 

1. Eksportuj wszystkie dzienniki do określonego pliku:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Przekaż dzienniki do obszaru roboczego usługi log Analytics w usłudze Azure Monitor:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Wyświetlanie dzienników w Azure Portal

Po przekazaniu dzienników powinno być możliwe wykonywanie do nich zapytań za pomocą eksploratora zapytań dziennika w następujący sposób:

1. Otwórz Azure Portal a następnie wyszukaj swój obszar roboczy według nazwy na pasku wyszukiwania u góry, a następnie wybierz go.
2. Wybierz pozycję Dzienniki w lewym panelu.
3. Wybierz pozycję Rozpocznij pracę (lub wybierz linki na stronie Wprowadzenie, aby dowiedzieć się więcej na temat Log Analytics, jeśli jesteś nowym).
4. Postępuj zgodnie z samouczkiem, aby dowiedzieć się więcej na temat Log Analytics, jeśli używasz Log Analytics.
5. Rozwiń pozycję Dzienniki niestandardowe u dołu listy tabel, aby zobaczyć tabelę o nazwie „sql_instance_logs_CL”.
6. Wybierz ikonę "oczu" obok nazwy tabeli.
7. Wybierz przycisk "Wyświetl w edytorze zapytań".
8. W edytorze zapytań znajdziesz teraz zapytanie, które będzie zawierać najnowsze 10 zdarzeń w dzienniku.
9. W tym miejscu możesz poeksperymentować z wykonywaniem zapytań w dziennikach przy użyciu edytora zapytań, ustawianiem alertów itd.

## <a name="automating-uploads-optional"></a>Automatyzacja przekazywania (opcjonalnie)

Jeśli chcesz przekazać metryki i dzienniki zgodnie z harmonogramem, możesz utworzyć skrypt i uruchomić go w czasomierzu co kilka minut. Poniżej znajduje się przykład automatyzacji przekazywania przy użyciu skryptu powłoki systemu Linux.

W ulubionym edytorze tekstu/kodu Dodaj następujący skrypt do pliku i Zapisz jako plik wykonywalny skryptu, taki jak. sh (Linux/Mac) lub. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Utwórz plik skryptu wykonywalnego

```console
chmod +x myuploadscript.sh
```

Uruchom skrypt co 20 minut:

```console
watch -n 1200 ./myuploadscript.sh
```

Można również użyć harmonogramu zadań, takiego jak CRONUS lub Windows Harmonogram zadań lub do programu Orchestrator, takiego jak rozwiązania ansible, Puppet lub Chef.

## <a name="next-steps"></a>Następne kroki

[Przekazywanie metryk i dzienników do Azure Monitor](upload-metrics.md)

[Przekazywanie danych użycia, metryk i dzienników do Azure Monitor](upload-usage-data.md)

[Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal](view-billing-data-in-azure.md)

[Wyświetlanie zasobu usługi Azure Arc Data Controller w Azure Portal](view-data-controller-in-azure-portal.md)
