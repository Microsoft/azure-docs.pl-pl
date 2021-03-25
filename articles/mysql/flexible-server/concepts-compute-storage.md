---
title: Opcje obliczeniowe i magazynowe — Azure Database for MySQL — serwer elastyczny
description: W tym artykule opisano opcje obliczeń i magazynu na serwerze elastycznym Azure Database for MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7165cdc072ffaa5b0d862e1fe17f94e35c35aeec
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034541"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Opcje obliczeń i magazynowania w Azure Database for MySQL-elastycznym serwerze (wersja zapoznawcza)

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Można utworzyć Azure Database for MySQL elastyczny serwer w jednej z trzech różnych warstw obliczeniowych: możliwe do przetworzenia, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Warstwy obliczeniowe są zróżnicowane przez podstawową jednostkę SKU maszyny wirtualnej używaną przez serię B, serii D i serii E. Wybór warstwy i rozmiaru obliczeń określa pamięć i rdzeni wirtualnych dostępne na serwerze. Ta sama technologia magazynowania jest używana we wszystkich warstwach obliczeniowych. Wszystkie zasoby są obsługiwane na poziomie serwera MySQL. Serwer może mieć jedną lub wiele baz danych.

| Zasób/warstwa | **Z możliwością przenoszenia** | **Ogólnego przeznaczenia** | **Zoptymalizowane pod kątem pamięci** |
|:---|:----------|:--------------------|:---------------------|
| Serie maszyn wirtualnych| Seria B | Seria Ddsv4 | Seria Edsv4|
| Rdzeni wirtualnych | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Pamięć na rdzeń wirtualny | Zmienna | 4 GiB | 8 GiB * |
| Rozmiar magazynu | 5 GiB do 16 TiB | 5 GiB do 16 TiB | 5 GiB do 16 TiB |
| Okres przechowywania kopii zapasowej bazy danych | od 1 do 35 dni | od 1 do 35 dni | od 1 do 35 dni |

\* Z wyjątkiem jednostki SKU E64ds_v4 (zoptymalizowanej pod kątem pamięci), która ma 504 GB pamięci

Aby wybrać warstwę obliczeniową, należy użyć poniższej tabeli jako punktu wyjścia.

| Warstwa zasobów obliczeniowych | Docelowe obciążenia |
|:-------------|:-----------------|
| Z możliwością przenoszenia | Najlepsze w przypadku obciążeń, które nie potrzebują pełnego procesora CPU w sposób ciągły. |
| Ogólnego przeznaczenia | Większość obciążeń firmowych, które wymagają zrównoważonych obliczeń i pamięci dzięki skalowalnej przepływności we/wy. Są to na przykład serwery do hostowania aplikacji internetowych i mobilnych oraz inne aplikacje dla przedsiębiorstw.|
| Optymalizacja pod kątem pamięci | Obciążenia baz danych o wysokiej wydajności, które wymagają wydajności w pamięci w celu przyspieszenia przetwarzania transakcji i wyższego współbieżności. Są to na przykład serwery przetwarzające dane w czasie rzeczywistym oraz wysokowydajne aplikacje transakcyjne i analityczne.|

