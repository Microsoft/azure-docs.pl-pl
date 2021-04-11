---
title: Odnajdywanie spisu oprogramowania na serwerach lokalnych przy użyciu Azure Migrate
description: Informacje na temat odnajdywania spisu oprogramowania na serwerach lokalnych z Azure Migrate odnajdywania i oceny.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: d68c3729e8a63f8342cd51b62413aec3276c6165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871014"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>Odkrywanie zainstalowanego spisu oprogramowania i SQL Server wystąpień i baz danych

W tym artykule opisano sposób odnajdywania spisu oprogramowania zainstalowanego oraz SQL Server wystąpień i baz danych na serwerach działających w środowisku VMware przy użyciu Azure Migrate: narzędzia odnajdywania i oceny.

Wykonanie spisu oprogramowania ułatwia identyfikację i dostosowanie ścieżki migracji do platformy Azure dla obciążeń. Spis oprogramowania korzysta z urządzenia Azure Migrate, aby przeprowadzić odnajdywanie przy użyciu poświadczeń serwera. Jest całkowicie niezależny od agenta — na serwerach nie są zainstalowane żadne agenci, aby zebrać te dane.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono projekt](./create-manage-projects.md) z Azure Migrate: narzędzia odnajdywania i oceny dodane do niego.
- Zapoznaj się z [wymaganiami programu VMware](migrate-support-matrix-vmware.md#vmware-requirements) w celu przeprowadzenia spisu oprogramowania.
- Przejrzyj [wymagania dotyczące urządzenia](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) przed skonfigurowaniem urządzenia.
- Przejrzyj [wymagania dotyczące odnajdywania aplikacji](migrate-support-matrix-vmware.md#application-discovery-requirements) przed zainicjowaniem spisu oprogramowania na serwerach.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Wdróż i Skonfiguruj urządzenie Azure Migrate

1. [Zapoznaj](migrate-appliance.md#appliance---vmware) się z wymaganiami dotyczącymi wdrażania urządzenia Azure Migrate.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać [](migrate-appliance.md#public-cloud-urls) dostęp w [chmurach publicznych i rządowych](migrate-appliance.md#government-cloud-urls).
3. [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-vmware.md#port-access-requirements) na wymagania dotyczące dostępu do portów dla urządzenia.
5. [Wdróż urządzenie Azure Migrate,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdywanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon komórki jajowe do programu VMware w celu utworzenia serwera działającego w vCenter Server. Po wdrożeniu urządzenia należy zarejestrować je w projekcie i skonfigurować do inicjowania odnajdywania.
6. Podczas konfigurowania urządzenia należy określić następujące elementy w Menedżerze konfiguracji urządzenia:
    - Szczegóły vCenter Server, z którym chcesz nawiązać połączenie.
    - vCenter Server poświadczenia w zakresie odnajdywania serwerów w środowisku programu VMware.
    - Poświadczenia serwera, które mogą być poświadczeniami domeny/systemu Windows (niebędącymi domeną)/Linux (nienależących do domeny). [Dowiedz się więcej](add-server-credentials.md) na temat podania poświadczeń i sposobu ich obsługi.

## <a name="verify-permissions"></a>Weryfikowanie uprawnień

- Należy [utworzyć vCenter Server konto tylko do odczytu](./tutorial-discover-vmware.md#prepare-vmware) na potrzeby odnajdywania i oceny. Konto tylko do odczytu wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**, aby umożliwić współdziałanie z serwerami w celu wykonywania spisu oprogramowania.
- Można dodać wiele poświadczeń domen i domen nienależących do domeny (Windows/Linux) do programu Configuration Manager na potrzeby odnajdywania aplikacji. Musisz mieć konto użytkownika-gościa dla serwerów z systemem Windows, a normalne/normalne konto użytkownika (dostęp sudo) dla wszystkich serwerów z systemem Linux. [Dowiedz się więcej](add-server-credentials.md) na temat podania poświadczeń i sposobu ich obsługi.

### <a name="add-credentials-and-initiate-discovery"></a>Dodawanie poświadczeń i Inicjowanie odnajdywania

1. Otwórz Menedżera konfiguracji urządzenia, Ukończ sprawdzanie wymagań wstępnych i rejestrację urządzenia.
2. Przejdź do panelu **Zarządzanie poświadczeniami i źródłami odnajdywania** .
1.  W **kroku 1: podaj poświadczenia vCenter Server**, kliknij pozycję **Dodaj poświadczenia** , aby podać poświadczenia dla konta vCenter Server, które będzie używane przez urządzenie do odnajdywania serwerów uruchomionych na vCenter Server.
1. W **kroku 2: podaj vCenter Server Szczegóły**, kliknij pozycję **Dodaj źródło odnajdywania** , aby wybrać przyjazną nazwę dla poświadczeń z listy rozwijanej, określ **adres IP/nazwę FQDN** panelu wystąpienia vCenter Server :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 w Menedżerze konfiguracji urządzenia dla vCenter Server Szczegóły":::
1. W **kroku 3: podawanie poświadczeń serwera do wykonywania spisu oprogramowania, analizy zależności bez agenta oraz odnajdywania wystąpień SQL Server i baz danych**, kliknij przycisk **Dodaj poświadczenia** , aby podać wiele poświadczeń serwera w celu zainicjowania spisu oprogramowania.
1. Kliknij pozycję **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie vCenter Server.

 Po ukończeniu odnajdywania vCenter Server urządzenie inicjuje odnajdywanie zainstalowanych aplikacji, ról i funkcji (Spis oprogramowania). Czas trwania zależy od liczby odnalezionych serwerów. W przypadku serwerów z 500 na wykrytym magazynie w portalu Azure Migrate trwa około godziny.

## <a name="review-and-export-the-inventory"></a>Przeglądanie i eksportowanie spisu

Po zakończeniu spisu oprogramowania można przejrzeć i wyeksportować spis w Azure Portal.

1. W **Azure Migrate — Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i Ocena**, kliknij wyświetlaną liczbę, aby otworzyć stronę **odnalezione serwery** .

    > [!NOTE]
    > Na tym etapie można także włączyć analizę zależności dla odnalezionych serwerów, aby można było wizualizować zależności między serwerami, które chcesz ocenić. [Dowiedz się więcej](concepts-dependency-visualization.md) o analizie zależności.

2. W kolumnie **Spis oprogramowania** kliknij liczbę wyświetlaną, aby przejrzeć odnalezione aplikacje, role i funkcje.
4. Aby wyeksportować spis, w **odnalezionych serwerach** kliknij pozycję **Eksportuj spis aplikacji**.

Spis oprogramowania jest eksportowany i pobierany w formacie programu Excel. Arkusz **spisu oprogramowania** zawiera wszystkie aplikacje odnalezione na wszystkich serwerach.

## <a name="discover-sql-server-instances-and-databases"></a>Odnajdź SQL Server wystąpienia i bazy danych

- Spis oprogramowania identyfikuje również wystąpienia SQL Server uruchomione w środowisku programu VMware.
- Jeśli nie podano uwierzytelniania systemu Windows lub SQL Server poświadczeń uwierzytelniania w Menedżerze konfiguracji urządzeń, należy dodać poświadczenia, aby urządzenie mogło używać ich do nawiązywania połączeń z odpowiednimi wystąpieniami SQL Server.

Po nawiązaniu połączenia urządzenie zbiera dane dotyczące konfiguracji i wydajności SQL Server wystąpień i baz danych. Dane konfiguracji SQL Server są aktualizowane co 24 godziny, a dane wydajności są przechwytywane co 30 sekund. W związku z tym każda zmiana właściwości wystąpienia SQL Server i baz danych, takich jak stan bazy danych, poziom zgodności itp., może zająć do 24 godzin.

## <a name="next-steps"></a>Następne kroki

- [Utwórz ocenę](how-to-create-assessment.md) dla odnalezionych serwerów.
- [Ocenianie serwerów SQL](./tutorial-assess-sql.md) na potrzeby migracji do usługi Azure SQL.
