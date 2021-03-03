---
title: Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal
description: Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 16546432c8c0a23d5c9dc471fe8c62ced5eca993
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687536"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal

> [!IMPORTANT] 
>  W okresie zapoznawczym nie ma kosztu korzystania z usług danych z obsługą usługi Azure Arc. Mimo że system rozliczeń działa jak najlepiej zakończyć licznik rozliczeń jest ustawiony na $0.  Jeśli wykonasz ten scenariusz, zobaczysz wpisy w rozliczeniach dla usługi o nazwie **hybrydowe usługi danych** i dla zasobów typu o nazwie **Microsoft. AzureData/ `<resource type>`**. Będziesz mieć możliwość wyświetlenia rekordu dla każdej usługi danych — utworzonego przez Ciebie łuku platformy Azure, ale każdy rekord zostanie rozliczony dla $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Tryby łączności — implikacje dla danych rozliczeń

W przyszłości będą dostępne dwa tryby, w których można uruchamiać usługi danych z włączoną funkcją Azure ARC:

- **Połączono** bezpośrednio — nie ma bezpośredniego połączenia z platformą Azure. Dane są wysyłane do platformy Azure tylko przez proces eksportowania/przekazywania. Wszystkie wdrożenia usługi Azure Arc Data Services działają w tym trybie dzisiaj w wersji zapoznawczej.
- **Połączone bezpośrednio** — w tym trybie jest zależna od usługi Kubernetes z włączonym Azure ARC, która zapewnia bezpośrednie połączenie między platformą Azure i klastrem Kubernetes, na którym działają usługi danych z funkcją Azure Arc. Spowoduje to włączenie większej liczby możliwości i umożliwi użycie Azure Portal i interfejsu wiersza polecenia platformy Azure do zarządzania usługami danych z obsługą usługi Azure ARC, podobnie jak w przypadku zarządzania usługami danych w usłudze Azure PaaS.  Ten tryb łączności nie jest jeszcze dostępny w wersji zapoznawczej, ale zostanie wkrótce uwzględniony.

Więcej informacji o różnicach między [trybami łączności](./connectivity.md).

W trybie połączone bezpośrednio dane dotyczące rozliczeń są okresowo eksportowane z kontrolera danych usługi Azure Arc do zabezpieczonego pliku, a następnie przekazywane do platformy Azure i przetwarzane.  W nadchodzącym bezpośrednio trybie połączonym dane dotyczące rozliczeń zostaną automatycznie wysłane do platformy Azure około 1/godzinę, aby zapewnić niemal rzeczywisty wgląd w koszty usług. Proces eksportowania i przekazywania danych w trybie bezpośrednio połączonym może być również zautomatyzowany przy użyciu skryptów lub możemy utworzyć usługę, która ją wykona.

## <a name="upload-billing-data-to-azure"></a>Przekazywanie danych dotyczących rozliczeń na platformę Azure

Aby przekazać dane dotyczące rozliczeń na platformę Azure, należy najpierw wykonać następujące czynności:

1. Utwórz usługę danych z włączonym Łukem Azure, jeśli jeszcze jej nie masz. Na przykład Utwórz jedną z następujących czynności:
   - [Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md)
   - [Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc](create-postgresql-hyperscale-server-group.md)
1. [Przekazanie spisu zasobów, danych użycia, metryk i dzienników do Azure monitor](upload-metrics-and-logs-to-azure-monitor.md) , jeśli nie zostało to jeszcze zrobione.
1. Zaczekaj co najmniej 2 godziny od momentu utworzenia usługi danych, aby proces gromadzenia danych telemetrii rozliczeń mógł zbierać dane dotyczące rozliczeń.

Uruchom następujące polecenie, aby wyeksportować dane dotyczące rozliczeń:

```console
azdata arc dc export -t usage -p usage.json
```

Na razie plik nie jest szyfrowany, aby można było zobaczyć jego zawartość. Możesz otworzyć Edytor tekstu i zobaczyć, jak wygląda zawartość.

Zobaczysz, że istnieją dwa zestawy danych: `resources` i `data` . `resources`Są to kontrolery danych, grupy serwerów PostgreSQL w skali i wystąpienia zarządzane SQL. `resources`Rekordy w danych przechwytują odpowiednie zdarzenia w historii zasobu — gdy został on utworzony, kiedy został on zaktualizowany i kiedy został usunięty. `data`Rekordy przechwytuje liczbę rdzeni dostępnych do użycia przez dane wystąpienie przez każdą godzinę.

Przykład `resource` wpisu:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Przykład `data` wpisu:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Uruchom następujące polecenie, aby przekazać usage.jsdo pliku na platformę Azure:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Wyświetl dane rozliczeń w Azure Portal

