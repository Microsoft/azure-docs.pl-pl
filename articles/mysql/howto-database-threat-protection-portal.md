---
title: Zaawansowana ochrona przed zagrożeniami — Azure Portal-Azure Database for MySQL
description: Dowiedz się, jak skonfigurować zaawansowaną ochronę przed zagrożeniami, aby wykrywać nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 43b85ba58ed7163a896054ab9e77ddf82e3461a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902808"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Zaawansowana ochrona przed zagrożeniami dla Azure Database for MySQL

Usługa Advanced Threat Protection dla usługi Azure Database for MySQL wykrywa nietypowe działania wskazujące na niestandardowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

Zaawansowana ochrona przed zagrożeniami jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem [Azure Portal](https://portal.azure.com) i jest obecnie dostępny w wersji zapoznawczej.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach na platformie Azure dla instytucji rządowych i w ramach suwerennej chmury: US Gov Teksas, US Gov Arizona, US gov Iowa, US, gov wirginia, US DoD (region wschodni), US dod (region środkowy), Niemcy środkowe, Niemcy północne, Chiny Wschodnie, Chiny Wschodnie 2. Odwiedź stronę [dostępne produkty według regionów,](https://azure.microsoft.com/global-infrastructure/services/) Aby uzyskać ogólną dostępność produktu.
>

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w których wdrożono Azure Database for MySQL dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="set-up-threat-detection"></a>Konfigurowanie wykrywania zagrożeń
1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .
2. Przejdź do strony konfiguracji serwera Azure Database for MySQL, który ma być chroniony. W obszarze Ustawienia zabezpieczeń wybierz pozycję **Zaawansowana ochrona przed zagrożeniami (wersja zapoznawcza)**.
3. Na stronie Konfiguracja **zaawansowanej ochrony przed zagrożeniami (wersja zapoznawcza)** :

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W obszarze **Ustawienia zaawansowanej ochrony przed zagrożeniami**w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Konfigurowanie wykrywania zagrożeń":::

## <a name="explore-anomalous-database-activities"></a>Zbadaj nietypowe działania bazy danych

Po wykryciu nietypowych działań bazy danych otrzymasz powiadomienie e-mail. Wiadomość e-mail zawiera informacje dotyczące podejrzanego zdarzenia zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia dla bazy danych.
 
1. Kliknij link **Wyświetl Ostatnie alerty** w wiadomości e-mail, aby uruchomić Azure Portal i wyświetlić stronę Azure Security Center alertów, która zawiera przegląd aktywnych zagrożeń wykrytych w bazie danych SQL.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Konfigurowanie wykrywania zagrożeń":::

    Wyświetl aktywne zagrożenia:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Konfigurowanie wykrywania zagrożeń":::

2. Kliknij konkretny alert, aby uzyskać dodatkowe szczegóły i akcje związane z badaniem tego zagrożenia i korygowaniem przyszłe zagrożenia.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Konfigurowanie wykrywania zagrożeń":::

## <a name="explore-threat-detection-alerts"></a>Eksploruj alerty wykrywania zagrożeń

SQL Database wykrywanie zagrożeń integruje swoje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/). Kafelek wykrywania zagrożeń na żywo w usłudze SQL śledzi stan aktywnych zagrożeń w bazie danych i stronach usługi SQL ATP w Azure Portal.

Kliknij pozycję **alert wykrywania zagrożeń** , aby uruchomić stronę alerty Azure Security Center i zapoznaj się z omówieniem aktywnych zagrożeń SQL wykrytych w bazie danych.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Konfigurowanie wykrywania zagrożeń":::
   

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) .  
