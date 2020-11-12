---
title: Zaawansowana ochrona przed zagrożeniami — Azure Database for MariaDB
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536201"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB zaawansowaną ochronę przed zagrożeniami

Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

> [!IMPORTANT]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej.

Zaawansowana ochrona przed zagrożeniami jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem [Azure Portal](https://portal.azure.com). Ta funkcja jest dostępna dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach na platformie Azure dla instytucji rządowych i w ramach suwerennej chmury: US Gov Teksas, US Gov Arizona, US gov Iowa, US, gov wirginia, US DoD (region wschodni), US dod (region środkowy), Niemcy środkowe, Niemcy północne, Chiny Wschodnie, Chiny Wschodnie 2. Odwiedź stronę [dostępne produkty według regionów,](https://azure.microsoft.com/global-infrastructure/services/) Aby uzyskać ogólną dostępność produktu.


## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami?

Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań. Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych oraz potencjalne luki w zabezpieczeniach, a także nietypowego dostępu do bazy danych i wzorców zapytań. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB integruje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące badania i łagodzenia zagrożeń. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB ułatwia rozwiązywanie potencjalnych zagrożeń dla bazy danych bez konieczności zapewnienia ekspertowi zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń. 

![Koncepcja zaawansowanej ochrony przed zagrożeniami](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alerty zaawansowanej ochrony przed zagrożeniami 
Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:
- **Dostęp z nietypowej lokalizacji** : ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MariaDB, w którym ktoś zalogował się do serwera Azure Database for MariaDB z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure** : ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MariaDB, w którym ktoś zalogował się na serwerze z nietypowego centrum danych platformy Azure, które było widoczne na tym serwerze w ostatnim okresie. W niektórych przypadkach alert wykrywa legalną akcję (nową aplikację na platformie Azure, Power BI). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń** : ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MariaDB, w którym ktoś zalogował się na serwer przy użyciu nietypowego podmiotu zabezpieczeń (Azure Database for MariaDB użytkownika). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji** : Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Wymuszanie Azure Database for MariaDB poświadczeń** : ten alert jest wyzwalany, gdy występuje nietypowa duża liczba nieudanych logowań z innymi poświadczeniami. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Security Center](../security-center/security-center-introduction.md)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) . 
* Konfigurowanie [Azure Database for MariaDB zaawansowanej ochrony przed zagrożeniami](howto-database-threat-protection-portal.md) przy użyciu Azure Portal