---
title: Podwójne szyfrowanie infrastruktury — Azure Database for MySQL
description: Informacje o korzystaniu z szyfrowania podwójnego infrastruktury w celu dodania drugiej warstwy szyfrowania przy użyciu kluczy zarządzanych przez usługę.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 233dcbeee0bccc714e3b4fe93e7c8b19aa9f2df0
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242453"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database for MySQL podwójne szyfrowanie infrastruktury

Azure Database for MySQL korzysta [z szyfrowania magazynu danych w usłudze REST](concepts-security.md#at-rest) dla danych przy użyciu kluczy zarządzanych przez firmę Microsoft. Dane, w tym kopie zapasowe, są szyfrowane na dysku, a szyfrowanie jest zawsze włączone i nie można go wyłączyć. Szyfrowanie używa zatwierdzonego modułu kryptograficznego FIPS 140-2 i szyfru AES 256-bit dla szyfrowania usługi Azure Storage.

Podwójne szyfrowanie infrastruktury dodaje drugą warstwę szyfrowania przy użyciu kluczy zarządzanych przez usługę. Używa on zatwierdzonego modułu kryptograficznego FIPS 140-2, ale z innym algorytmem szyfrowania. Zapewnia to dodatkową warstwę ochrony danych przechowywanych w usłudze. Klucz używany w przypadku szyfrowania podwójnego infrastruktury jest również zarządzany przez usługę Azure Database for MySQL. Podwójne szyfrowanie infrastruktury nie jest domyślnie włączone, ponieważ dodatkowa warstwa szyfrowania może mieć wpływ na wydajność.

> [!NOTE]
> Ta funkcja jest obsługiwana tylko dla warstw cenowych "Ogólnego przeznaczenia" i "zoptymalizowanych pod kątem pamięci" w Azure Database for PostgreSQL.

Zaletą szyfrowania warstwy infrastruktury jest implementacja na warstwie najbliżej urządzenia magazynującego lub sieci. Azure Database for MySQL implementuje dwie warstwy szyfrowania przy użyciu kluczy zarządzanych przez usługę. Mimo że w warstwie usług nadal są technicznie, jest to bardzo bliski sprzęt przechowujący dane. Nadal można opcjonalnie włączyć szyfrowanie danych w spoczynku przy użyciu [klucza zarządzanego przez klienta](concepts-data-encryption-mysql.md) dla aprowizacji serwera MySQL. 

Implementacja w warstwach infrastruktury obsługuje również różnorodność kluczy. Infrastruktura musi mieć świadomość różnych klastrów maszyn i sieci. W związku z tym różne klucze są używane w celu zminimalizowania ryzyka związanego z infrastrukturą oraz wielu awarii sprzętu i sieci. 

> [!NOTE]
> Użycie podwójnego szyfrowania infrastruktury będzie miało wpływ na wydajność na serwerze Azure Database for MySQL z powodu dodatkowego procesu szyfrowania.

## <a name="benefits"></a>Korzyści

Podwójne szyfrowanie infrastruktury dla Azure Database for MySQL zapewnia następujące korzyści:

1. **Dodatkowa różnorodność implementacji kryptograficznej** — planowane przejście do szyfrowania opartego na sprzęcie spowoduje dalsze zróżnicowanie implementacji przez udostępnienie implementacji opartej na sprzęcie oraz implementacji opartej na oprogramowaniu.
2. **Błędy implementacji** — dwie warstwy szyfrowania w warstwie infrastruktury chronią przed wszelkimi błędami w pamięci podręcznej lub zarządzaniu pamięcią na wyższych warstwach, które udostępniają dane zwykłego tekstu. Ponadto dwie warstwy są również chronione przed błędami w implementacji szyfrowania.

Połączenie tych elementów zapewnia silną ochronę przed typowymi zagrożeniami i słabościmi używanymi do zaatakowania kryptografii.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Scenariusze obsługiwane z podwójnym szyfrowaniem infrastruktury

Funkcje szyfrowania zapewniane przez Azure Database for MySQL mogą być używane razem. Poniżej znajduje się podsumowanie różnych scenariuszy, których można użyć:

|  ##   | Domyślne szyfrowanie | Podwójne szyfrowanie infrastruktury | Szyfrowanie danych przy użyciu kluczy zarządzanych przez klienta  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Tak*              | *Nie*                             | *Nie*                                         |
| 2     | *Tak*              | *Tak*                            | *Nie*                                         |
| 3     | *Tak*              | *Nie*                             | *Tak*                                        |
| 4     | *Tak*              | *Tak*                            | *Tak*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Scenariusz 2 i 4 mogą spowodować spadek przepływności 5-10 procent na podstawie typu obciążenia dla Azure Database for MySQL serwera z powodu dodatkowej warstwy szyfrowania infrastruktury.
> - Konfigurowanie podwójnego szyfrowania infrastruktury dla Azure Database for MySQL jest dozwolone tylko podczas tworzenia serwera. Po aprowizacji serwera nie można zmienić szyfrowania magazynu. Jednak nadal można włączyć szyfrowanie danych przy użyciu kluczy zarządzanych przez klienta dla serwera utworzonego za pomocą podwójnego szyfrowania infrastruktury/bez.

## <a name="limitations"></a>Ograniczenia

W przypadku Azure Database for MySQL Obsługa podwójnego szyfrowania infrastruktury przy użyciu klucza zarządzanego przez usługę ma następujące ograniczenia:

* Obsługa tej funkcji jest ograniczona do warstw cenowych **ogólnego przeznaczenia** i **zoptymalizowanych pod kątem pamięci** .
* Azure Database for MySQL z szyfrowaniem infrastruktury można utworzyć w następujących regionach:

   * East US
   * South Central US
   * Zachodnie stany USA 2
   
* * Ta funkcja jest obsługiwana tylko w regionach i serwerach, które obsługują magazyn do 16 TB. Listę regionów świadczenia usługi Azure obsługującą magazyn o pojemności do 16 TB można znaleźć w [dokumentacji magazynu](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Wszystkie **nowe** serwery MySQL utworzone w wymienionych powyżej regionach obsługują również szyfrowanie danych za pomocą kluczy menedżera klienta. W takim przypadku serwery utworzone za poorednictwem przywracania do punktu w czasie (kopie) lub odczytu replik nie kwalifikują się do "New".
    > - Aby sprawdzić, czy serwer aprowizacji obsługuje maksymalnie 16 TB, można przejść do bloku warstwa cenowa w portalu i sprawdzić, czy można przenieść suwak magazynu o do 16 TB. Jeśli suwak można przesuwać do maksymalnie 4 TB, serwer może nie obsługiwać szyfrowania z użyciem kluczy zarządzanych przez klienta; Jednak dane są szyfrowane przy użyciu kluczy zarządzanych przez usługę przez cały czas. AskAzureDBforMySQL@service.microsoft.comJeśli masz jakieś pytania, skontaktuj się z Tobą.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować podwójne szyfrowanie infrastruktury dla usługi Azure Database for MySQL](howto-double-encryption.md).
