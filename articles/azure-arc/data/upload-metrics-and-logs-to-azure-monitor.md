---
title: Przekazywanie spisu zasobów, danych użycia, metryk i dzienników do Azure Monitor
description: Przekazywanie spisu zasobów, danych użycia, metryk i dzienników do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939106"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Przekazywanie spisu zasobów, danych użycia, metryk i dzienników do Azure Monitor

Dzięki usłudze Azure Arc Data Services możesz *Opcjonalnie* przekazać metryki i dzienniki do Azure monitor, aby można było agregować i analizować metryki, dzienniki, zgłaszać alerty, wysyłać powiadomienia lub wyzwalać zautomatyzowane akcje. Wysyłanie danych do Azure Monitor umożliwia również przechowywanie danych monitorowania i dzienników poza lokacją oraz na dużą skalę w celu umożliwienia długoterminowego przechowywania danych na potrzeby zaawansowanej analizy.  Jeśli masz wiele witryn z usługą Azure Arc Data Services, możesz użyć Azure Monitor jako centralnej lokalizacji, aby zebrać wszystkie dzienniki i metryki w witrynach.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Istnieje kilka jednorazowych kroków konfiguracji wymaganych do włączenia scenariuszy przesyłania dzienników i metryk:

1) Utwórz aplikację główną usługi/Azure Active Directory, w tym tworzenie klucza tajnego dostępu klienta, i Przypisz jednostkę usługi do roli "Monitoruj metryki wydawcy" w subskrypcjach, w których znajdują się zasoby wystąpienia bazy danych.
2) Utwórz obszar roboczy usługi log Analytics i Pobierz klucze i Ustaw informacje w zmiennych środowiskowych.

Pierwszy element jest wymagany do przekazywania metryk, a drugi jest wymagany do przekazania dzienników.

Wykonaj następujące polecenia, aby utworzyć nazwę główną usługi przekazywania metryk i przypisać ją do ról "Monitoruj wydawcę metryk" i "Współautor", aby jednostka usługi mogła przekazywać metryki i wykonywać operacje tworzenia i przekazywania.

## <a name="create-service-principal-and-assign-roles"></a>Tworzenie nazwy głównej usługi i przypisywanie ról

Wykonaj następujące polecenia, aby utworzyć nazwę główną usługi przekazywania metryk i przypisać ją do roli "Wydawca metryk" monitorowania:

Aby utworzyć nazwę główną usługi, uruchom następujące polecenie:

