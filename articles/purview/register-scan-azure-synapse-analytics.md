---
title: Jak skanować usługę Azure Synapse Analytics
description: W tym przewodniku opisano szczegóły dotyczące sposobu skanowania usługi Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: a0b0dc8c29bcdb51f7b348dd62e3d27796819a7d
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554571"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Rejestrowanie i skanowanie usługi Azure Synapse Analytics

W tym artykule omówiono sposób rejestrowania i skanowania wystąpienia usługi Azure Synapse Analytics (dawniej SQL DW) w kontrolą.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Usługa Azure Synapse Analytics (wcześniej SQL DW) obsługuje pełne i przyrostowe skanowania w celu przechwytywania metadanych i schematu. Skanowanie umożliwia również automatyczne klasyfikowanie danych w oparciu o system i niestandardowe reguły klasyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą
- Dostęp sieciowy między kontem kontrolą i analizą usługi Azure Synapse.
 
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Istnieją trzy sposoby konfigurowania uwierzytelniania dla usługi Azure Blob Storage:

- Tożsamość zarządzana
- Uwierzytelnianie SQL
- Jednostka usługi

    > [!Note]
    > Nowe nazwy logowania można tworzyć tylko w przypadku głównego konta serwera (utworzonego przez proces aprowizacji) lub członkiem `loginmanager` roli bazy danych w bazie danych Master. Po udzieleniu uprawnień trwa około 15 minut, konto kontrolą powinno mieć odpowiednie uprawnienia do skanowania zasobów.

### <a name="managed-identity-recommended"></a>Zarządzana tożsamość (zalecana) 
   
Twoje konto kontrolą ma własną tożsamość zarządzaną, która stanowi zasadniczo swoją nazwę kontrolą podczas jego tworzenia. Musisz utworzyć użytkownika usługi Azure AD w usłudze Azure Synapse Analytics (dawniej SQL DW) o nazwie tożsamości zarządzanej kontrolą, postępując zgodnie z wymaganiami wstępnymi i samouczkiem dotyczącym [tworzenia użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial).

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

Ponadto należy również utworzyć użytkownika usługi Azure AD w usłudze Azure Synapse Analytics, postępując zgodnie z wymaganiami wstępnymi i samouczkiem dotyczącym [tworzenia użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial). Przykładowa składnia SQL służąca do tworzenia uprawnień użytkownika i udzielania:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Aby można było skanować, kontrolą będzie potrzebować **identyfikatora aplikacji** i **klucza tajnego klienta** .

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

Aby utworzyć identyfikator logowania dla usługi Azure Synapse Analytics (dawniej SQL DW), możesz postępować zgodnie z instrukcjami w temacie [Tworzenie logowania](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) .

Gdy wybrana metoda uwierzytelniania to **uwierzytelnianie SQL**, należy uzyskać hasło i magazyn w magazynie kluczy:

1. Pobieranie hasła dla logowania SQL
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *hasło* dla swojego identyfikatora logowania SQL
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu klucza w celu skonfigurowania skanowania

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Rejestrowanie wystąpienia usługi Azure Synapse Analytics (dawniej SQL DW)

Aby zarejestrować nowy serwer usługi Azure Synapse Analytics w Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
1. Wybierz **źródła** na lewym pasku nawigacyjnym
1. Wybierz pozycję **zarejestruj**
1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Synapse Analytics (dawniej SQL DW)**
1. Wybierz przycisk **Kontynuuj**

Na ekranie **Rejestr sources (Azure Synapse Analytics)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Wybierz sposób wskazywania żądanego konta magazynu:
   1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję z listy rozwijanej **subskrypcja platformy Azure** i odpowiedni serwer w polu listy rozwijanej **Nazwa serwera** .
   1. Możesz też wybrać opcję **wprowadź ręcznie** i wprowadzić **nazwę serwera**.
1. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)

