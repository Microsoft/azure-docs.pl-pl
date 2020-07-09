---
title: Zarządzanie regułami zapory — funkcja Citus) — Azure Database for PostgreSQL
description: Tworzenie reguł zapory dla Azure Database for PostgreSQL-i zarządzanie nimi przy użyciu Azure Portal
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/12/2019
ms.openlocfilehash: c84616e8a9b9ff9722f5a104175c80c37dbcbcc3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116917"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Zarządzanie regułami zapory dla Azure Database for PostgreSQL-ze skalowaniem (Citus)
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do węzła koordynatora ze skalowaniem (Citus) z określonego adresu IP lub zakresu adresów IP.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- Grupa serwerów [tworzy grupę serwerów Azure Database for PostgreSQL — Citus](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

> [!NOTE]
> Te ustawienia są również dostępne podczas tworzenia grupy serwerów Azure Database for PostgreSQL-Citus. Na karcie **Sieć** kliknij pozycję **publiczny punkt końcowy**.
> ![Azure Portal — karta sieciowa](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Na stronie Grupa serwerów PostgreSQL w obszarze nagłówek zabezpieczeń kliknij pozycję **Sieć** , aby otworzyć reguły zapory.

   ![Azure Portal — kliknij pozycję Sieć](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij pozycję **Dodaj adres IP klienta**, na pasku narzędzi (opcja A) lub w łączu (opcja B). W obu przypadkach program automatycznie tworzy regułę zapory z publicznym adresem IP komputera, jak zostało to postrzegane przez system Azure.

   ![Azure Portal kliknij pozycję Dodaj adres IP klienta](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Alternatywnie, kliknięcie **+ Dodaj 0.0.0.0-255.255.255.255** (z prawej strony opcji B) umożliwia nie tylko adres IP, ale cały Internet umożliwia dostęp do portu 5432 węzła koordynatora. W takiej sytuacji klienci nadal muszą zalogować się przy użyciu prawidłowej nazwy użytkownika i hasła w celu korzystania z klastra. Niemniej jednak zalecamy zezwolenie na dostęp na całym świecie tylko dla krótkich okresów i tylko dla baz danych nieprodukcyjnych.

3. Sprawdź swój adres IP przed zapisaniem konfiguracji. W niektórych sytuacjach adres IP zaobserwowany przez Azure Portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może zajść potrzeba zmiany początkowego adresu IP i końcowego adresu IP, aby zapewnić działanie reguły zgodnie z oczekiwaniami.
   Użyj wyszukiwarki lub innego narzędzia online do sprawdzenia własnego adresu IP. Na przykład wyszukaj ciąg "co to jest mój adres IP".

   ![Wyszukiwanie w usłudze Bing — mój adres IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Dodaj dodatkowe zakresy adresów. W regułach zapory można określić pojedynczy adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do pojedynczego adresu IP, wpisz ten sam adres w polu dla początkowy adres IP i końcowy adres IP. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacjom dostęp do węzła koordynatora na porcie 5432.

5. Kliknij przycisk **Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończyła się pomyślnie.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure

Istnieje prosty sposób udzielania dostępu do bazy danych w ramach aplikacji hostowanej na platformie Azure (np. aplikacji platformy Azure Web Apps lub działających na maszynie wirtualnej platformy Azure). Po prostu ustaw opcję **Zezwól usługom i zasobom platformy Azure na dostęp do tej grupy serwerów** **w portalu z okienka** **Sieć** i kliknij przycisk **Zapisz**.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu witryny Azure Portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk i **Dodaj adres IP klienta**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać kolejne adresy IP, wpisz nazwę reguły, początkowy adres IP i końcowy adres IP. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule i wprowadź zmiany. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **Usuń** , aby usunąć regułę. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [koncepcji reguł zapory](concepts-hyperscale-firewall-rules.md), w tym o sposobach rozwiązywania problemów z połączeniami.
