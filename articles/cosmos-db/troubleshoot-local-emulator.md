---
title: Rozwiązywanie problemów podczas korzystania z emulatora Azure Cosmos DB
description: Dowiedz się, jak w przypadku korzystania z emulatora Azure Cosmos DB usługa Troubleshot niedostępne, certyfikat, szyfrowanie i przechowywanie wersji.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb07adafbc68ff1e9b7d3ee49bb7631dc4395d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033463"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Rozwiązywanie problemów podczas korzystania z emulatora Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Porady w tym artykule ułatwiają rozwiązywanie problemów występujących podczas instalowania lub korzystania z emulatora Azure Cosmos DB. 

Jeśli po zainstalowaniu nowej wersji emulatora występują błędy, upewnij się, że dane zostały zresetowane. Możesz zresetować dane, klikając prawym przyciskiem myszy ikonę emulatora Azure Cosmos DB na pasku zadań, a następnie klikając polecenie Zresetuj dane.... Jeśli to nie rozwiąże błędów, można odinstalować emulator i wszystkie starsze wersje emulatora, jeśli zostały znalezione, Usuń *C:\Program files\Azure Cosmos DB Directory emulator* i ponownie zainstaluj emulator. Odpowiednie instrukcje znajdziesz w sekcji [Odinstalowywanie lokalnego emulatora](local-emulator.md#uninstall). Alternatywnie, jeśli Resetowanie danych nie działa, przejdź do `%LOCALAPPDATA%\CosmosDBEmulator` lokalizacji i Usuń folder.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Rozwiązywanie problemów z uszkodzonymi licznikami wydajności systemu Windows

* W przypadku awarii emulatora Azure Cosmos DB Zbierz pliki zrzutu z `%LOCALAPPDATA%\CrashDumps` folderu, skompresuj je i Otwórz bilet pomocy technicznej z [Azure Portal](https://portal.azure.com).

* Jeśli wystąpi awaria w programie `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` , może to być objawem, w którym liczniki wydajności są w stanie uszkodzenia. Zwykle uruchomienie następującego polecenia z poziomu wiersza polecenia administratora rozwiązuje problem:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

* W przypadku wystąpienia problemu z łącznością należy [zebrać pliki śledzenia](#trace-files), skompresować je i otworzyć bilet pomocy technicznej w [Azure Portal](https://portal.azure.com).

* Jeśli zostanie wyświetlony komunikat **Service Unavailable** (Usługa niedostępna), może to wskazywać na błąd inicjacji stosu sieciowego. Sprawdź, czy masz zainstalowanego klienta Pulse Secure lub Juniper Networks — ich sterowniki filtrów sieci mogą powodować problemy. Odinstalowanie sterowników filtrów sieci innych firm zwykle pozwala rozwiązać problem. Alternatywnie można uruchomić emulator przy użyciu/DisableRIO, co spowoduje przełączenie komunikacji między sieciami emulatora do zwykłego interfejsu Winsock. 

* Jeśli wystąpi **"zabronione", "komunikat": "żądanie jest wykonywane z niedozwolonym szyfrowaniem w protokole tranzytowym lub szyfrie. Sprawdź ustawienia Minimalny dozwolony protokół SSL/TLS konta... "** problemy z łącznością mogą być spowodowane przez globalne zmiany w systemie operacyjnym (na przykład kompilacja Insider Preview 20170) lub ustawienia przeglądarki, które domyślnie włączają protokół TLS 1,3. Podobny błąd może wystąpić podczas używania zestawu SDK do wykonywania żądania względem emulatora Cosmos, takiego jak **Microsoft.Azure.Documents.DocumentClientException: żądanie jest wykonywane z niedozwolonym szyfrowaniem w protokole tranzytowym lub szyfrem. Sprawdź ustawienie Minimalny dozwolony protokół SSL/TLS konta**. Obecnie jest to oczekiwane zachowanie, ponieważ emulator usługi Cosmos akceptuje tylko protokół TLS 1.2 i z nim współpracuje. Zalecane obejście to zmiana ustawień i domyślnego protokołu TLS 1,2; na przykład w Menedżerze usług IIS przejdź do lokalizacji "Lokacje" — > "domyślne witryny sieci Web" i Znajdź "powiązania witryny" dla portu 8081 i edytuj je, aby wyłączyć protokół TLS 1,3. Podobną operację można wykonać dla przeglądarki internetowej za pomocą opcji „Ustawienia”.

* Kiedy emulator jest uruchomiony, jeśli komputer przejdzie w tryb uśpienia lub uruchomi dowolną aktualizację systemu operacyjnego, możesz zobaczyć komunikat **Service is currently unavailable** (Usługa jest obecnie niedostępna). Zresetuj dane emulatora, klikając prawym przyciskiem myszy ikonę widoczną na pasku powiadomień systemu Windows i wybierając pozycję **Zresetuj dane**.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Zbieranie plików śledzenia

Aby zebrać dane śledzenia debugowania, uruchom następujące polecenia w wierszu polecenia z uprawnieniami administratora:

1. Przejdź do ścieżki, w której zainstalowano emulator:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Zamknij emulator i obejrzyj zasobnik systemowy, aby upewnić się, że program został zamknięty. Ukończenie tego procesu może potrwać minutę. Możesz również wybrać pozycję **Exit** w interfejsie użytkownika emulatora Azure Cosmos DB.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Rozpocznij rejestrowanie przy użyciu następującego polecenia:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Uruchamianie emulatora

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Odtwórz problem. Jeśli Eksplorator danych nie działa, musisz poczekać, aż przeglądarka zostanie otwarta przez kilka sekund, aby przechwycić błąd.

1. Zatrzymaj rejestrowanie przy użyciu następującego polecenia:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Przejdź do `%ProgramFiles%\Azure Cosmos DB Emulator` ścieżki i Znajdź plik *docdbemulator_000001. etl* .

1. Otwórz bilet pomocy technicznej w [Azure Portal](https://portal.azure.com) i Uwzględnij plik ETL wraz z krokami Odtwórz.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób debugowania problemów z emulatorem lokalnym. Teraz możesz przechodzić do następnych artykułów:

* [Eksportowanie Azure Cosmos DB certyfikatów emulatora do użycia z aplikacjami Java, Python i Node.js](local-emulator-export-ssl-certificates.md)
* [Używanie parametrów wiersza polecenia i poleceń programu PowerShell do sterowania emulatorem](emulator-command-line-parameters.md)
