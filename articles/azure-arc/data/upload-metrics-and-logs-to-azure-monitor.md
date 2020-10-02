---
title: Przekazywanie danych użycia, metryk i dzienników do Azure Monitor
description: Przekazywanie spisu zasobów, danych użycia, metryk i dzienników do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3e3b804e2a3c43eb9579d1c6a1195511df528de2
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630003"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Przekazywanie danych użycia, metryk i dzienników do Azure Monitor

Okresowo można wyeksportować informacje o użyciu dotyczące rozliczeń, metryk monitorowania i dzienników, a następnie przekazać je na platformę Azure.  Eksportowanie i przekazywanie dowolnych z tych trzech typów danych spowoduje również utworzenie i zaktualizowanie zasobów grupy serwerów, wystąpienia zarządzanego SQL i PostgreSQL na platformie Azure.

> [!NOTE] 
W okresie zapoznawczym nie ma kosztu korzystania z usług danych z obsługą usługi Azure Arc.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagany jest interfejs wiersza polecenia platformy Azure (az) i zainstalowano interfejs wiersza polecenia platformy Azure (azdata).  [Zainstaluj narzędzia](./install-client-tools.md).

Przed przekazaniem danych na platformę Azure musisz upewnić się, że Twoja subskrypcja platformy Azure ma zarejestrowany dostawca zasobów Microsoft. AzureData.

Można to sprawdzić, uruchamiając następujące polecenie:

```console
az provider show -n Microsoft.AzureData -o table
```

Jeśli dostawca zasobów nie jest obecnie zarejestrowany w ramach subskrypcji, możesz go zarejestrować, uruchamiając następujące polecenie.  Wykonanie tego polecenia może potrwać minutę lub dwa.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Przekazywanie danych użycia

Informacje dotyczące użycia, takie jak spis i użycie zasobów, można przekazać do platformy Azure w następujący dwuetapowy sposób:

1. Wyeksportuj dane użycia za pomocą ```azdata export``` polecenia w następujący sposób:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   To polecenie tworzy `usage.json` plik ze wszystkimi zasobami danych z obsługą usługi Azure ARC, takimi jak wystąpienia zarządzane SQL i wystąpieniami PostgreSQL, które są tworzone na kontrolerze danych.

2. Przekazywanie danych użycia za pomocą ```azdata upload``` polecenia

   > [!NOTE]
   > Przed uruchomieniem przekazywania zaczekaj co najmniej 24 godziny od momentu utworzenia kontrolera danych usługi Azure Arc

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Przekazywanie metryk i dzienników

Dzięki usługom danych usługi Azure Arc można opcjonalnie przekazać metryki i dzienniki do Azure Monitor, aby można było agregować i analizować metryki, dzienniki, zgłaszać alerty, wysyłać powiadomienia lub wyzwalać akcje automatyczne. 

Wysyłanie danych do Azure Monitor umożliwia także przechowywanie danych monitorowania i dzienników poza lokacją oraz na dużą skalę w celu umożliwienia długoterminowego przechowywania danych na potrzeby zaawansowanej analizy.

Jeśli masz wiele witryn z usługą Azure Arc Data Services, możesz użyć Azure Monitor jako centralnej lokalizacji, aby zebrać wszystkie dzienniki i metryki w witrynach.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Istnieje kilka jednorazowych kroków konfiguracji wymaganych do włączenia scenariuszy przesyłania dzienników i metryk:

1. Utwórz aplikację główną usługi/Azure Active Directory, w tym tworzenie klucza tajnego dostępu klienta, i Przypisz jednostkę usługi do roli "Monitoruj metryki wydawcy" w subskrypcjach, w których znajdują się zasoby wystąpienia bazy danych.
2. Utwórz obszar roboczy usługi log Analytics i Pobierz klucze i Ustaw informacje w zmiennych środowiskowych.

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

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Zapisz wartości appId i dzierżawców w zmiennej środowiskowej, aby użyć jej później. 

Aby zapisać wartości appId i dzierżawy za pomocą programu PowerShell, wykonaj następujące czynności:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

Alternatywnie w systemie Linux lub macOS można zapisać wartości appId i dzierżawy z poniższym przykładem:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Uruchom to polecenie, aby przypisać jednostkę usługi do roli "monitorowanie" wydawcy metryk w subskrypcji, w której znajdują się zasoby wystąpienia bazy danych:


> [!NOTE]
> Podczas uruchamiania ze środowiska systemu Windows należy użyć podwójnych cudzysłowów dla nazw ról.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Przykładowe dane wyjściowe:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi log Analytics

Następnie wykonaj następujące polecenia, aby utworzyć obszar roboczy Log Analytics i ustawić informacje o dostępie do zmiennych środowiskowych.

> [!NOTE]
> Pomiń ten krok, jeśli masz już obszar roboczy.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Aby przekazać metryki dla wystąpień zarządzanych SQL z obsługą usługi Azure Arc i usługi Azure ARC z włączonymi PostgreSQL grupy serwerów, należy wykonać następujące polecenia interfejsu CLI:

