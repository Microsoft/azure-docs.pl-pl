---
title: Rozwiązywanie problemów z nieautoryzowanym wyjątkiem Azure Cosmos DB
description: Jak zdiagnozować i naprawić nieautoryzowany wyjątek
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294262"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB nieautoryzowany wyjątek

HTTP 401: podpis MAC znaleziony w żądaniu HTTP nie jest taki sam jak obliczony podpis.
Jeśli został wyświetlony następujący komunikat o błędzie 401: „Podpis MAC znaleziony w żądaniu HTTP nie jest taki sam, jak podpis obliczony”, może to być spowodowane następującymi scenariuszami.

Starsze zestawy SDK wyjątek może pojawić się jako nieprawidłowy wyjątek JSON zamiast poprawnego nieautoryzowanego wyjątku 401. Nowsze zestawy SDK prawidłowo obsługują ten scenariusz i dają prawidłowy komunikat o błędzie.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania nieautoryzowanego wyjątku.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. klucz nie został poprawnie obrócony, jest najbardziej typowym scenariuszem.
Komunikat o błędzie 401 dotyczącym podpisu MAC jest wyświetlany krótko po wymianie kluczy i przestaje być wyświetlany bez wprowadzania jakichkolwiek zmian. 

#### <a name="solution"></a>Rozwiązanie:
Klucz został wymieniony i nie zastosowano [najlepszych rozwiązań](secure-access-to-data.md#key-rotation). Rotacja klucza konta Cosmos DB może potrwać od kilku sekund do prawdopodobnie dni, w zależności od rozmiaru konta Cosmos DB.

### <a name="2-the-key-is-misconfigured"></a>2. klucz jest nieprawidłowo skonfigurowany 
problem z sygnaturą 401 dla komputerów MAC będzie spójny i występuje w przypadku wszystkich wywołań przy użyciu tego klucza

#### <a name="solution"></a>Rozwiązanie:
Klucz jest nieprawidłowo skonfigurowany w aplikacji i używa niewłaściwego klucza dla konta lub nie skopiowano całego klucza.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. aplikacja używa kluczy tylko do odczytu dla operacji zapisu
problem z sygnaturą 401 dla komputerów MAC występuje tylko w przypadku operacji zapisu, takich jak tworzenie lub zastępowanie, ale żądanie odczytu powiodło się.

#### <a name="solution"></a>Rozwiązanie:
Przełącz aplikację w taki sposób, aby korzystała z klucza do odczytu/zapisu, aby umożliwić pomyślne zakończenie operacji.

### <a name="4-race-condition-with-create-container"></a>4. warunek wyścigu z użyciem kontenera tworzenia
problem z podpisem MAC 401 jest widoczny wkrótce po utworzeniu kontenera. Dzieje się tak tylko do momentu zakończenia tworzenia kontenera.

#### <a name="solution"></a>Rozwiązanie:
Istnieje sytuacja wyścigu dotycząca tworzenia kontenera. Wystąpienie aplikacji próbuje uzyskać dostęp do kontenera przed ukończeniem tworzenia kontenera. Najbardziej typowym scenariuszem dla tego warunku wyścigu jest to, że aplikacja jest uruchomiona, a kontener jest usuwany i tworzony ponownie o tej samej nazwie. Zestaw SDK podejmie próbę użycia nowego kontenera, ale tworzenie kontenera nadal trwa, dlatego nie ma kluczy.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)