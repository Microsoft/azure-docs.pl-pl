---
title: Rozwiązywanie problemów z nieautoryzowanymi wyjątkami Azure Cosmos DB
description: Dowiedz się, jak diagnozować i naprawiać nieautoryzowane wyjątki.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dce956b932274a44e98a28b4c6567a5e98771613
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340009"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB nieautoryzowanych wyjątków
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: podpis MAC znaleziony w żądaniu HTTP nie jest taki sam jak obliczony podpis.
Jeśli otrzymasz komunikat o błędzie 401 "podpis MAC znaleziony w żądaniu HTTP nie jest taki sam jak obliczony podpis", może to być spowodowane przez następujące scenariusze.

W przypadku starszych zestawów SDK wyjątek może pojawić się jako nieprawidłowy wyjątek JSON zamiast poprawnego nieautoryzowanego wyjątku 401. Nowsze zestawy SDK prawidłowo obsługują ten scenariusz i dają prawidłowy komunikat o błędzie.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania nieautoryzowanych wyjątków.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>Klucz nie został poprawnie obrócony jest najbardziej typowym scenariuszem
Podpis MAC 401 jest widoczny wkrótce po rotacji kluczy i ostatecznie zatrzyma bez wprowadzania jakichkolwiek zmian. 

#### <a name="solution"></a>Rozwiązanie:
Klucz został obrócony i nie był zgodny z [najlepszymi rozwiązaniami](secure-access-to-data.md#key-rotation). Rotacja klucza konta Azure Cosmos DB może potrwać od kilku sekund do prawdopodobnie dni, w zależności od rozmiaru konta Azure Cosmos DB.

### <a name="the-key-is-misconfigured"></a>Klucz jest nieprawidłowo skonfigurowany 
Problem z sygnaturą 401 dla komputerów MAC będzie spójny i ma miejsce w przypadku wszystkich wywołań przy użyciu tego klucza.

#### <a name="solution"></a>Rozwiązanie:
Klucz jest nieprawidłowo skonfigurowany w aplikacji i używa niewłaściwego klucza dla konta lub cały klucz nie został skopiowany.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>Aplikacja używa kluczy tylko do odczytu dla operacji zapisu
Problem z sygnaturą 401 dla komputerów MAC występuje tylko w przypadku operacji zapisu, takich jak tworzenie lub zastępowanie, ale żądania odczytu powiodły się.

#### <a name="solution"></a>Rozwiązanie:
Przełącz aplikację w taki sposób, aby korzystała z klucza do odczytu/zapisu, aby umożliwić pomyślne zakończenie operacji.

### <a name="race-condition-with-create-container"></a>Sytuacja wyścigu z użyciem kontenera tworzenia
Problem z podpisem komputera MAC 401 jest widoczny wkrótce po utworzeniu kontenera. Ten problem występuje tylko do momentu zakończenia tworzenia kontenera.

#### <a name="solution"></a>Rozwiązanie:
Istnieje sytuacja wyścigu z tworzeniem kontenera. Wystąpienie aplikacji próbuje uzyskać dostęp do kontenera przed ukończeniem tworzenia kontenera. Najbardziej typowym scenariuszem dla tego warunku wyścigu jest, jeśli aplikacja jest uruchomiona, a kontener zostanie usunięty i utworzony przy użyciu tej samej nazwy. Zestaw SDK próbuje użyć nowego kontenera, ale Tworzenie kontenera nadal trwa, dlatego nie ma kluczy.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).