Wykonaj następujące kroki, aby wyświetlić dane dotyczące rozliczeń w Azure Portal:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
1. W polu wyszukiwania w górnej części ekranu wpisz **Cost Management** i kliknij usługę Cost Management.
1. W obszarze **Cost Management przegląd** kliknij kartę **Cost Management** .
1. Kliknij kartę **Analiza kosztów** po lewej stronie.
1. Kliknij przycisk **Koszt według zasobów** w górnej części widoku.
1. Upewnij się, że Twój zakres jest ustawiony na subskrypcję, w której zostały utworzone zasoby usługi danych.
1. Wybierz pozycję **Koszt według zasobów** na liście rozwijanej obok selektora zakresu w górnej części widoku.
1. Upewnij się, że filtr daty jest ustawiony na **ten miesiąc** , lub inny zakres czasu, który ma sens w przypadku tworzenia zasobów usługi danych.
1. Kliknij przycisk **Dodaj filtr** , aby dodać filtr według **typu zasobu** ,  =  `microsoft.azuredata/<data service type>` Jeśli chcesz odfiltrować tylko jeden typ usługi danych z włączonym łukem Azure.
1. Zostanie wyświetlona lista wszystkich zasobów, które zostały utworzone i przekazane do platformy Azure. Ponieważ licznik rozliczeń to $0, zobaczysz, że koszt jest zawsze $0.

## <a name="download-billing-data"></a>Pobierz dane rozliczeń

Możesz pobrać dane podsumowania rozliczeń bezpośrednio z Azure Portal.

1. W tej samej **analizie kosztów — widok > według typu zasobu** , który został osiągnięty zgodnie z powyższymi instrukcjami, kliknij przycisk Pobierz w górnej części strony.
1. Wybierz typ pliku do pobrania — Excel lub CSV — i kliknij przycisk **Pobierz dane** .
1. Otwórz plik w odpowiednim edytorze, używając wybranego typu pliku.

## <a name="export-billing-data"></a>Eksportuj dane rozliczeń

Możesz również okresowo eksportować **szczegółowe** dane dotyczące użycia i rozliczeń do kontenera usługi Azure Storage, tworząc zadanie eksportu rozliczeń. Jest to przydatne, jeśli chcesz zobaczyć szczegóły dotyczące rozliczeń, na przykład liczbę godzin naliczania danego wystąpienia w okresie rozliczeniowym.

Wykonaj następujące kroki, aby skonfigurować zadanie eksportu rozliczeń:

1. Kliknij pozycję **eksporty** po lewej stronie.
1. Kliknij pozycję **Dodaj**.
1. Wprowadź nazwę i częstotliwość eksportowania, a następnie kliknij przycisk Dalej.
1. Wybierz opcję utworzenia nowego konta magazynu lub Użyj istniejącego i wypełnij formularz, aby określić konto magazynu, kontener i ścieżkę katalogu do eksportowania plików danych dotyczących rozliczeń, a następnie kliknij przycisk Dalej.
1. Kliknij pozycję **Utwórz**.

Pliki eksportu danych dotyczących rozliczeń będą dostępne przez około 4 godziny i zostaną wyeksportowane zgodnie z harmonogramem określonym podczas tworzenia zadania eksportu rozliczenia.

Wykonaj następujące kroki, aby wyświetlić wyeksportowane pliki danych dotyczących rozliczeń:

Można sprawdzić poprawność plików danych rozliczeń w Azure Portal. 

> [!IMPORTANT]
> Po utworzeniu zadania eksportu rozliczenia odczekaj 4 godziny przed wykonaniem poniższych kroków.

1. W polu wyszukiwania w górnej części portalu wpisz pozycję **konta magazynu** i kliknij pozycję **konta magazynu**.
3. Kliknij konto magazynu, które zostało określone podczas tworzenia zadania eksportu rozliczenia powyżej.
4. Kliknij kontenery po lewej stronie.
5. Kliknij kontener, który został określony podczas tworzenia zadania eksportu rozliczenia powyżej.
6. Kliknij folder określony podczas tworzenia zadania eksportu rozliczenia powyżej.
7. Przejdź do szczegółów wygenerowanych folderów i plików, a następnie kliknij jeden z wygenerowanych plików CSV.
8. Kliknij przycisk **Pobierz** , który zapisze plik w lokalnym folderze pobierania.
9. Otwórz plik, używając przeglądarki plików CSV, takiej jak program Excel.
10. Przefiltruj wyniki, aby wyświetlić tylko wiersze z **typem zasobu**  =  `Microsoft.AzureData/<data service resource type` .
11. Zostanie wyświetlona liczba godzin użycia wystąpienia w bieżącym 24-godzinnym okresie w kolumnie UsageQuantity.
