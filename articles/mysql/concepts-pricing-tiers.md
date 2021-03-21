---
title: Warstwy cenowe — Azure Database for MySQL
description: Poznaj różne warstwy cenowe dla Azure Database for MySQL, w tym generacji obliczeń, typy magazynów, rozmiar magazynu, rdzeni wirtualnych, pamięć i okresy przechowywania kopii zapasowych.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: be7f15b5221be8b3acb7f64c4435e40f40f21f8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720923"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database for MySQL warstw cenowych

Serwer Azure Database for MySQL można utworzyć w jednej z trzech różnych warstw cenowych: podstawowe, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Warstwy cenowe są zróżnicowane według ilości obliczeń w rdzeni wirtualnych, które mogą być inicjowane, pamięć na rdzeń wirtualny i technologia magazynowania służąca do przechowywania danych. Wszystkie zasoby są obsługiwane na poziomie serwera MySQL. Serwer może mieć jedną lub wiele baz danych.

| Atrybut   | **Podstawowa** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Generowanie obliczeń | Gen 4, Gen 5 | Gen 4, Gen 5 | 5. generacja |
| Rdzeni wirtualnych | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Pamięć na rdzeń wirtualny | 2 GB | 5 GB | 10 GB |
| Rozmiar magazynu | od 5 GB do 1 TB | od 5 GB do 16 TB | od 5 GB do 16 TB |
| Okres przechowywania kopii zapasowej bazy danych | od 7 do 35 dni | od 7 do 35 dni | od 7 do 35 dni |

Aby wybrać warstwę cenową, należy użyć poniższej tabeli jako punktu wyjścia.

| Warstwa cenowa | Docelowe obciążenia |
|:-------------|:-----------------|
| Podstawowa | Obciążenia, które wymagają lekkich obliczeń i wydajności operacji we/wy. Przykłady obejmują serwery używane do programowania lub testowania oraz nierzadko używane aplikacje. |
| Ogólnego przeznaczenia | Większość obciążeń firmowych, które wymagają zrównoważonych obliczeń i pamięci dzięki skalowalnej przepływności we/wy. Są to na przykład serwery do hostowania aplikacji internetowych i mobilnych oraz inne aplikacje dla przedsiębiorstw.|
| Optymalizacja pod kątem pamięci | Obciążenia baz danych o wysokiej wydajności, które wymagają wydajności w pamięci w celu przyspieszenia przetwarzania transakcji i wyższego współbieżności. Są to na przykład serwery przetwarzające dane w czasie rzeczywistym oraz wysokowydajne aplikacje transakcyjne i analityczne.|

