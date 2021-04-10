---
title: Rejestrowanie i skanowanie Azure SQL Database wystąpienia zarządzanego
description: W tym samouczku opisano sposób skanowania Azure SQL Database wystąpienia zarządzanego
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a30980ba61a1dfec918dce1a55e78f1be2a36dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677875"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Rejestrowanie i skanowanie Azure SQL Database wystąpienia zarządzanego

W tym artykule opisano sposób rejestrowania źródła danych wystąpienia zarządzanego Azure SQL Database w programie kontrolą i skonfigurowania na nim skanowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Źródło danych wystąpienia zarządzanego Azure SQL Database obsługuje następujące funkcje:

- **Pełne i przyrostowe skanowania** do przechwytywania metadanych i klasyfikacji w Azure SQL Database wystąpieniu zarządzanym.

-  Elementy powiązane z zasobami danych dla operacji kopiowania i przepływu danych w usłudze ADF.

### <a name="known-limitations"></a>Znane ograniczenia

Usługa Azure kontrolą nie obsługuje skanowania [widoków](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) w wystąpieniu zarządzanym usługi Azure SQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz nowe konto kontrolą, jeśli jeszcze go nie masz.

- [Skonfiguruj publiczny punkt końcowy w wystąpieniu zarządzanym Azure SQL](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > Organizacja musi mieć możliwość zezwalania na publiczny punkt końcowy, ponieważ **prywatny punkt końcowy nie jest jeszcze obsługiwany** przez kontrolą. W przypadku korzystania z prywatnego punktu końcowego skanowanie nie powiedzie się.

### <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Uwierzytelnianie do skanowania Azure SQL Database wystąpieniem zarządzanym. Jeśli musisz utworzyć nowe uwierzytelnianie, musisz [autoryzować dostęp do bazy danych do SQL Database wystąpienia zarządzanego](../azure-sql/database/logins-create-manage.md). Istnieją trzy metody uwierzytelniania, które kontrolą obecnie obsługuje:

- Uwierzytelnianie SQL
- Jednostka usługi
- Tożsamość zarządzana

#### <a name="sql-authentication"></a>Uwierzytelnianie SQL

> [!Note]
> Nowe nazwy logowania można tworzyć tylko w przypadku głównego konta serwera (utworzonego przez proces aprowizacji) lub członkiem `loginmanager` roli bazy danych w bazie danych Master. Po udzieleniu uprawnień trwa około **15 minut** , konto kontrolą powinno mieć odpowiednie uprawnienia do skanowania zasobów.

Możesz postępować zgodnie z instrukcjami w temacie [Tworzenie nazwy logowania](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) , aby utworzyć nazwę logowania dla Azure SQL Database wystąpienia zarządzanego, jeśli nie jest to możliwe. Do następnych kroków będzie potrzebna **Nazwa użytkownika** i **hasło** .

1. Przejdź do magazynu kluczy w Azure Portal
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *hasło* z wystąpienia zarządzanego Azure SQL Database
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu **nazwy użytkownika** i **hasła** w celu skonfigurowania skanowania

#### <a name="service-principal-and-managed-identity"></a>Nazwa główna usługi i zarządzana tożsamość

Istnieje kilka kroków, aby zezwolić kontrolą na użycie jednostki usługi do skanowania wystąpienia zarządzanego Azure SQL Database

##### <a name="create-or-use-an-existing-service-principal"></a>Utwórz lub Użyj istniejącej nazwy głównej usługi

> [!Note]
> Pomiń ten krok, jeśli używasz **tożsamości zarządzanej**

Aby użyć nazwy głównej usługi, można użyć istniejącej lub utworzyć nową. 

> [!Note]
> Jeśli musisz utworzyć nową nazwę główną usługi, wykonaj następujące kroki:
> 1. Przejdź do [Azure Portal](https://portal.azure.com).
> 1. Wybierz **Azure Active Directory** z menu po lewej stronie.
> 1. Wybierz pozycję **Rejestracje aplikacji**.
> 1. Wybierz pozycję **+ Nowa rejestracja aplikacji**.
> 1. Wprowadź nazwę **aplikacji** (nazwę główną usługi).
> 1. Wybierz **konta tylko w tym katalogu organizacji**.
> 1. W obszarze Identyfikator URI przekierowania wybierz pozycję **Sieć Web** i wprowadź dowolny żądany adres URL. nie musi być w rzeczywistości ani działać.
> 1. Następnie wybierz pozycję **Zarejestruj**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurowanie uwierzytelniania usługi Azure AD na koncie bazy danych

Nazwa główna usługi lub zarządzana tożsamość musi mieć uprawnienia do pobierania metadanych dla bazy danych, schematów i tabel. Ponadto musi być w stanie zbadać tabele, aby uzyskać przykład klasyfikacji.
- [Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą usługi Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Utwórz użytkownika usługi Azure AD w Azure SQL Database wystąpieniu zarządzanym, postępując zgodnie z wymaganiami wstępnymi i samouczkiem dotyczącym [tworzenia zawartych użytkowników mapowanych na tożsamości usługi Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)
- Przypisanie `db_owner` (**zalecane**) uprawnienia do tożsamości

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Dodaj nazwę główną usługi do magazynu kluczy i poświadczenia kontrolą

> [!Note]
> Jeśli planujesz użycie **tożsamości zarządzanej**, możesz pominąć ten krok, ponieważ domyślna tożsamość kontrolą jest już w **kontrolą-MSI**

Wymagane jest uzyskanie identyfikatora aplikacji i klucza tajnego jednostki usługi:

1. Przejdź do nazwy głównej usługi w [Azure Portal](https://portal.azure.com)
1. Skopiuj wartości **Identyfikator aplikacji (klienta)** z **omówienia** i **klucz tajny klienta** z **certyfikatów & wpisy tajne**.
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź wybraną **nazwę** oraz **wartość** jako **klucz tajny klienta** z nazwy głównej usługi
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu nazwy głównej usługi, aby skonfigurować skanowanie

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Rejestrowanie źródła danych wystąpienia zarządzanego Azure SQL Database

1. Przejdź do konta kontrolą

1. Wybierz **źródła** na lewym pasku nawigacyjnym

1. Wybierz pozycję **zarejestruj**

1. Wybierz **Azure SQL Database wystąpienie zarządzane** , a następnie **Kontynuuj**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Konfigurowanie źródła danych SQL":::

1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję w polu listy rozwijanej **subskrypcja platformy Azure** i odpowiedni serwer z listy rozwijanej **Nazwa serwera** .

1. Podaj publiczny, w **pełni kwalifikowaną nazwę domeny** i **numer portu**. Następnie wybierz pozycję **Zakończ** , aby zarejestrować źródło danych.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Dodawanie Azure SQL Database wystąpienia zarządzanego":::

    Na przykład `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Usunięcie skanowania nie powoduje usunięcia zasobów z wcześniejszych Azure SQL Database skanów wystąpienia zarządzanego.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)