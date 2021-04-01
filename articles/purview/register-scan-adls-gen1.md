---
title: Zarejestruj i Skanuj Azure Data Lake Storage (ADLS) Gen1
description: W tym samouczku opisano sposób skanowania danych z Azure Data Lake Storage Gen1 do usługi Azure kontrolą.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693719"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Rejestruj i Skanuj Azure Data Lake Storage Gen1

W tym artykule opisano sposób rejestrowania Azure Data Lake Storage Gen1 jako źródła danych w usłudze Azure kontrolą oraz konfigurowania na nim skanowania.

> [!Note]
> Usługa Azure Data Lake Storage Gen2 jest teraz ogólnie dostępna. Zalecamy, aby zacząć z niej korzystać już dziś. Aby uzyskać więcej informacji, zobacz [stronę produktu](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Azure Data Lake Storage Gen1 źródło danych obsługuje następujące funkcje:

- **Pełne i przyrostowe skanowania** do przechwytywania metadanych i klasyfikacji w Azure Data Lake Storage Gen1

- Elementy **powiązane między zasobami** danych dla działań Copy/przepływu danych usługi ADF

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Dla Azure Data Lake Storage Gen1 są obsługiwane następujące metody uwierzytelniania:

- Tożsamość zarządzana
- Jednostka usługi

### <a name="managed-identity-recommended"></a>Zarządzana tożsamość (zalecana)

W celu ułatwienia i bezpieczeństwa możesz użyć kontrolą MSI do uwierzytelniania w magazynie danych.

Aby skonfigurować skanowanie przy użyciu pliku MSI Data Catalog, musisz najpierw nadać kontu kontrolą uprawnienia do skanowania źródła danych. Ten krok należy wykonać przed skonfigurowaniem skanowania lub *przeprowadzeniem* skanowania zakończy się niepowodzeniem.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Dodawanie Data Catalog MSI do konta Azure Data Lake Storage Gen1

Plik MSI katalogu można dodać na poziomie subskrypcji, grupy zasobów lub zasobu, w zależności od tego, co ma mieć uprawnienia do skanowania.

> [!Note]
> Musisz być właścicielem subskrypcji, aby móc dodać zarządzaną tożsamość w ramach zasobu platformy Azure.

1. W [Azure Portal](https://portal.azure.com)Znajdź subskrypcję, grupę zasobów lub zasób (na przykład konto magazynu Azure Data Lake Storage Gen1), które chcesz zezwolić na skanowanie katalogu.

2. Kliknij pozycję **Przegląd** , a następnie wybierz pozycję **Eksplorator danych** .

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Wybieranie kontroli dostępu":::

3. Kliknij pozycję **dostęp** na górnym pasku nawigacyjnym

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Kliknij pozycję dostęp":::

4. Kliknij przycisk **Dodaj**. Dodaj **katalog kontrolą** w obszarze Wybieranie użytkownika lub grupy. Wybierz uprawnienia **Odczyt** i **wykonywanie** . Upewnij się, że wybrano **ten folder i wszystkie elementy podrzędne** w opcji Dodaj do, jak pokazano na poniższym zrzucie ekranu, a następnie kliknij przycisk **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="szczegóły uwierzytelniania MSI":::

5. Jeśli magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu nazwy głównej usługi, aby skonfigurować skanowanie
> [!Note]
> Po dodaniu pliku MSI katalogu w źródle danych poczekaj do 15 minut, aż uprawnienia zostaną rozpropagowane przed skonfigurowaniem skanowania.

Po około 15 minutach katalog powinien mieć odpowiednie uprawnienia, aby można było skanować zasoby.

### <a name="service-principal"></a>Jednostka usługi

Aby użyć nazwy głównej usługi, należy najpierw utworzyć jedną z następujących kroków:

1. Przejdź do [Azure Portal](https://portal.azure.com).

2. Wybierz **Azure Active Directory** z menu po lewej stronie.

3. Wybierz pozycję **Rejestracje aplikacji**.

4. Wybierz pozycję **+ Nowa rejestracja aplikacji**.

5. Wprowadź nazwę **aplikacji** (nazwę główną usługi).

6. Wybierz **konta tylko w tym katalogu organizacji**.

7. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web** i wprowadź dowolny żądany adres URL. nie musi być w rzeczywistości ani działać.

8. Następnie wybierz pozycję **Zarejestruj**.

9. Skopiuj wartości z zarówno nazwy wyświetlanej, jak i identyfikatora aplikacji.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Dodawanie jednostki usługi Data Catalog do konta Azure Data Lake Storage Gen1
1. W [Azure Portal](https://portal.azure.com)Znajdź subskrypcję, grupę zasobów lub zasób (na przykład konto magazynu Azure Data Lake Storage Gen1), które chcesz zezwolić na skanowanie katalogu.

2. Kliknij pozycję **Przegląd** , a następnie wybierz pozycję **Eksplorator danych** .

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Wybieranie kontroli dostępu":::

3. Kliknij pozycję **dostęp** na górnym pasku nawigacyjnym

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Kliknij pozycję dostęp":::

4. Kliknij przycisk **Dodaj**. Dodaj **aplikację główną usługi** do wyboru wybierz użytkownika lub grupę. Wybierz uprawnienia **Odczyt** i **wykonywanie** . Upewnij się, że wybrano **ten folder i wszystkie elementy podrzędne** w opcji Dodaj do, jak pokazano na poniższym zrzucie ekranu, a następnie kliknij przycisk **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="szczegóły uwierzytelniania nazwy głównej usługi":::

5. Jeśli magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu nazwy głównej usługi, aby skonfigurować skanowanie.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Rejestrowanie źródła danych Azure Data Lake Storage Gen1

Aby zarejestrować nowe konto ADLS Gen1 w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do kontrolą Data Catalog.
2. Wybierz pozycję **źródła** na lewym pasku nawigacyjnym.
3. Wybierz pozycję **zarejestruj**
4. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Data Lake Storage Gen1**. 
5. Wybierz opcję **Kontynuuj**.

Na ekranie rejestr sources (Azure Data Lake Storage Gen1) wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
2. Wybierz subskrypcję, aby odfiltrować konta magazynu
3. Wybierz konto magazynu
4. Wybierz kolekcję lub Utwórz nową (opcjonalnie)
5. Zakończ, aby zarejestrować źródło danych.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
