---
title: Wprowadzenie do usługi Azure cache dla warstwy Redis Premium
description: Dowiedz się, jak tworzyć i zarządzać trwałością Redis, Redis klastrowaniem i obsługą sieci wirtualnej dla usługi Azure cache w warstwie Premium dla wystąpień Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74121673"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Wprowadzenie do usługi Azure cache dla warstwy Redis Premium
Usługa Azure cache for Redis to dystrybuowana, Zarządzana pamięć podręczna, która pomaga tworzyć wysoce skalowalne i reagujące aplikacje, zapewniając szybki dostęp do danych. 

Nowa warstwa Premium jest warstwą gotową dla przedsiębiorstw, która obejmuje wszystkie funkcje warstwy Standardowa i inne, takie jak lepsza wydajność, większe obciążenia, odzyskiwanie po awarii, import/eksport i zwiększone zabezpieczenia. Kontynuuj odczytywanie, aby dowiedzieć się więcej o dodatkowych funkcjach warstwy pamięci podręcznej Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepsza wydajność w porównaniu z warstwą standardowa lub podstawowa
**Lepsza wydajność w warstwie Standardowa lub podstawowa.** Pamięć podręczna w warstwie Premium są wdrażane na sprzęcie z szybszymi procesorami i zapewnia lepszą wydajność w porównaniu z warstwą podstawowa lub standardowa. Pamięć podręczna warstwy Premium ma wyższą przepływność i mniejsze opóźnienia. 

**Przepływność dla tego samego rozmiaru pamięci podręcznej jest wyższa w porównaniu do warstwy Standardowa.** Na przykład przepływność pamięci podręcznej 53 GB P4 (Premium) to 250 000 żądania na sekundę w porównaniu do 150 tys. dla C6 (standard).