1. Eksportuj wszystkie metryki do określonego pliku:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Przekaż metryki do usługi Azure Monitor:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Poczekaj co najmniej 30 minut po utworzeniu wystąpień danych z włączonym Łukem platformy Azure podczas pierwszego przekazywania
   >
   >Upewnij się `upload` , że metryki od razu po `export` jako Azure monitor akceptują tylko metryki w ciągu ostatnich 30 minut. [Dowiedz się więcej](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Jeśli widzisz błędy wskazujące "Niepowodzenie uzyskiwania informacji o metrykach" podczas eksportowania, sprawdź, czy zbieranie danych jest ustawione na ```true``` , uruchamiając następujące polecenie:

```console
azdata arc dc config show
```

i poszukaj w sekcji "zabezpieczenia".

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Sprawdź, czy `allowNodeMetricsCollection` `allowPodMetricsCollection` właściwości i są ustawione na `true` .

## <a name="view-the-metrics-in-the-portal"></a>Wyświetlanie metryk w portalu

Po przekazaniu metryk można je wyświetlić z poziomu Azure Portal.
> [!NOTE]
> Należy pamiętać, że przetworzenie przekazanych danych może potrwać kilka minut, zanim będzie można wyświetlić metryki w portalu.


Aby wyświetlić metryki w portalu, użyj tego linku, aby otworzyć Portal: <https://portal.azure.com> następnie wyszukaj wystąpienie bazy danych według nazwy na pasku wyszukiwania:

Użycie procesora CPU na stronie Przegląd lub aby uzyskać bardziej szczegółowe metryki, kliknij pozycję metryki w lewym panelu nawigacyjnym.

Wybierz serwer SQL jako przestrzeń nazw metryk:

Wybierz metrykę, którą chcesz wizualizować (można również wybrać wiele):

Zmień częstotliwość na ostatnie 30 minut:

> [!NOTE]
> Metryki można przekazać tylko w ciągu ostatnich 30 minut. Azure Monitor odrzuca metryki starsze niż 30 minut.

## <a name="upload-logs-to-azure-monitor"></a>Przekazywanie dzienników do usługi Azure Monitor

 Aby przekazać dzienniki dla wystąpień zarządzanych SQL z włączonym funkcją Azure Arc i AzureArc z włączonymi grupami serwerów PostgreSQL w celu uruchomienia następujących poleceń interfejsu wiersza polecenia —

1. Eksportuj wszystkie dzienniki do określonego pliku:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Przekaż dzienniki do obszaru roboczego usługi log Analytics w usłudze Azure Monitor:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Wyświetlanie dzienników w Azure Portal

Po przekazaniu dzienników powinno być możliwe wykonywanie do nich zapytań za pomocą eksploratora zapytań dziennika w następujący sposób:

1. Otwórz Azure Portal a następnie wyszukaj swój obszar roboczy według nazwy na pasku wyszukiwania u góry, a następnie wybierz go.
2. Kliknij pozycję Dzienniki w lewym panelu
3. Kliknij pozycję Rozpocznij (lub kliknij linki na stronie Wprowadzenie, aby dowiedzieć się więcej na temat Log Analytics, jeśli jesteś nowym).
4. Postępuj zgodnie z samouczkiem, aby dowiedzieć się więcej na temat Log Analytics, jeśli używasz Log Analytics
5. Rozwiń pozycję Dzienniki niestandardowe u dołu listy tabel, aby zobaczyć tabelę o nazwie „sql_instance_logs_CL”.
6. Kliknij ikonę „oka” obok nazwy tabeli
7. Kliknij przycisk „Wyświetl w edytorze zapytań”
8. W edytorze zapytań znajdziesz teraz zapytanie, które będzie zawierać najnowsze 10 zdarzeń w dzienniku
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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Ogólne wskazówki dotyczące eksportowania i przekazywania użycia, metryk

Operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w usługach danych z obsługą usługi Azure Arc są rejestrowane na potrzeby rozliczeń i monitorowania. Istnieją usługi w tle, które monitorują te operacje CRUD i odpowiednio obliczają zużycie. Rzeczywiste obliczenie użycia lub zużycia odbywa się zgodnie z harmonogramem i jest wykonywane w tle. 

W trakcie okresu zapoznawczego ten proces odbywa się nocnie. Ogólne wskazówki to przekazywanie użycia tylko raz dziennie. Gdy informacje o użyciu zostaną wyeksportowane i przekazane wiele razy w tym samym okresie 24 godzin, tylko spis zasobów zostanie zaktualizowany w Azure Portal ale nie w przypadku użycia zasobów.

W przypadku przekazywania metryk usługa Azure monitor akceptuje tylko ostatnie 30 minut danych ([Dowiedz się więcej](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Wskazówki dotyczące przekazywania metryk polegają na przekazaniu metryk bezpośrednio po utworzeniu pliku eksportu, aby można było wyświetlić cały zestaw danych w Azure Portal. Na przykład jeśli zostały wyeksportowane metryki o godzinie 2:00 PM i uruchomiono polecenie przekazywania o godzinie 2:50 PM. Ponieważ Azure Monitor akceptuje tylko dane z ostatnich 30 minut, w portalu mogą nie być widoczne żadne dane. 

## <a name="next-steps"></a>Następne kroki

[Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal](view-billing-data-in-azure.md)

[Wyświetlanie zasobu usługi Azure Arc Data Controller w Azure Portal](view-data-controller-in-azure-portal.md)
