---
title: Przekaż metryki do Azure Monitor
description: Przekaż metryki usług danych z włączonym funkcją Azure Arc do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: d7c611f1cdb5e3294e38f87c0534003813e50388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575682"
---
# <a name="upload-metrics-to-azure-monitor"></a>Przekaż metryki do Azure Monitor

Okresowo możesz wyeksportować metryki monitorowania, a następnie przekazać je do platformy Azure. Eksport i przekazywanie danych również tworzy i aktualizuje zasoby kontrolera danych, wystąpienia zarządzanego SQL i grupy serwerów PostgreSQL na platformie Azure.

> [!NOTE] 
> W okresie zapoznawczym nie ma kosztu korzystania z usług danych z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że została utworzona wymagana jednostka usługi i przypisano ją do odpowiedniej roli. Aby uzyskać szczegółowe informacje, zobacz:
* [Utwórz nazwę główną usługi](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Przypisywanie ról do jednostki usługi](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Przekazywanie metryk

Dzięki usługom danych usługi Azure Arc można opcjonalnie przekazać metryki do Azure Monitor, aby można było agregować i analizować metryki, zgłaszać alerty, wysyłać powiadomienia lub wyzwalać akcje automatyczne. 

Wysyłanie danych do Azure Monitor umożliwia również przechowywanie danych metryk poza lokacją i na dużą skalę, umożliwiając przechowywanie długoterminowe danych na potrzeby zaawansowanej analizy.

Jeśli masz wiele witryn z usługą Azure Arc Data Services, możesz użyć Azure Monitor jako centralnej lokalizacji, aby zebrać wszystkie dzienniki i metryki w witrynach.

## <a name="set-final-environment-variables-and-confirm"></a>Ustaw końcowe zmienne środowiskowe i Potwierdź

Ustaw adres URL urzędu nazw SPN w zmiennej środowiskowej:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Upewnij się, że wszystkie wymagane zmienne środowiskowe są ustawione, jeśli chcesz:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Przekaż metryki do Azure Monitor

Aby przekazać metryki dla wystąpień zarządzanych SQL z obsługą usługi Azure Arc i usługi Azure ARC z włączonymi PostgreSQL grupy serwerów, należy wykonać następujące polecenia interfejsu CLI:

1. Zaloguj się do kontrolera danych przy użyciu programu `azdata` .
 
1. Eksportuj wszystkie metryki do określonego pliku:

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Przekaż metryki do usługi Azure Monitor:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Zaczekaj co najmniej 30 minut po utworzeniu wystąpień danych z włączonym Łukem platformy Azure podczas pierwszego przekazywania.
   >
   >Upewnij się `upload` , że metryki od razu po `export` jako Azure monitor akceptują tylko metryki w ciągu ostatnich 30 minut. [Dowiedz się więcej](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting).


Jeśli widzisz błędy wskazujące "Niepowodzenie uzyskiwania informacji o metrykach" podczas eksportowania, sprawdź, czy zbieranie danych jest ustawione na `true` , uruchamiając następujące polecenie:

```console
azdata arc dc config show
```

Poszukaj w sekcji "zabezpieczenia".

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

W przypadku przekazywania metryk usługa Azure monitor akceptuje tylko ostatnie 30 minut danych ([Dowiedz się więcej](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Wskazówki dotyczące przekazywania metryk polegają na przekazaniu metryk bezpośrednio po utworzeniu pliku eksportu, aby można było wyświetlić cały zestaw danych w Azure Portal. Na przykład jeśli zostały wyeksportowane metryki o godzinie 2:00 PM i uruchomiono polecenie przekazywania o godzinie 2:50 PM. Ponieważ Azure Monitor akceptuje tylko dane z ostatnich 30 minut, w portalu mogą nie być widoczne żadne dane. 

## <a name="next-steps"></a>Następne kroki

[Przekaż dzienniki do Azure Monitor](upload-logs.md)

[Przekazywanie danych użycia, metryk i dzienników do Azure Monitor](upload-usage-data.md)

[Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal](view-billing-data-in-azure.md)

[Wyświetlanie zasobu usługi Azure Arc Data Controller w Azure Portal](view-data-controller-in-azure-portal.md)
