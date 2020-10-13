---
title: Zmiana w miejscu SQL Server wersja
description: Dowiedz się, jak zmienić wersję SQL Server maszyny wirtualnej na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: a57a432a5f0f8e5a6bd802ec08b18350da3a77b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293377"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Zmiana w miejscu dotycząca wersji programu SQL Server na maszynie wirtualnej platformy Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób zmiany wersji Microsoft SQL Server na maszynie wirtualnej z systemem Windows w programie Microsoft Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać uaktualnienie w miejscu SQL Server, mają zastosowanie następujące warunki:

- Wymagany jest nośnik instalacyjny żądanej wersji SQL Server. Klienci, którzy biorą udział w programie [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), mogą uzyskać nośnik instalacyjny z [Centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają programu Software Assurance, mogą korzystać z nośnika instalacyjnego z witryny Azure Marketplace SQL Server obrazu maszyny wirtualnej z nowszą wersją SQL Server (zazwyczaj znajdującą się w C:\SQLServerFull).
- Uaktualnienia wersji powinny być zgodne ze [ścieżkami uaktualnienia obsługi](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15).

## <a name="planning-for-version-change"></a>Planowanie zmiany wersji

Przed wprowadzeniem zmian w wersji zalecamy przejrzenie następujących elementów:

1. Sprawdź, co nowego w wersji planowanej do uaktualnienia:

   - Co nowego w programie [SQL 2019](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-ver15?view=sql-server-ver15)
   - Co nowego w programie [SQL 2017](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2017?view=sql-server-ver15)
   - Co nowego w programie [SQL 2016](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-ver15)
   - Co nowego w programie [SQL 2014](https://docs.microsoft.com/sql/sql-server/what-s-new-in-sql-server-2016?view=sql-server-2014)

1. Zalecamy sprawdzenie [certyfikacji zgodności](https://docs.microsoft.com/sql/database-engine/install-windows/compatibility-certification?view=sql-server-ver15) dla wersji, którą zamierzasz zmienić, aby można było użyć trybów zgodności bazy danych, aby zminimalizować efekt uaktualnienia.
1. Aby sprawdzić pomyślne wyniki, można zapoznać się z następującymi artykułami:

   - [Wideo: Modernizacja SQL Server | Pam Lahoud & Pedro Lopes | 20 lat PRZEBIEGu](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Asystent eksperymentowania z bazą danych do testowania AB](https://docs.microsoft.com/sql/dea/database-experimentation-assistant-overview?view=sql-server-ver15)
   - [Uaktualnianie baz danych przy użyciu Asystenta strojenia zapytania](https://docs.microsoft.com/sql/relational-databases/performance/upgrade-dbcompat-using-qta?view=sql-server-ver15)
   - [Zmiana poziomu zgodności bazy danych i użycie magazynu zapytań](https://docs.microsoft.com/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store?view=sql-server-ver15)

## <a name="upgrade-sql-version"></a>Uaktualnij wersję SQL

> [!WARNING]
> Uaktualnienie wersji SQL Server spowoduje ponowne uruchomienie usługi dla SQL Server poza wszystkimi skojarzonymi usługami, takimi jak usługi Analysis Services i R.

Aby uaktualnić wersję programu SQL Server, uzyskaj nośnik instalacyjny SQL Server dla nowszej wersji, który będzie [obsługiwał ścieżkę uaktualnienia](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15?view=sql-server-ver15) SQL Server i wykonaj następujące czynności:

1. Przed rozpoczęciem procesu należy utworzyć kopię zapasową baz danych, w tym z systemu (z wyjątkiem bazy danych tempdb) i z nich. Można również utworzyć kopię zapasową spójną na poziomie aplikacji przy użyciu usług Azure Backup.
1. Rozpocznij Setup.exe od nośnika instalacyjnego SQL Server.
1. Kreator instalacji uruchamia SQL Server centrum instalacji. Aby uaktualnić istniejące wystąpienie SQL Server, wybierz pozycję **Instalacja** w okienku nawigacji, a następnie wybierz pozycję **Uaktualnij ze starszej wersji SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Wybór do uaktualnienia wersji SQL Server":::

1. Na stronie **klucz produktu** wybierz opcję, aby wskazać, czy przeprowadzasz uaktualnienie do bezpłatnej wersji SQL Server, czy masz klucz PID dla wersji produkcyjnej produktu. Aby uzyskać więcej informacji, zobacz [wersje i obsługiwane funkcje programu SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15) oraz [obsługiwane uaktualnienia wersji i wydania (SQL Server 2016)](https://docs.microsoft.com/sql/database-engine/install-windows/supported-version-and-edition-upgrades?view=sql-server-ver15).
1. Wybierz pozycję **dalej** do momentu uzyskania dostępu **do strony gotowy do uaktualnienia** , a następnie wybierz pozycję **Uaktualnij**. Okno instalatora może przestać odpowiadać przez kilka minut, gdy zmiana zacznie obowiązywać. **Pełna** Strona potwierdzi, że uaktualnienie zostało ukończone. Aby zapoznać się z procedurą krok po kroku dotyczącą uaktualniania, zobacz [kompletną procedurę](https://docs.microsoft.com/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup?view=sql-server-ver15#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Wybór do uaktualnienia wersji SQL Server":::

Jeśli została zmieniona wersja SQL Server oprócz zmiany wersji, należy również zaktualizować wersję i zapoznać się z sekcją **Sprawdź wersję i wydanie w portalu** , aby zmienić wystąpienie maszyny wirtualnej SQL.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Wybór do uaktualnienia wersji SQL Server":::

## <a name="downgrade-the-version-of-sql-server"></a>Obniżenie wersji SQL Server

Aby obniżyć wersję SQL Server, należy całkowicie odinstalować SQL Server i ponownie ją zainstalować przy użyciu odpowiedniej wersji. Jest to podobne do nowej instalacji SQL Server, ponieważ nie będzie można przywrócić starszej bazy danych z nowszej wersji do nowo zainstalowanej starszej wersji. Bazy danych należy utworzyć od podstaw. Jeśli podczas uaktualniania zmieniono również wersję SQL Server, Zmień właściwość **Edition** maszyny wirtualnej SQL Server na Azure Portal na nową wartość. Spowoduje to zaktualizowanie metadanych i rozliczeń skojarzonych z tą maszyną wirtualną.

> [!WARNING]
> Nie jest obsługiwane obniżenie wersji SQL Server w miejscu.

Wersję SQL Server można obniżyć, wykonując następujące czynności:

1. Upewnij się, że nie używasz żadnej funkcji, która jest [dostępna tylko w nowszej wersji](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Utwórz kopię zapasową wszystkich baz danych, w tym system (z wyjątkiem tempdb) i bazy danych użytkowników.
1. Wyeksportuj wszystkie niezbędne obiekty na poziomie serwera (takie jak wyzwalacze serwera, role, logowania, połączone serwery, zadania, poświadczenia i certyfikaty).
1. Jeśli nie masz skryptów do ponownego tworzenia baz danych użytkownika w starszej wersji, musisz utworzyć skrypt dla wszystkich obiektów i wyeksportować wszystkie dane przy użyciu BCP.exe, SSIS lub DACPAC.

   Upewnij się, że wybrano prawidłowe opcje podczas skryptowania takich elementów jako wersji docelowej, obiektów zależnych i opcji zaawansowanych.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Wybór do uaktualnienia wersji SQL Server":::

1. Całkowicie Odinstaluj SQL Server i wszystkie skojarzone usługi.
1. Uruchom ponownie maszynę wirtualną.
1. Zainstaluj SQL Server przy użyciu nośnika dla odpowiedniej wersji programu.
1. Zainstaluj najnowsze dodatki Service Pack i aktualizacje zbiorcze.
1. Zaimportuj wszystkie wymagane obiekty na poziomie serwera (które zostały wyeksportowane w kroku 3).
1. Utwórz ponownie wszystkie wymagane bazy danych użytkownika od podstaw (przy użyciu utworzonych skryptów lub plików z kroku 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Weryfikowanie wersji i wydania w portalu

Po zmianie wersji SQL Server należy ponownie zarejestrować maszynę wirtualną SQL Server z [dostawcą zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md) , aby można było używać Azure Portal do wyświetlania wersji SQL Server. Wymieniony numer wersji powinien teraz odzwierciedlać nowo uaktualnioną wersję i edycję instalacji SQL Server.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Wybór do uaktualnienia wersji SQL Server":::

> [!NOTE]
> Jeśli zarejestrowano już dostawcę zasobów maszyny wirtualnej SQL, [Wyrejestruj z jednostki uzależnionej](sql-vm-resource-provider-register.md#unregister-from-rp) , a następnie ponownie [zarejestruj zasób maszyny wirtualnej SQL](sql-vm-resource-provider-register.md#register-with-rp) , aby wykryje poprawną wersję i wydanie SQL Server zainstalowanej na maszynie wirtualnej. Spowoduje to zaktualizowanie informacji o metadanych i rozliczeniach skojarzonych z tą maszyną wirtualną.

## <a name="remarks"></a>Uwagi

- Zaleca się zainicjowanie wykonywania kopii zapasowych/aktualizacji statystyk/ponowne kompilowanie indeksów/sprawdzanie spójności po zakończeniu uaktualniania. Możesz również sprawdzić poziomy zgodności poszczególnych baz danych, aby upewnić się, że odzwierciedlają one żądany poziom.
- Po zaktualizowaniu SQL Server na maszynie wirtualnej upewnij się, że właściwość **wersja** SQL Server w Azure Portal jest zgodna z numerem zainstalowaną dla rozliczeń.
- Możliwość [zmiany wersji](change-sql-server-edition.md#change-edition-in-portal) to funkcja dostawcy zasobów maszyny wirtualnej SQL. Wdrożenie obrazu portalu Azure Marketplace za pomocą Azure Portal powoduje automatyczne zarejestrowanie SQL Serverj maszyny wirtualnej przy użyciu dostawcy zasobów. Jednak klienci, którzy samodzielnie instalują SQL Server, będą musieli ręcznie [zarejestrować swoją SQL Serverą maszynę wirtualną](sql-vm-resource-provider-register.md).
- W przypadku porzucenia SQL Server zasobów maszyny wirtualnej zostanie przywrócony zakodowane ustawienie wersji obrazu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)
- [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
- [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](doc-changes-updates-release-notes.md)
