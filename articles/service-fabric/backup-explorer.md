---
title: Lokalne odczytywanie i aktualizowanie niezawodnej kopii zapasowej kolekcji
description: Użyj Eksploratora kopii zapasowych na platformie Azure Service Fabric, aby odczytywać i aktualizować kopie zapasowe lokalnych niezawodnych kolekcji.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048072"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Odczytywanie i aktualizowanie niezawodnej kolekcji kopii zapasowej za pomocą Eksploratora kopii zapasowych

Eksplorator usługi Kopia zapasowa Azure Service Fabric ułatwia korektę danych, jeśli dane są uszkodzone w Service Fabric niezawodnych kolekcjach. Bieżący stan danych może być uszkodzony przez jakąkolwiek usterkę, która została wprowadzona w aplikacji lub przez nieprawidłowe wpisy wprowadzone w klastrze działającym na żywo.

Za pomocą Eksploratora kopii zapasowych można wykonywać następujące zadania:
-   Zbadaj metadane dla kolekcji.
-   Wyświetl bieżący stan i jego wpisy w kolekcji załadowanej kopii zapasowej.
-   Wyświetl listę transakcji wykonanych od ostatniego punktu kontrolnego.
-   Zaktualizuj kolekcję przez dodawanie, aktualizowanie lub usuwanie wpisów w kolekcji.
-   Utwórz nową kopię zapasową, używając zaktualizowanego stanu.

> [!NOTE]
> Service Fabric narzędzia Kopia zapasowa obecnie obsługuje tylko wyświetlanie i edytowanie niezawodnych kolekcji w kopii zapasowej.
>

## <a name="access-the-backup"></a>Dostęp do kopii zapasowej

Service Fabric narzędzia Kopia zapasowa może być używana w dowolnym z następujących sposobów wyświetlania lub aktualizowania niezawodnych kolekcji w kopii zapasowej:
-   **Plik binarny**: Użyj pakietu NuGet do wyświetlania i zmieniania niezawodnych kolekcji.
-   **Http/REST**: do wyświetlania i zmieniania niezawodnych kolekcji służy serwer REST oparty na protokole HTTP.
-   **bkpctl**: Użyj Service Fabric interfejsu wiersza polecenia (CLI) Eksploratora kopii zapasowej, aby wyświetlić i zmienić kopię zapasową niezawodnych kolekcji.

Eksplorator kopii zapasowych ma bibliotekę języka C# dla zaawansowanych użytkowników. Biblioteki w aplikacji można używać bezpośrednio do pracy z niezawodnymi kolekcjami podobnymi do sposobu, w jaki klienci pracują z menedżerem stanu w swoich istniejących usługach stanowych. W przypadku użytkowników podstawowych i w podstawowym przypadku użycia Eksplorator ma również autonomiczny serwer REST, który udostępnia interfejsy API do inspekcji kopii zapasowych. Narzędzie interfejsu wiersza polecenia bkpctl działa na szczycie interfejsów API REST i opiera się na języku Python. Za pomocą narzędzia interfejsu wiersza polecenia można odczytywać i aktualizować kopie zapasowe oraz tworzyć nowe kopie zapasowe za pośrednictwem wiersza poleceń.

Aby uzyskać więcej informacji, zobacz repozytorium [Service Fabric tworzenia kopii zapasowych](https://github.com/microsoft/service-fabric-backup-explorer) w witrynie GitHub. Repozytorium zawiera informacje o źródle i wersji oraz instrukcje dotyczące instalacji.

Możesz również skompilować repozytorium lokalnie i korzystać z kopii zapasowych.
 
Pakiet NuGet dla Eksploratora kopii zapasowych (Microsoft. servicefabric. ReliableCollectionBackup. Parser) będzie dostępny w witrynie [NuGet.org](https://www.nuget.org/). 

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej [na temat niezawodnych kolekcji w usłudze Azure Service Fabric stanowe usługi](service-fabric-reliable-services-reliable-collections.md).
* Zapoznaj się z [najlepszymi rozwiązaniami Service Fabric](./service-fabric-best-practices-security.md).