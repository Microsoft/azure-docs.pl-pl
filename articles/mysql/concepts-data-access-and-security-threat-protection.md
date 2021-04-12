---
title: Zaawansowana ochrona przed zagrożeniami — Azure Database for MySQL
description: Poznaj koncepcje dotyczące zaawansowanej ochrony przed zagrożeniami, która wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542579"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL zaawansowaną ochronę przed zagrożeniami

Usługa Advanced Threat Protection dla usługi Azure Database for MySQL wykrywa nietypowe działania wskazujące na niestandardowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

> [!NOTE]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej.

Zaawansowana ochrona przed zagrożeniami jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pomocą [Azure Portal](https://portal.azure.com) lub [interfejsu API REST](/rest/api/mysql). Ta funkcja jest dostępna dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach na platformie Azure dla instytucji rządowych i w ramach suwerennej chmury: US Gov Teksas, US Gov Arizona, US gov Iowa, US, gov wirginia, US DoD (region wschodni), US dod (region środkowy), Niemcy środkowe, Niemcy północne, Chiny Wschodnie, Chiny Wschodnie 2. Odwiedź stronę [dostępne produkty według regionów,](https://azure.microsoft.com/global-infrastructure/services/) Aby uzyskać ogólną dostępność produktu.


## <a name="what-is-advanced-threat-protection"></a>Co to jest zaawansowana ochrona przed zagrożeniami?

Zaawansowana ochrona przed zagrożeniami dla Azure Database for MySQL zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań. Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych oraz potencjalne luki w zabezpieczeniach, a także nietypowego dostępu do bazy danych i wzorców zapytań. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MySQL integruje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecane działania dotyczące badania i łagodzenia zagrożeń. Zaawansowana ochrona przed zagrożeniami dla Azure Database for MySQL ułatwia rozwiązywanie potencjalnych zagrożeń dla bazy danych bez konieczności zapewnienia ekspertowi zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Koncepcja zaawansowanej ochrony przed zagrożeniami":::

## <a name="advanced-threat-protection-alerts"></a>Alerty zaawansowanej ochrony przed zagrożeniami 
Zaawansowana ochrona przed zagrożeniami dla Azure Database for MySQL wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania i może wyzwolić następujące alerty:
- **Dostęp z nietypowej lokalizacji**: ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MySQL, w którym ktoś zalogował się do serwera Azure Database for MySQL z nietypowej lokalizacji geograficznej. W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja lub konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nietypowego centrum danych platformy Azure**: ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MySQL, w którym ktoś zalogował się na serwerze z nietypowego centrum danych platformy Azure, które było widoczne na tym serwerze w ostatnim okresie. W niektórych przypadkach alert wykrywa legalną akcję (nową aplikację na platformie Azure, Power BI, Azure Database for MySQL edytorze zapytań). W innych przypadkach ten alert wykrywa złośliwe działanie z zasobu/usługi platformy Azure (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z nieznanego podmiotu zabezpieczeń**: ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do serwera Azure Database for MySQL, w którym ktoś zalogował się na serwer przy użyciu nietypowego podmiotu zabezpieczeń (Azure Database for MySQL użytkownika). W niektórych przypadkach ten alert wykrywa prawidłowe działanie (nowa aplikacja, konserwacja przeprowadzana przez deweloperów). W innych przypadkach ten alert wykrywa złośliwe działanie (były pracownik, zewnętrzna osoba atakująca).
- **Dostęp z potencjalnie szkodliwej aplikacji**: Ten alert jest wyzwalany, gdy potencjalnie szkodliwa aplikacja jest używana w celu uzyskania dostępu do bazy danych. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak przy użyciu typowych narzędzi ataku.
- **Wymuszanie Azure Database for MySQL poświadczeń**: ten alert jest wyzwalany, gdy występuje nietypowa duża liczba nieudanych logowań z innymi poświadczeniami. W niektórych przypadkach ten alert wykrywa przeprowadzany test penetracji. W innych przypadkach ten alert wykrywa atak siłowy.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Security Center](../security-center/security-center-introduction.md)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) . 
* Konfigurowanie [Azure Database for MySQL zaawansowanej ochrony przed zagrożeniami](howto-database-threat-protection-portal.md) przy użyciu Azure Portal