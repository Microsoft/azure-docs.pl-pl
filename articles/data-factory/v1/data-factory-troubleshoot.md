---
title: Rozwiązywanie problemów dotyczących Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: 7afc16beaacee5b75d57c4e4216a105734d20a09
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637075"
---
# <a name="troubleshoot-data-factory-issues"></a>Rozwiązywanie problemów z usługą Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. 

W tym artykule przedstawiono wskazówki dotyczące rozwiązywania problemów występujących podczas korzystania z Azure Data Factory. W tym artykule nie wymieniono wszystkich możliwych problemów występujących podczas korzystania z usługi, ale omówiono niektóre problemy i ogólne porady dotyczące rozwiązywania problemów.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Błąd: subskrypcja nie jest zarejestrowana do korzystania z przestrzeni nazw „Microsoft.DataFactory”
Wyświetlenie tego błędu oznacza, że dostawca zasobów usługi Azure Data Factory nie został zarejestrowany na maszynie. Wykonaj następujące czynności:

1. Uruchom program Azure PowerShell.
2. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia.

    ```powershell
    Connect-AzAccount
    ```
3. Uruchom następujące polecenie, aby zarejestrować dostawcę Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: nieautoryzowany błąd podczas uruchamiania polecenia cmdlet Data Factory
Przypuszczalnie nie używasz prawidłowego konta lub subskrypcji platformy Azure w programie Azure PowerShell. Użyj następujących poleceń cmdlet, aby wybrać odpowiednie konto i subskrypcję platformy Azure do stosowania w programie Azure PowerShell.

1. Connect-AzAccount — Użyj odpowiedniego identyfikatora użytkownika i hasła
2. Get-AzSubscription — Wyświetl wszystkie subskrypcje dla konta.
3. Select-AzSubscription &lt; nazwę subskrypcji &gt; — Wybierz odpowiednią subskrypcję. Użyj tego samego, którego używasz, aby utworzyć fabrykę danych na Azure Portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: nie można uruchomić Instalatora Express Zarządzanie danymi bramy z Azure Portal
Instalacja ekspresowa bramy zarządzania danymi wymaga przeglądarki Internet Explorer lub przeglądarki zgodnej z technologią Microsoft ClickOnce. Jeśli uruchomienie instalacji ekspresowej nie powiedzie się, wykonaj jedną z poniższych czynności:

* Użyj programu Internet Explorer lub przeglądarki internetowej zgodnej z programem Microsoft ClickOnce.

    Jeśli używasz programu Chrome, przejdź do [sklepu sieci Web programu Chrome](https://chrome.google.com/webstore/), wyszukaj słowo kluczowe "ClickOnce", wybierz jedno z rozszerzeń ClickOnce i zainstaluj je.

    Wykonaj te same czynności dla przeglądarki Firefox (Zainstaluj dodatek). Kliknij przycisk Otwórz menu na pasku narzędzi (trzy poziome linie w prawym górnym rogu), kliknij pozycję Dodatki, wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.
* Użyj linku **instalacji ręcznej** widocznego w tym samym bloku w portalu. Ta metoda służy do pobierania pliku instalacyjnego i uruchamiania go ręcznie. Po pomyślnym zakończeniu instalacji zostanie wyświetlone okno dialogowe Konfiguracja bramy Zarządzanie danymi. Skopiuj **klucz** z ekranu portalu i użyj go w menedżerze konfiguracji, aby ręcznie zarejestrować bramę w usłudze.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Problem: nie można nawiązać połączenia z SQL Server
Uruchom **Zarządzanie danymi bramę Configuration Manager** na maszynie bramy i Użyj karty **Rozwiązywanie problemów** , aby przetestować połączenie z SQL Server z maszyny bramy. Zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) , aby uzyskać porady dotyczące rozwiązywania problemów związanych z połączeniem/bramą.   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Problem: wycinki wejściowe są w stanie oczekiwania w nieskończoność
Wycinki mogą znajdować się w stanie **oczekiwania** z różnych powodów. Jednym z typowych przyczyn jest to, że właściwość **zewnętrzna** nie jest ustawiona na **wartość true** . Każdy zestaw danych, który jest tworzony poza zakresem Azure Data Factory powinien być oznaczony przy użyciu właściwości **zewnętrznej** . Ta właściwość wskazuje, że dane są zewnętrzne i nie są obsługiwane przez żadne potoki w fabryce danych. Wycinki danych są oznaczane jako **Gotowy** , gdy dane staną się dostępne w odpowiednim magazynie.

