---
title: Jak skanować dedykowane pule SQL
description: W tym przewodniku opisano szczegóły dotyczące skanowania dedykowanych pul SQL.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 7d6a0b04306c2ed6ae4887c79962cbb5528643fc
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106976"
---
# <a name="register-and-scan-dedicated-sql-pools-formerly-sql-dw"></a>Rejestrowanie i skanowanie dedykowanych pul SQL (dawniej SQL DW)

> [!NOTE]
> Jeśli chcesz zarejestrować i przeskanować dedykowaną bazę danych SQL w obszarze roboczym Synapse, musisz postępować zgodnie z instrukcjami w [tym miejscu](register-scan-synapse-workspace.md).

W tym artykule omówiono sposób rejestrowania i skanowania wystąpienia dedykowanej puli SQL (dawniej SQL DW) w programie kontrolą.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Usługa Azure Synapse Analytics (wcześniej SQL DW) obsługuje pełne i przyrostowe skanowania w celu przechwytywania metadanych i schematu. Skanowanie umożliwia również automatyczne klasyfikowanie danych w oparciu o system i niestandardowe reguły klasyfikacji.

### <a name="known-limitations"></a>Znane ograniczenia

> * Usługa Azure kontrolą nie obsługuje skanowania [widoków](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) w usłudze Azure Synapse Analytics.
> * Usługa Azure kontrolą nie obsługuje ponad 300 kolumn na karcie schemat i pokazuje "dodatkowe kolumny, które są obcinane". 

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą
- Dostęp sieciowy między kontem kontrolą i analizą usługi Azure Synapse.
 
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Istnieją trzy sposoby konfigurowania uwierzytelniania dla usługi Azure Synapse Analytics:

- Tożsamość zarządzana
- Uwierzytelnianie SQL
- Jednostka usługi

    > [!Note]
    > Nowe nazwy logowania można tworzyć tylko w przypadku głównego konta serwera (utworzonego przez proces aprowizacji) lub członkiem `loginmanager` roli bazy danych w bazie danych Master. Po udzieleniu uprawnień trwa około 15 minut, konto kontrolą powinno mieć odpowiednie uprawnienia do skanowania zasobów.

### <a name="managed-identity-recommended"></a>Zarządzana tożsamość (zalecana) 
   
Twoje konto kontrolą ma własną tożsamość zarządzaną, która stanowi zasadniczo swoją nazwę kontrolą podczas jego tworzenia. Musisz utworzyć użytkownika usługi Azure AD w usłudze Azure Synapse Analytics (dawniej SQL DW) o nazwie tożsamości zarządzanej kontrolą, postępując zgodnie z wymaganiami wstępnymi i samouczkiem dotyczącym [tworzenia użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Przykładowa składnia SQL służąca do tworzenia uprawnień użytkownika i udzielania:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

Uwierzytelnianie musi mieć uprawnienia do pobierania metadanych dla bazy danych, schematów i tabel. Ponadto musi być w stanie zbadać tabele, aby uzyskać przykład klasyfikacji. Zalecenie polega na przypisaniu `db_owner` uprawnień do tożsamości.

### <a name="service-principal"></a>Jednostka usługi

Aby użyć uwierzytelniania jednostki usługi do skanowania, można użyć istniejącego lub utworzyć nowy. 

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

Wymagane jest uzyskanie identyfikatora aplikacji i klucza tajnego jednostki usługi:

1. Przejdź do nazwy głównej usługi w [Azure Portal](https://portal.azure.com)
1. Skopiuj wartości **Identyfikator aplikacji (klienta)** z **omówienia** i **klucz tajny klienta** z **certyfikatów & wpisy tajne**.
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź wybraną **nazwę** oraz **wartość** jako **klucz tajny klienta** z nazwy głównej usługi
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu nazwy głównej usługi, aby skonfigurować skanowanie 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Udzielanie dostępu do jednostki usługi w usłudze Azure Synapse Analytics (dawniej: SQL DW)

Ponadto należy również utworzyć użytkownika usługi Azure AD w usłudze Azure Synapse Analytics, postępując zgodnie z wymaganiami wstępnymi i samouczkiem dotyczącym [tworzenia użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Przykładowa składnia SQL służąca do tworzenia uprawnień użytkownika i udzielania:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_owner ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Aby można było skanować, kontrolą będzie potrzebować **identyfikatora aplikacji** i **klucza tajnego klienta** .

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

Aby utworzyć identyfikator logowania dla usługi Azure Synapse Analytics (dawniej SQL DW), możesz postępować zgodnie z instrukcjami w temacie [Tworzenie logowania](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) .

Gdy wybrana metoda uwierzytelniania to **uwierzytelnianie SQL**, należy uzyskać hasło i magazyn w magazynie kluczy:

1. Pobieranie hasła dla logowania SQL
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *hasło* dla swojego identyfikatora logowania SQL
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu klucza w celu skonfigurowania skanowania

## <a name="register-a-sql-dedicated-pool-formerly-sql-dw"></a>Rejestrowanie dedykowanej puli SQL (dawniej SQL DW)

Aby zarejestrować nowy serwer usługi Azure Synapse Analytics w Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
1. Wybierz **źródła** na lewym pasku nawigacyjnym
1. Wybierz pozycję **zarejestruj**
1. W obszarze **Rejestruj źródła** wybierz opcję **dedykowana Pula SQL (wcześniej SQL DW)**
1. Wybierz przycisk **Kontynuuj**

Na ekranie **Rejestr sources (Azure Synapse Analytics)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Wybierz, w jaki sposób chcesz wskazać żądany SQL Server logiczny:
   1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję z listy rozwijanej **subskrypcja platformy Azure** i odpowiedni serwer w polu listy rozwijanej **Nazwa serwera** .
   1. Możesz też wybrać opcję **wprowadź ręcznie** i wprowadzić **nazwę serwera**.
1. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