Po utworzeniu serwera można zmienić liczbę rdzeni wirtualnych, generowanie sprzętu i warstwę cenową (oprócz i z Basic) w ciągu kilku sekund. Można także niezależnie dostosować ilość miejsca do magazynowania i okres przechowywania kopii zapasowych w górę lub w dół bez przestojów aplikacji. Po utworzeniu serwera nie można zmienić typu magazynu kopii zapasowej. Aby uzyskać więcej informacji, zobacz sekcję [skalowanie zasobów](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Generacja obliczeń i rdzeni wirtualnych

Zasoby obliczeniowe są udostępniane jako rdzeni wirtualnych, które reprezentują logicznego procesora bazowego sprzętu. Chiny Wschodnie 1, Chiny Północne 1, US DoD (region środkowy) i US DoD (region wschodni) używają logicznych procesorów CPU, które są oparte na procesorach Intel E5-2673 v3 (Haswell) 2,4 GHz. Wszystkie pozostałe regiony wykorzystują jednostki logiczne procesorów generacji 5, które są oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Zapewniana ilość miejsca w magazynie to pojemność magazynu dostępna dla serwera Azure Database for MySQL. Magazyn jest używany dla plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera MySQL. Całkowita ilość dostępnego miejsca w magazynie określa również wydajność we/wy dostępną dla serwera.

| Atrybut magazynu   | Podstawowa | Ogólnego przeznaczenia | Optymalizacja pod kątem pamięci |
|:---|:----------|:--------------------|:---------------------|
| Typ magazynu | Magazyn podstawowy | Magazyn Ogólnego przeznaczenia | Magazyn Ogólnego przeznaczenia |
| Rozmiar magazynu | od 5 GB do 1 TB | od 5 GB do 16 TB | od 5 GB do 16 TB |
| Rozmiar przyrostu pamięci masowej | 1 GB | 1 GB | 1 GB |
| Liczba operacji we/wy na sekundę | Zmienna |3 IOPS/GB<br/>Minimalna liczba operacji we/wy 100<br/>Maksymalna liczba operacji we/wy 20 000 | 3 IOPS/GB<br/>Minimalna liczba operacji we/wy 100<br/>Maksymalna liczba operacji we/wy 20 000 |

> [!NOTE]
> Magazyn o pojemności do 16TB i 20 000 operacji we/wy jest obsługiwany w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Brazylia Południowa, zachodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, Europa Północna, Europa Zachodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Azja Południowo-Wschodnia, Japonia Południowo-Wschodnia, Japonia Zachodnia, Korea środkowa Azja Wschodnia , Kanada Wschodnia i Kanada środkowa.
>
> Wszystkie inne regiony obsługują do 4 TB magazynu i do 6000 operacji we/wy na sekundę.
>

Można dodać dodatkową pojemność magazynu podczas i po utworzeniu serwera oraz pozwolić systemowi na automatyczne zwiększanie ilości miejsca w oparciu o użycie magazynu w ramach obciążenia. 

>[!NOTE]
> Magazyn można skalować w górę, nie w dół.

Warstwa Podstawowa nie oferuje gwarancji operacji we/wy na sekundę. W warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci liczba operacji we/wy z zainicjowanym rozmiarem magazynu wynosi 3:1.

Możesz monitorować użycie we/wy w Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure. Odpowiednie metryki do monitorowania to [Limit magazynu, procent magazynu, użycie magazynu i procent operacji we/wy](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Osiągnięcie limitu magazynu

Serwery z aprowizowanym magazynem o rozmiarze mniejszym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5% rozmiaru aprowizowanego magazynu. Serwery z aprowizowanym magazynem o rozmiarze większym niż 100 GB są oznaczane jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5 GB.

Jeśli na przykład Zainicjowano obsługę administracyjną 110 GB miejsca w magazynie, a rzeczywiste wykorzystanie przekracza 105 GB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie, jeśli masz zainicjowany 5 GB miejsca w magazynie, serwer jest oznaczony jako tylko do odczytu, gdy ilość wolnego miejsca osiągnie mniej niż 256 MB.

Podczas gdy usługa próbuje przełączyć serwer w tryb tylko do odczytu, wszystkie nowe żądania transakcji zapisu są blokowane, a istniejące aktywne transakcje nadal są wykonywane. Gdy serwer zostanie przełączony w tryb tylko do odczytu, wszystkie kolejne zatwierdzenia transakcji i operacji zapisu zakończą się niepowodzeniem. Zapytania odczytu będą działać bez żadnych przerw. Gdy zwiększysz zaaprowizowaną pojemność magazynu, serwer będzie ponownie gotowy do akceptowania transakcji zapisu.

Zalecamy włączenie opcji autowzrostu magazynu lub skonfigurowanie alertu w celu powiadomienia użytkownika o tym, że magazyn serwera zbliża się do progu, aby można było uniknąć przekroczenia stanu tylko do odczytu. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [sposobu konfigurowania alertu](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Autouzupełnianie magazynu

Automatyczne zwiększanie ilości miejsca do magazynowania uniemożliwia serwerowi wyjście z magazynu i staje się tylko do odczytu. Jeśli funkcja automatycznego zwiększania rozmiaru magazynu jest włączona, magazyn automatycznie rośnie bez wpływu na obciążenie. W przypadku serwerów o rozmiarze mniejszym niż równym 100 GB alokacja pamięci masowej jest zwiększana o 5 GB, gdy ilość wolnego miejsca w magazynie jest mniejsza niż 10% zainicjowanego magazynu. W przypadku serwerów mających więcej niż 100 GB zasobów magazynowych zainicjowany rozmiar magazynu jest zwiększany o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 10 GB rozmiaru magazynu. Obowiązują maksymalne limity magazynu określone powyżej.

Jeśli na przykład Zainicjowano obsługę administracyjną 1000 GB miejsca w magazynie, a rzeczywiste wykorzystanie przekracza 990 GB, rozmiar magazynu serwera zostanie zwiększony do 1050 GB. Alternatywnie, jeśli masz zainicjowany 10 GB miejsca w magazynie, rozmiar magazynu zostanie zwiększony do 15 GB, gdy jest mniej niż 1 GB miejsca w magazynie.

Należy pamiętać, że magazyn można skalować w górę, nie w dół.

## <a name="backup-storage"></a>Magazyn kopii zapasowych 

Azure Database for MySQL zapewnia do 100% magazynu z zainicjowaną obsługą kopii zapasowych bez dodatkowych kosztów. Wszystkie magazyny kopii zapasowych, których używasz powyżej tej ilości, są obciążone w GB miesięcznie. Jeśli na przykład udostępnisz serwer z 250 GB miejsca w magazynie, będziesz mieć do dyspozycji 250 GB dodatkowego magazynu dla kopii zapasowych serwera bez dodatkowych opłat. Magazyn kopii zapasowych o wielkości przekraczającej 250 GB jest naliczany zgodnie z [modelem cen](https://azure.microsoft.com/pricing/details/mysql/). Aby poznać czynniki wpływające na użycie magazynu kopii zapasowych, monitorowanie i kontrolowanie kosztu magazynu kopii zapasowych, można zapoznać się z [dokumentacją kopii zapasowej](concepts-backup.md).

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmienić rdzeni wirtualnych, generowanie sprzętu, warstwę cenową (poza i z warstwy Podstawowa), ilość miejsca do magazynowania oraz okres przechowywania kopii zapasowych. Po utworzeniu serwera nie można zmienić typu magazynu kopii zapasowej. Liczbę rdzeni wirtualnych można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 7 do 35 dni. Rozmiar magazynu można zwiększyć tylko. Skalowanie zasobów można przeprowadzić za pomocą portalu lub interfejsu wiersza polecenia platformy Azure. Aby zapoznać się z przykładem skalowania przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [monitorowanie i skalowanie serwera Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure](scripts/sample-scale-server.md).

W przypadku zmiany liczby rdzeni wirtualnych, wygenerowania sprzętu lub warstwy cenowej kopia oryginalnego serwera zostanie utworzona przy użyciu nowej alokacji obliczeniowej. Gdy nowy serwer zostanie uruchomiony, połączenia zostaną przełączone na nowy serwer. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. Ten przestój podczas skalowania może wynosić około 60-120 sekund. Czas przestoju podczas skalowania zależy od czasu odzyskiwania bazy danych, co może spowodować, że baza danych będzie przełączona w tryb online, jeśli na serwerze w czasie operacji skalowania wystąpi duże działania transakcyjne. Aby uniknąć dłuższego czasu ponownego uruchomienia, zaleca się wykonywanie operacji skalowania w okresach z niską aktywnością transakcyjną na serwerze.

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowych to prawdziwe operacje online. Brak przestoju i nie ma to znaczenia dla Twojej aplikacji. Jako że liczba operacji we/wy na sekundę jest skalowana wraz z rozmiarem magazynu, można zwiększyć liczbę operacji we/wy dla serwera, skalowanie w górę magazynu.

## <a name="pricing"></a>Ceny

Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/mysql/)usługi. Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) przedstawia miesięczny koszt na karcie **warstwa cenowa** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** i wybierz **Azure Database for MySQL** , aby dostosować opcje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak utworzyć serwer MySQL w portalu](howto-create-manage-server-portal.md).
- Dowiedz się więcej o [limitach usługi](concepts-limits.md).
- Dowiedz się, jak [skalować w poziomie przy użyciu replik odczytu](howto-read-replicas-portal.md).