> [!NOTE]
> Tworzenie jednostki usługi wymaga [pewnych uprawnień na platformie Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Przykładowe dane wyjściowe:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Zapisz wartości appId i dzierżawców w zmiennej środowiskowej, aby użyć jej później:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Uruchom to polecenie, aby przypisać jednostkę usługi do roli "monitorowanie" wydawcy metryk w subskrypcji, w której znajdują się zasoby wystąpienia bazy danych:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Przykładowe dane wyjściowe:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi log Analytics

Następnie wykonaj następujące polecenia, aby utworzyć obszar roboczy Log Analytics i ustawić informacje o dostępie do zmiennych środowiskowych.

> [!NOTE]
> Pomiń ten krok, jeśli masz już obszar roboczy.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Przykładowe dane wyjściowe:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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

Zapisz element customerId (identyfikator obszaru roboczego) jako zmienną środowiskową, która ma zostać użyta później:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

To polecenie spowoduje wydrukowanie kluczy dostępu wymaganych do nawiązania połączenia z obszarem roboczym usługi log Analytics:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Przykładowe dane wyjściowe:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Zapisz klucz podstawowy w zmiennej środowiskowej, która ma zostać użyta później:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Ustaw końcowe zmienne środowiskowe i Potwierdź

Ustaw adres URL urzędu nazw SPN w zmiennej środowiskowej:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Upewnij się, że wszystkie wymagane zmienne środowiskowe są ustawione, jeśli chcesz:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Przekaż metryki do Azure Monitor

Aby przekazać metryki dla wystąpień zarządzanych usługi Azure SQL i Azure Database for PostgreSQL grupy serwerów z funkcją skalowania w poziomie, wykonaj następujące polecenia CLI:

Spowoduje to wyeksportowanie wszystkich metryk do określonego pliku:

```console
azdata arc dc export -t metrics --path metrics.json
```

Spowoduje to przekazanie metryk do usługi Azure Monitor:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Wyświetlanie metryk w portalu

Po przekazaniu metryk powinno być możliwe ich wizualizowanie z poziomu witryny Azure Portal.

Aby wyświetlić metryki w portalu, użyj tego linku specjalnego, aby otworzyć Portal: <https://portal.azure.com> następnie wyszukaj wystąpienie bazy danych według nazwy na pasku wyszukiwania:

Użycie procesora CPU na stronie Przegląd lub aby uzyskać bardziej szczegółowe metryki, kliknij pozycję metryki w lewym panelu nawigacyjnym.

Wybierz serwer SQL jako przestrzeń nazw metryk:

Wybierz metrykę, którą chcesz wizualizować (można również wybrać wiele):

Zmień częstotliwość na ostatnie 30 minut:

> [!NOTE]
> Metryki można przekazać tylko w ciągu ostatnich 30 minut. Azure Monitor odrzuca metryki starsze niż 30 minut.

## <a name="upload-logs-to-azure-monitor"></a>Przekazywanie dzienników do usługi Azure Monitor

 Aby przekazać dzienniki dla wystąpień zarządzanych usługi Azure SQL i Azure Database for PostgreSQL grupy serwerów z możliwością skalowania, uruchom następujące polecenia interfejsu CLI:

Spowoduje to wyeksportowanie wszystkich dzienników do określonego pliku:

```console
azdata arc dc export -t logs --path logs.json
```

Spowoduje to przekazanie dzienników do obszaru roboczego usługi Azure Monitor Log Analytics:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Wyświetlanie dzienników w Azure Portal

Po przekazaniu dzienników powinno być możliwe wykonywanie do nich zapytań za pomocą eksploratora zapytań dziennika w następujący sposób:

1. Otwórz Azure Portal a następnie wyszukaj swój obszar roboczy według nazwy na pasku wyszukiwania u góry, a następnie wybierz go.
2. Kliknij pozycję Dzienniki w lewym panelu
3. Kliknij pozycję Rozpocznij (lub kliknij linki na stronie Wprowadzenie, aby dowiedzieć się więcej na temat Log Analytics, jeśli jesteś nowym).
4. Postępuj zgodnie z samouczkiem, aby dowiedzieć się więcej na temat Log Analytics, jeśli jest to Twoje pierwsze
5. Rozwiń pozycję Dzienniki niestandardowe u dołu listy tabel, aby zobaczyć tabelę o nazwie „sql_instance_logs_CL”.
6. Kliknij ikonę „oka” obok nazwy tabeli
7. Kliknij przycisk „Wyświetl w edytorze zapytań”
8. W edytorze zapytań będzie teraz dostępne zapytanie wyświetlające 10 ostatnich zdarzeń w dzienniku
9. W tym miejscu możesz poeksperymentować z wykonywaniem zapytań w dziennikach przy użyciu edytora zapytań, ustawianiem alertów itd.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatyzowanie metryk i dzienników przekazywania (opcjonalnie)

Jeśli chcesz stale przekazywać metryki i dzienniki, możesz utworzyć skrypt i uruchomić go w czasomierzu co kilka minut.  Poniżej znajduje się przykład automatyzacji przekazywania przy użyciu skryptu powłoki systemu Linux.

W ulubionym edytorze tekstu/kodu Dodaj następujące elementy do zawartości skryptu do pliku i Zapisz jako plik wykonywalny skryptu, taki jak. sh (Linux/Mac) lub. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Utwórz plik skryptu wykonywalnego

```console
chmod +x myuploadscript.sh
```

Uruchom skrypt co 2 minuty:

```console
watch -n 120 ./myuploadscript.sh
```

Można również użyć harmonogramu zadań, takiego jak CRONUS lub Windows Harmonogram zadań lub do programu Orchestrator, takiego jak rozwiązania ansible, Puppet lub Chef.
