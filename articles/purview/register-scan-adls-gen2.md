---
title: Zarejestruj i Skanuj Azure Data Lake Storage (ADLS) Gen2
description: W tym samouczku opisano sposób skanowania Azure Data Lake Storage Gen2.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882403"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Rejestruj i Skanuj Azure Data Lake Storage Gen2

W tym artykule opisano sposób rejestrowania Azure Data Lake Storage Gen2 jako źródła danych w usłudze Azure kontrolą i konfigurowania skanowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Azure Data Lake Storage Gen2 źródło danych obsługuje następujące funkcje:

- **Pełne i przyrostowe skanowania** do przechwytywania metadanych i klasyfikacji w Azure Data Lake Storage Gen2

- Elementy **powiązane między zasobami** danych dla działań Copy/przepływu danych usługi ADF

## <a name="prerequisites"></a>Wymagania wstępne

Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Dla Azure Data Lake Storage Gen2 są obsługiwane następujące metody uwierzytelniania:

- Tożsamość zarządzana
- Jednostka usługi
- Klucz konta

#### <a name="managed-identity-recommended"></a>Zarządzana tożsamość (zalecana)

W przypadku wybrania opcji **tożsamość zarządzana** w celu skonfigurowania połączenia należy najpierw nadać kontu kontrolą uprawnienie do skanowania źródła danych:

1. Przejdź do konta magazynu ADLS Gen2.
1. Wybierz pozycję **Access Control (IAM)** z menu nawigacji po lewej stronie. 
1. Wybierz pozycję **+ Dodaj**.
1. Ustaw **rolę** na **czytnik danych obiektów blob magazynu** i wprowadź nazwę konta usługi Azure kontrolą w obszarze **Wybierz** pole wejściowe. Następnie wybierz pozycję **Zapisz** , aby nadać temu przypisaniu roli konto kontrolą.

> [!Note]
> Aby uzyskać więcej informacji, zobacz kroki w artykule [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md)

#### <a name="account-key"></a>Klucz konta

Gdy wybrana metoda uwierzytelniania to **klucz konta**, należy uzyskać klucz dostępu i magazyn w magazynie kluczy:

1. Przejdź do swojego konta magazynu ADLS Gne2
1. Wybierz pozycję **ustawienia > klucze dostępu**
1. Skopiuj *klucz* i Zapisz go w miejscu dla następnych kroków
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *klucz* z konta magazynu
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu klucza w celu skonfigurowania skanowania

#### <a name="service-principal"></a>Jednostka usługi

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

Wymagane jest uzyskanie identyfikatora aplikacji i klucza tajnego jednostki usługi:

1. Przejdź do nazwy głównej usługi w [Azure Portal](https://portal.azure.com)
1. Skopiuj wartości **Identyfikator aplikacji (klienta)** z **omówienia** i **klucz tajny klienta** z **certyfikatów & wpisy tajne**.
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź wybraną **nazwę** oraz **wartość** jako **klucz tajny klienta** z nazwy głównej usługi
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu nazwy głównej usługi, aby skonfigurować skanowanie

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Udzielanie dostępu do nazwy głównej usługi do konta ADLS Gen2

1. Przejdź do konta magazynu.
1. Wybierz pozycję **Access Control (IAM)** z menu nawigacji po lewej stronie. 
1. Wybierz pozycję **+ Dodaj**.
1. Ustaw **rolę** na **czytnik danych obiektów blob magazynu** i wprowadź nazwę główną usługi lub identyfikator obiektu w obszarze **Wybierz** pole wejściowe. Następnie wybierz pozycję **Zapisz** , aby nadać temu przypisaniu roli nazwę główną usługi.
### <a name="firewall-settings"></a>Ustawienia zapory

> [!NOTE]
> Jeśli masz włączoną zaporę dla konta magazynu, podczas konfigurowania skanowania musisz użyć metody uwierzytelniania **tożsamości zarządzanej** .

1. Przejdź do swojego konta magazynu ASLS Gen2 w [Azure Portal](https://portal.azure.com)
1. Przejdź do **ustawień > sieci** i
1. Wybierz **wybrane sieci** w obszarze **Zezwalaj na dostęp z**
1. W sekcji **wyjątki** wybierz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** i naciśnij klawisz **Save**

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Zrzut ekranu przedstawiający ustawienie zapory":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Rejestrowanie źródła danych Azure Data Lake Storage Gen2

Aby zarejestrować nowe konto ADLS Gen2 w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
2. Wybierz **źródła** na lewym pasku nawigacyjnym
3. Wybierz pozycję **zarejestruj**
4. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Data Lake Storage Gen2**
5. Wybierz przycisk **Kontynuuj**

Na ekranie **Rejestr sources (Azure Data Lake Storage Gen2)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
2. Wybierz subskrypcję, aby odfiltrować konta magazynu
3. Wybierz konto magazynu
4. Wybierz kolekcję lub Utwórz nową (opcjonalnie)
5. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)