Aby uzyskać więcej informacji o rozmiarze, przepływności i przepustowości z pamięcią podręczną Premium, zobacz [Azure cache for Redis — często zadawane pytania](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Trwałość danych Redis
Warstwa Premium umożliwia utrwalanie danych w pamięci podręcznej na koncie usługi Azure Storage. W pamięci podręcznej podstawowa/standardowa wszystkie dane są przechowywane tylko w pamięci. W przypadku problemów związanych z infrastrukturą może istnieć potencjalna utrata danych. Zalecamy używanie funkcji trwałości danych Redis w warstwie Premium w celu zwiększenia odporności na utratę danych. Usługa Azure cache for Redis oferuje opcje RDB i kopia zapasowa AOF (dostępne wkrótce) w przypadku [trwałości Redis](https://redis.io/topics/persistence). 

Aby uzyskać instrukcje dotyczące konfigurowania trwałości, zobacz [jak skonfigurować trwałość dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Klaster Redis
Jeśli chcesz utworzyć pamięć podręczną o rozmiarze większym niż 53 GB lub chcesz fragmentu dane między wieloma węzłami Redis, możesz użyć klastrowania Redis, który jest dostępny w warstwie Premium. Każdy węzeł składa się z pary pamięci podręcznej podstawowej/repliki zarządzanej przez platformę Azure w celu zapewnienia wysokiej dostępności. 

**Klastrowanie Redis zapewnia maksymalną skalę i przepływność.** Przepływność wzrasta liniowo w miarę zwiększania liczby fragmentów (węzłów) w klastrze. Na przykład: Jeśli utworzysz klaster P4 o wartości 10 fragmentów, dostępna przepływność to 250 000 * 10 = 2 500 000 żądań na sekundę. Zapoznaj się z tematem [Azure cache for Redis — często zadawane pytania](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) , aby uzyskać więcej informacji o rozmiarze, przepływności i przepustowości z pamięcią podręczną Premium.

Aby rozpocząć pracę z klastrem, zobacz [jak skonfigurować klastrowanie dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Ulepszone zabezpieczenia i izolacja
Pamięci podręczne utworzone w warstwie Podstawowa lub standardowa są dostępne w publicznym Internecie. Dostęp do pamięci podręcznej jest ograniczony w oparciu o klucz dostępu. Za pomocą warstwy Premium można zapewnić, że tylko klienci w określonej sieci będą mogli uzyskać dostęp do pamięci podręcznej. Usługę Azure cache for Redis można wdrożyć w usłudze [azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/). Aby jeszcze bardziej ograniczyć dostęp do serwera Redis, można użyć wszystkich funkcji VNet, np. podsieci, zasad kontroli dostępu oraz innych funkcji.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować obsługę usługi Virtual Network w przypadku pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/Export to pamięć podręczna platformy Azure do Redis operacji zarządzania danymi, która umożliwia importowanie danych do usługi Azure cache dla Redis lub eksportowanie danych z pamięci podręcznej platformy Azure dla Redis przez zaimportowanie i eksportowanie pamięci podręcznej platformy Azure dla migawki bazy danych Redis Database (RDB) z pamięci podręcznej Premium do strony obiektu BLOB na koncie usługi Azure Storage. Dzięki temu można migrować między różnymi pamięcią podręczną platformy Azure dla wystąpień Redis lub wypełnić pamięć podręczną danymi przed użyciem.

Import może służyć do przenoszenia zgodnych plików RDB Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub dowolnym dostawcy chmury, takim jak Amazon Web Services i inne. Importowanie danych to prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure cache for Redis ładuje pliki RDB z usługi Azure Storage do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Eksport pozwala wyeksportować dane przechowywane w pamięci podręcznej platformy Azure dla Redis do Redis zgodnych plików RDB. Ta funkcja służy do przenoszenia danych z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która obsługuje wystąpienie serwera usługi Azure cache for Redis, i plik zostanie przekazany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie plik tymczasowy zostanie usunięty.

Aby uzyskać więcej informacji, zobacz [jak importować dane do i eksportować dane z usługi Azure cache for Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Ponowne uruchamianie
Warstwa Premium umożliwia ponowne uruchomienie jednego lub większej liczby węzłów pamięci podręcznej na żądanie. Dzięki temu można testować aplikację pod kątem odporności w przypadku awarii. Można uruchomić ponownie następujące węzły.

* Główny węzeł pamięci podręcznej
* Pomocniczy węzeł pamięci podręcznej
* Podstawowy i pomocniczy węzeł pamięci podręcznej
* W przypadku korzystania z pamięci podręcznej Premium z klastrowaniem można uruchomić ponownie podstawowe, pomocnicze lub oba węzły dla poszczególnych fragmentów w pamięci podręcznej

Aby uzyskać więcej informacji, zobacz temat [Ponowne uruchamianie](cache-administration.md#reboot) i [Ponowne uruchamianie — często zadawane pytania](cache-administration.md#reboot-faq).

>[!NOTE]
>Funkcja ponownego uruchamiania jest teraz włączona dla wszystkich warstw usługi Azure cache for Redis.
>
>

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
Funkcja zaplanowane aktualizacje umożliwia wyznaczenie okna obsługi pamięci podręcznej. Po określeniu okna obsługi wszystkie aktualizacje serwera Redis są wykonywane w tym oknie. Aby wyznaczyć okno obsługi, wybierz odpowiednie dni i określ godzinę rozpoczęcia okna obsługi dla każdego dnia. Należy pamiętać, że czas okna obsługi jest w formacie UTC. 

Aby uzyskać więcej informacji, zobacz [Planowanie aktualizacji](cache-administration.md#schedule-updates) i [Planowanie często zadawanych aktualizacji](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> W oknie zaplanowanej konserwacji są wykonywane tylko aktualizacje serwera Redis. Okno obsługi nie ma zastosowania do aktualizacji lub aktualizacji platformy Azure dla systemu operacyjnego maszyny wirtualnej.
> 
> 

## <a name="geo-replication"></a>Replikacja geograficzna

**Replikacja geograficzna** zapewnia mechanizm łączenia dwóch pamięci podręcznej platformy Azure w warstwie Premium dla wystąpień Redis. Jedna pamięć podręczna jest wyznaczyna jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączona pamięć podręczna. Pomocnicza połączonej pamięci podręcznej jest tylko do odczytu, a dane zapisywane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Tej funkcji można użyć do replikowania pamięci podręcznej w regionach platformy Azure.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować replikację geograficzną dla usługi Azure cache for Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Aby skalować do warstwy Premium
Aby skalować do warstwy Premium, wystarczy wybrać jedną z warstw Premium w bloku **Zmień warstwę cenową** . Możesz również skalować pamięć podręczną do warstwy Premium przy użyciu programu PowerShell i interfejsu wiersza polecenia. Instrukcje krok po kroku znajdują się w temacie [Jak skalować pamięć podręczną platformy Azure dla Redis](cache-how-to-scale.md) oraz [jak zautomatyzować operację skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Następne kroki
Utwórz pamięć podręczną i Eksploruj nowe funkcje warstwy Premium.

* [Jak skonfigurować trwałość pamięci podręcznej systemu Azure w warstwie Premium dla Redis](cache-how-to-premium-persistence.md)
* [Jak skonfigurować obsługę Virtual Network w przypadku pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-vnet.md)
* [Jak skonfigurować klastrowanie pamięci podręcznej systemu Azure w warstwie Premium dla Redis](cache-how-to-premium-clustering.md)
* [Jak importować dane do i eksportować dane z usługi Azure cache for Redis](cache-how-to-import-export-data.md)
* [Jak administrować usługą Azure cache for Redis](cache-administration.md)