Po utworzeniu serwera zostanie zmieniona warstwa obliczeniowa, rozmiar obliczeń i rozmiar magazynu. Skalowanie obliczeniowe wymaga ponownego uruchomienia i trwa od 60-120 sekund, podczas gdy skalowanie magazynu nie wymaga ponownego uruchomienia. Można też niezależnie dostosować okres przechowywania kopii zapasowej w górę lub w dół. Aby uzyskać więcej informacji, zobacz sekcję [skalowanie zasobów](#scale-resources) .

## <a name="compute-tiers-size-and-server-types"></a>Warstwy obliczeń, rozmiar i typy serwerów

Zasoby obliczeniowe mogą być wybierane na podstawie warstwy i rozmiaru. Określa rdzeni wirtualnych i rozmiar pamięci. Rdzeni wirtualnych reprezentuje logiczny procesor CPU bazowego sprzętu.

Szczegółowe specyfikacje dostępnych typów serwerów są następujące:

| Rozmiar obliczeń         | Rdzeni wirtualnych | Rozmiar pamięci (GiB) | Maksymalna obsługiwana liczba IOPS | Maksymalna obsługiwana przepustowość we/wy (MB/s)|
|----------------------|--------|-------------------| ------------------ |-----------------------------------|
| **Z możliwością przenoszenia**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 
| Standard_B1ms        | 1      | 2                 | 640                | 10                                |
| Standard_B2s         | 2      | 4                 | 1280               | 15                                |
| **Ogólnego przeznaczenia**  |        |                   |                    |                                   |
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                |
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                |
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               |
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               |
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               |
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              |
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              |
| **Zoptymalizowane pod kątem pamięci** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                |
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                |
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               |
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               |
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               |
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              |
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              |

Aby uzyskać więcej szczegółowych informacji o dostępnych seriach obliczeniowych, zapoznaj się z dokumentacją maszyn wirtualnych platformy Azure w celu ich przełożenia ( [Seria B)](../../virtual-machines/sizes-b-series-burstable.md), [ogólnego przeznaczenia (Ddsv4)](../../virtual-machines/ddv4-ddsv4-series.md)i [zoptymalizowana pod kątem pamięci (seria Edsv4)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>W przypadku [warstwy obliczeniowej serii B](../../virtual-machines/sizes-b-series-burstable.md) , jeśli maszyna wirtualna zostanie uruchomiona/zatrzymana lub uruchomiona ponownie, kredyty mogą zostać utracone. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące możliwości pierścieniowania (Seria B)](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Storage

Zainicjowanym magazynem jest ilość miejsca do magazynowania dostępna dla serwera elastycznego. Magazyn jest używany dla plików bazy danych, plików tymczasowych, dzienników transakcji i dzienników serwera MySQL. W przypadku wszystkich warstw obliczeniowych Minimalna obsługiwana wielkość magazynu to 5 GiB, a maksymalna to 16 TiB. Magazyn jest skalowany w 1 GiB przyrostach i może być skalowany w górę po utworzeniu serwera.

>[!NOTE]
> Magazyn można skalować w górę, nie w dół.

Użycie magazynu w Azure Portal (z Azure Monitor) można monitorować przy użyciu metryk magazynu, procentu magazynu i użytych zasobów magazynu. Zapoznaj się z [artykułem monitorowanie](./concepts-monitoring.md) , aby dowiedzieć się więcej o metrykach. 

### <a name="reaching-the-storage-limit"></a>Osiągnięcie limitu magazynu

Gdy Magazyn używany na serwerze zbliża się do osiągnięcia limitu, serwer jest w trybie tylko do odczytu, aby chronić wszelkie utracone zapisy na serwerze. Serwery z magazynem o rozmiarze mniejszym niż 100 GiB są oznaczone jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5% rozmiaru magazynu. Serwery z więcej niż 100 GiB magazynu są oznaczone jako tylko do odczytu, jeśli ilość wolnego miejsca w magazynie jest mniejsza niż 5 GiB.

Na przykład jeśli masz zainicjowaną 110 GiB magazynu, a rzeczywiste użycie przejdzie przez 105 GiB, serwer jest oznaczony jako tylko do odczytu. Alternatywnie w przypadku aprowizacji 5 GiB magazynu serwer jest oznaczony jako tylko do odczytu, gdy ilość wolnego miejsca osiągnie mniej niż 256 MB.

Podczas gdy usługa próbuje przełączyć serwer w tryb tylko do odczytu, wszystkie nowe żądania transakcji zapisu są blokowane, a istniejące aktywne transakcje nadal są wykonywane. Gdy serwer zostanie przełączony w tryb tylko do odczytu, wszystkie kolejne zatwierdzenia transakcji i operacji zapisu zakończą się niepowodzeniem. Zapytania odczytu będą działać bez żadnych przerw. 

Aby uzyskać serwer z trybu tylko do odczytu, należy zwiększyć magazyn aprowizacji na serwerze. Można to zrobić za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. Po zwiększeniu serwer będzie gotowy do akceptowania transakcji zapisu.

Zalecamy skonfigurowanie alertu, aby powiadomić Cię, gdy magazyn serwera zbliża się do progu, aby można było uniknąć przekroczenia stanu tylko do odczytu. Zapoznaj się z [artykułem monitorowanie](./concepts-monitoring.md) , aby dowiedzieć się więcej o dostępnych metrykach. 

Zalecamy, aby <!--turn on storage auto-grow or to--> Skonfiguruj alert, aby powiadomić Cię, gdy serwer serwera zbliża się do progu, aby można było uniknąć uzyskiwania informacji o stanie tylko do odczytu. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą alertu [jak skonfigurować alert](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Autouzupełnianie magazynu

Funkcja autozwiększania magazynu nie jest jeszcze dostępna dla Azure Database for MySQL elastyczny serwer.

## <a name="iops"></a>Liczba operacji we/wy na sekundę

Azure Database for MySQL — elastyczny serwer obsługuje Inicjowanie obsługi dodatkowych IOPS. Ta funkcja umożliwia udostępnianie dodatkowych operacji we/wy na wyższy limit liczby operacji we/wy na sekundę. Korzystając z tej funkcji, można zwiększyć lub zmniejszyć liczbę operacji we/wy, która została zainicjowana na podstawie wymagań dotyczących obciążenia w dowolnym momencie. 

Minimalna liczba operacji we/wy jest 100 dla wszystkich rozmiarów obliczeń, a maksymalna wartość IOPS zależy od wybranego rozmiaru obliczenia. W wersji zapoznawczej Maksymalna obsługiwana liczba IOPS to 20 000 operacji we/wy na sekundę.

Aby dowiedzieć się więcej na temat maksymalnej liczby operacji we/wy na rozmiar obliczeń, poniżej pokazano poniżej: 

| Rozmiar obliczeń         | Maksymalna liczba IOPS        | 
|----------------------|---------------------|
| **Z możliwością przenoszenia**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Ogólnego przeznaczenia**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Zoptymalizowane pod kątem pamięci** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

Maksymalna liczba operacji we/wy zależy od maksymalnej liczby operacji we/wy na rozmiar obliczeniowy. Zapoznaj się z kolumną *Maksymalna przepływność dysku w pamięci podręcznej: IOPS/MB/s* w dokumentacji serii [B](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4](../../virtual-machines/ddv4-ddsv4-series.md)i [Edsv4](../../virtual-machines/edv4-edsv4-series.md) .

> [!Important]
> Liczby **bezpłatnych** operacji we/wy są równe minimum ("Maksymalna przepływność dysku niebuforowanego: IOPS/MB/s" rozmiaru obliczeń, magazyn udostępniony w GIB * 3)<br>
> **Minimalna liczba IOPS** jest 100 dla wszystkich rozmiarów obliczeń<br>
> **Maksymalna liczba operacji we/wy** zależy od wybranego rozmiaru obliczenia. W wersji zapoznawczej Maksymalna obsługiwana liczba IOPS to 20 000 operacji we/wy na sekundę.

Możesz monitorować użycie we/wy w Azure Portal (z Azure Monitor) przy użyciu metryki [procentowej wejścia](./concepts-monitoring.md) /wyjścia. Jeśli potrzebujesz większej liczby operacji we/wy, Maksymalna liczba IOPS na podstawie obliczeń jest wymagana do skalowania obliczeniowego serwera.

## <a name="backup"></a>Backup

Usługa automatycznie pobiera kopie zapasowe serwera. Możesz wybrać okres przechowywania z zakresu od 1 do 35 dni. Więcej informacji o kopiach zapasowych znajduje się w [artykule dotyczącym tworzenia kopii zapasowych i przywracania](concepts-backup-restore.md).

## <a name="scale-resources"></a>Skalowanie zasobów

Po utworzeniu serwera można niezależnie zmieniać warstwę obliczeń, rozmiar obliczeń (rdzeni wirtualnych i pamięć) oraz ilość miejsca do magazynowania oraz okres przechowywania kopii zapasowych. Rozmiar obliczeń można skalować w górę lub w dół. Okres przechowywania kopii zapasowej można skalować w górę lub w dół od 1 do 35 dni. Rozmiar magazynu można zwiększyć tylko. Skalowanie zasobów można przeprowadzić za pomocą portalu lub interfejsu wiersza polecenia platformy Azure.

> [!NOTE]
> Rozmiar magazynu można zwiększyć tylko. Po zwiększeniu nie można wrócić do mniejszego rozmiaru magazynu.

W przypadku zmiany warstwy obliczeniowej lub rozmiaru obliczenia serwer zostanie uruchomiony ponownie, aby nowy typ serwera zaczęł obowiązywać. Podczas przełączania systemu do nowego serwera nie można nawiązywać nowych połączeń, a wszystkie niezatwierdzone transakcje zostaną wycofane. To okno jest różne, ale w większości przypadków jest między 60-120 sekund. 

Skalowanie magazynu i zmiana okresu przechowywania kopii zapasowych są operacjami online i nie wymaga ponownego uruchomienia serwera.

## <a name="pricing"></a>Ceny

Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/MySQL/)usługi. Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) przedstawia miesięczny koszt na karcie **obliczenia + magazyn** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** , wybierz **Azure Database for MySQL** i **elastyczny serwer** jako typ wdrożenia, aby dostosować opcje.

Jeśli chcesz zoptymalizować koszt serwera, możesz rozważyć następujące wskazówki:

- Skalowanie w dół warstwy obliczeniowej lub rozmiaru obliczeń (rdzeni wirtualnych) w przypadku niedostatecznego wykorzystania zasobów obliczeniowych.
- Rozważ przełączenie do warstwy obliczeniowej z możliwością naliczania, jeśli obciążenie nie wymaga ciągłego zwiększenia pojemności obliczeniowej z warstw Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
- Zatrzymaj serwer, gdy nie jest używany.
- Zmniejszenie okresu przechowywania kopii zapasowej, jeśli nie jest wymagane przechowywanie kopii zapasowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak utworzyć serwer MySQL w portalu](quickstart-create-server-portal.md).
- Dowiedz się więcej o [ograniczeniach usługi](concepts-limitations.md).