Zobacz poniższy przykład wykorzystania właściwości **external** . Opcjonalnie można określić **externalData** _, gdy ustawisz zewnętrzny na true.

Dodatkowe informacje na temat tej właściwości można znaleźć w artykule [Zestawy danych](data-factory-create-datasets.md).

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Aby rozwiązać ten problem, należy dodać właściwość _ *External* * i opcjonalną sekcję **EXTERNALDATA** do definicji JSON tabeli wejściowej i ponownie utworzyć tabelę.

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: operacja kopiowania hybrydowego kończy się niepowodzeniem
Zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) , aby uzyskać instrukcje dotyczące rozwiązywania problemów z kopiowaniem do/z lokalnego magazynu danych przy użyciu bramy zarządzanie danymi.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: Inicjowanie obsługi usługi HDInsight na żądanie nie powiodło się
Podczas korzystania z połączonej usługi typu HDInsightOnDemand należy określić nazwę linkedServiceName, która wskazuje usługę Azure Blob Storage. Usługa Data Factory korzysta z tego magazynu do przechowywania dzienników i plików pomocniczych dla klastra HDInsight na żądanie.  Czasami inicjowanie obsługi klastra HDInsight na żądanie kończy się niepowodzeniem z następującym błędem:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Błąd ten wskazuje zazwyczaj, że lokalizacja konta magazynu określona w parametrze linkedServiceName znajduje się w innej lokalizacji centrum danych niż lokalizacja, w której następuje inicjowanie obsługi HDInsight. Przykład: Jeśli Fabryka danych znajduje się w regionie zachodnie stany USA, a usługa Azure Storage jest w Wschodnie stany USA, Inicjowanie obsługi na żądanie kończy się niepowodzeniem w zachodnich stanach USA.

Ponadto występuje druga właściwość JSON o nazwie additionalLinkedServiceNames, w której można określić dodatkowe konta magazynu dla usługi HDInsight na żądanie. Te dodatkowe połączone konta magazynu powinny znajdować się w tej samej lokalizacji co klaster usługi HDInsight lub niepowodzeniem z powodu tego samego błędu.

### <a name="problem-custom-net-activity-fails"></a>Problem: niepowodzenie niestandardowego działania platformy .NET
Aby uzyskać szczegółowe instrukcje, zobacz [debugowanie potoku za pomocą działania niestandardowego](data-factory-use-custom-activities.md#troubleshoot-failures) .

## <a name="use-azure-portal-to-troubleshoot"></a>Używanie Azure Portal do rozwiązywania problemów
### <a name="using-portal-blades"></a>Korzystanie z bloków portalu
Aby uzyskać instrukcje, zobacz [monitorowanie potoków](data-factory-monitor-manage-pipelines.md) .

### <a name="using-monitor-and-manage-app"></a>Korzystanie z aplikacji Monitorowanie i zarządzanie
Aby uzyskać szczegółowe informacje [, zobacz Monitorowanie potoków usługi Data Factory i zarządzanie nimi za pomocą aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) .

## <a name="use-azure-powershell-to-troubleshoot"></a>Używanie Azure PowerShell do rozwiązywania problemów
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Użyj Azure PowerShell, aby rozwiązać problem
Aby uzyskać szczegółowe informacje, zobacz [monitorowanie potoków Data Factory przy użyciu Azure PowerShell](data-factory-monitor-manage-pipelines.md) .

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png