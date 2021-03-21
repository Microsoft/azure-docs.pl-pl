---
title: Zaawansowana ochrona przed zagrożeniami — Azure Portal-Azure Database for MariaDB
description: Ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665110"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB

Zaawansowana ochrona przed zagrożeniami dla Azure Database for MariaDB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Zaawansowana ochrona przed zagrożeniami jest częścią zaawansowanej oferty zabezpieczeń danych, która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Zaawansowana ochrona przed zagrożeniami jest w publicznej wersji zapoznawczej. Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w których wdrożono Azure Database for MariaDB dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami **nie** jest dostępna w następujących regionach na platformie Azure dla instytucji rządowych i w ramach suwerennej chmury: US Gov Teksas, US Gov Arizona, US gov Iowa, US, gov wirginia, US DoD (region wschodni), US dod (region środkowy), Niemcy środkowe, Niemcy północne, Chiny Wschodnie, Chiny Wschodnie 2. Odwiedź stronę [dostępne produkty według regionów,](https://azure.microsoft.com/global-infrastructure/services/) Aby uzyskać ogólną dostępność produktu.

## <a name="set-up-threat-detection"></a>Konfigurowanie wykrywania zagrożeń
1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .
2. Przejdź do strony konfiguracji serwera Azure Database for MariaDB, który ma być chroniony. W obszarze Ustawienia zabezpieczeń wybierz pozycję **Zaawansowana ochrona przed zagrożeniami (wersja zapoznawcza)**.
3. Na stronie Konfiguracja **zaawansowanej ochrony przed zagrożeniami (wersja zapoznawcza)** :

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W obszarze **Ustawienia zaawansowanej ochrony przed zagrożeniami** w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Zbadaj nietypowe działania bazy danych

Po wykryciu nietypowych działań bazy danych otrzymasz powiadomienie e-mail. Wiadomość e-mail zawiera informacje dotyczące podejrzanego zdarzenia zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia dla bazy danych.
 
1. Kliknij link **Wyświetl Ostatnie alerty** w wiadomości e-mail, aby uruchomić Azure Portal i wyświetlić stronę Azure Security Center alertów, która zawiera przegląd aktywnych zagrożeń wykrytych w bazie danych SQL.
    
    ![Raport o nietypowych działaniach](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Wyświetl aktywne zagrożenia:

    ![Aktywne zagrożenia](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknij konkretny alert, aby uzyskać dodatkowe szczegóły i akcje związane z badaniem tego zagrożenia i korygowaniem przyszłe zagrożenia.
    
    ![Konkretny alert](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Eksploruj alerty wykrywania zagrożeń

SQL Database wykrywanie zagrożeń integruje swoje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/). Na żywo kafelki wykrywania zagrożeń SQL w bazie danych i w programie SQL ATP w Azure Portal śledzenia stanu aktywnych zagrożeń.

Kliknij pozycję **alert wykrywania zagrożeń** , aby uruchomić stronę alerty Azure Security Center i zapoznaj się z omówieniem aktywnych zagrożeń SQL wykrytych w bazie danych.

   ![Alert wykrywania zagrożeń](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure Security Center](../security-center/security-center-introduction.md)
* Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) .