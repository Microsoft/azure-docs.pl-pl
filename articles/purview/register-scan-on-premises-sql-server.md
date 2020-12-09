---
title: Rejestrowanie i skanowanie lokalnego programu SQL Server
description: W tym samouczku opisano sposób skanowania Premium programu SQL Server przy użyciu samodzielnego środowiska IR.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 9003366ec0d64057ca7426d5b6b99986bc21fc9d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920290"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Rejestrowanie i skanowanie lokalnego programu SQL Server

W tym artykule opisano sposób rejestrowania źródła danych programu SQL Server w programie kontrolą i konfigurowania na nim skanowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Lokalne źródło danych programu SQL Server obsługuje następujące funkcje:

- **Pełne i przyrostowe skanowania** do przechwytywania metadanych i klasyfikacji w sieci lokalnej lub w programie SQL Server zainstalowanym na maszynie wirtualnej platformy Azure.

- Elementy **powiązane między zasobami** danych dla działań Copy/przepływu danych usługi ADF

Lokalne źródło danych programu SQL Server obsługuje:

- Każda wersja programu SQL z programu SQL Server 2019 z powrotem do programu SQL Server 2000

- Metoda uwierzytelniania: uwierzytelnianie SQL

### <a name="known-limitations"></a>Znane ograniczenia

Usługa Azure kontrolą nie obsługuje skanowania [widoków](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) w SQL Server. 

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).

- Skonfiguruj [własne środowisko Integration Runtime](manage-integration-runtimes.md) , aby skanować źródło danych.

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Istnieje tylko jeden sposób na skonfigurowanie uwierzytelniania dla lokalnego programu SQL Server:

- Uwierzytelnianie SQL

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

Tożsamość SQL musi mieć dostęp do podstawowej bazy danych. Lokalizacja jest `sys.databases` przechowywana. `sys.databases`Aby można było znaleźć wszystkie wystąpienia bazy danych SQL na serwerze, skaner kontrolą musi zostać wyliczony.

#### <a name="using-an-existing-server-administrator"></a>Korzystanie z istniejącego administratora serwera

Jeśli planujesz użyć istniejącego użytkownika administratora serwera (SA) do skanowania lokalnego programu SQL Server, upewnij się, że:

1. `sa` nie jest typem uwierzytelniania systemu Windows.

2. Użytkownik na poziomie serwera, który ma być używany, musi mieć role serwera Public i sysadmin. Można to sprawdzić, przechodząc do SQL Server Management Studio (SSMS), nawiązując połączenie z serwerem, przechodząc do zabezpieczeń, wybierając nazwę logowania, którego planujesz użyć, klikając prawym przyciskiem myszy pozycję **Właściwości** , a następnie wybierając pozycję **role serwera**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Identyfikator logowania na poziomie serwera.":::

3. Bazy danych są mapowane do użytkownika, który ma co najmniej db_datareader dostęp na poziomie każdej bazy danych.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="Mapowanie użytkownika dla skojarzenia zabezpieczeń.":::

#### <a name="creating-a-new-login-and-user"></a>Tworzenie nowego identyfikatora logowania i użytkownika

Jeśli chcesz utworzyć nową nazwę logowania i użytkownika, aby umożliwić skanowanie programu SQL Server, wykonaj poniższe kroki:

1. Przejdź do SQL Server Management Studio (SSMS), Połącz się z serwerem, przejdź do zabezpieczenia, kliknij prawym przyciskiem myszy pozycję Logowanie i Utwórz nową nazwę logowania. Upewnij się, że wybrano opcję uwierzytelnianie SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Utwórz nową nazwę logowania i użytkownika.":::

2. Wybierz pozycję Role serwera po lewej stronie nawigacyjnej i wybierz opcję publiczny i administrator systemu.

3. Wybierz pozycję Mapowanie użytkowników po lewej stronie nawigacyjnej i wybierz wszystkie bazy danych na mapie.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="Mapowanie użytkownika.":::

4. Kliknij przycisk OK, aby zapisać.

5. Przejdź ponownie do utworzonego użytkownika, klikając prawym przyciskiem myszy i wybierając pozycję **Właściwości**. Wprowadź nowe hasło i potwierdź je. Wybierz pozycję "Określ stare hasło" i wprowadź stare hasło. **Konieczna jest zmiana hasła zaraz po utworzeniu nowej nazwy logowania.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="Zmień hasło.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Przechowywanie hasła logowania SQL w magazynie kluczy i Tworzenie poświadczeń w usłudze kontrolą

1. Przejdź do magazynu kluczy w Azure Portal
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *hasło* z identyfikatora logowania programu SQL Server
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu **nazwy użytkownika** i **hasła** w celu skonfigurowania skanowania

## <a name="register-a-sql-server-data-source"></a>Rejestrowanie źródła danych programu SQL Server

1. Przejdź do konta kontrolą

1. W obszarze źródła i skanowanie w lewym obszarze nawigacji wybierz pozycję **Integration Runtimes**. Upewnij się, że jest skonfigurowany własny środowisko Integration Runtime. Jeśli nie została skonfigurowana, wykonaj kroki opisane w [tym miejscu](manage-integration-runtimes.md) , aby utworzyć własne środowisko Integration Runtime do skanowania na maszynę wirtualną lub maszynie wirtualnej platformy Azure, która ma dostęp do sieci lokalnej.

1. Wybierz **źródła** na lewym pasku nawigacyjnym

1. Wybierz pozycję **zarejestruj**

1. Wybierz pozycję **SQL Server** , a następnie **Kontynuuj** .

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Skonfiguruj źródło danych SQL.":::

5. Podaj przyjazną nazwę i punkt końcowy serwera, a następnie wybierz pozycję **Zakończ** , aby zarejestrować źródło danych. Jeśli na przykład nazwa FQDN programu SQL Server to **Foobar.Database.Windows.NET**, wprowadź *Foobar* jako punkt końcowy serwera.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
