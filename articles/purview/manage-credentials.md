---
title: Tworzenie poświadczeń i zarządzanie nimi na potrzeby skanowania
description: Zapoznaj się z instrukcjami dotyczącymi tworzenia poświadczeń i zarządzania nimi w usłudze Azure kontrolą.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461712"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Poświadczenia uwierzytelniania źródłowego w usłudze Azure kontrolą

W tym artykule opisano sposób tworzenia poświadczeń w usłudze Azure kontrolą. Te zapisane poświadczenia pozwalają szybko ponownie wykorzystać i zastosować zapisane informacje uwierzytelniania do skanowania źródła danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Magazyn kluczy platformy Azure. Aby dowiedzieć się, jak ją utworzyć, zobacz [Szybki Start: Tworzenie magazynu kluczy przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Wprowadzenie

Poświadczenie to informacje uwierzytelniające, które mogą być używane przez usługę Azure kontrolą do uwierzytelniania w zarejestrowanych źródłach danych. Obiekt Credential można utworzyć dla różnych typów scenariuszy uwierzytelniania, takich jak uwierzytelnianie podstawowe wymagające nazwy użytkownika/hasła. Informacje dotyczące rejestrowania poświadczeń wymagane do uwierzytelnienia na podstawie wybranego typu metody uwierzytelniania. Poświadczenia używają istniejących wpisów tajnych magazynów kluczy platformy Azure do pobierania poufnych informacji o uwierzytelnianiu podczas procesu tworzenia poświadczeń.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Używanie tożsamości zarządzanej kontrolą do konfigurowania skanów

Jeśli używasz tożsamości zarządzanej kontrolą do konfigurowania skanów, nie musisz jawnie tworzyć poświadczeń i łączyć Magazyn kluczy z kontrolą, aby je zapisać. Aby uzyskać szczegółowe instrukcje dotyczące dodawania tożsamości zarządzanej kontrolą w celu uzyskania dostępu do skanowania źródeł danych, zapoznaj się z poniższymi sekcjami uwierzytelniania specyficznymi dla źródła danych:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Usługa Azure Data Lake Storage 1. generacji](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database wystąpienie zarządzane](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Tworzenie połączeń magazynów kluczy platformy Azure na koncie usługi Azure kontrolą

Aby można było utworzyć poświadczenia, najpierw skojarz co najmniej jedno istniejące wystąpienie Azure Key Vault z kontem usługi Azure kontrolą.

1. W [Azure Portal](https://portal.azure.com)wybierz swoje konto usługi Azure kontrolą i Otwórz usługę Azure kontrolą Studio. Przejdź do **centrum zarządzania** w usłudze Azure kontrolą Studio, a następnie przejdź do **poświadczeń**.

2. Na stronie **poświadczenia** wybierz pozycję **zarządzaj połączeniami Key Vault**.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Zarządzanie połączeniami Azure Key Vault":::

3. Wybierz pozycję **+ Nowy** na stronie Zarządzanie połączeniami Key Vault.

4. Podaj wymagane informacje, a następnie wybierz pozycję **Utwórz**.

5. Upewnij się, że Key Vault została pomyślnie skojarzona z kontem usługi Azure kontrolą, jak pokazano w tym przykładzie:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Wyświetl Azure Key Vault połączeń, aby potwierdzić.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Przyznaj usłudze kontrolą zarządzaną tożsamość do Azure Key Vault

1. Przejdź do Azure Key Vault.

2. Wybierz stronę **zasady dostępu** .

3. Wybierz pozycję **Dodaj zasady dostępu**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Dodawanie kontrolą MSI do AKV":::

4. Z listy rozwijanej uprawnienia do wpisów **tajnych** wybierz pozycję uprawnienia **Get** i **list** .

5. Dla **opcji wybór podmiotu zabezpieczeń** wybierz tożsamość zarządzaną kontrolą. Plik MSI kontrolą można wyszukać przy użyciu nazwy wystąpienia kontrolą **lub** identyfikatora aplikacji zarządzanej tożsamości. Obecnie nie są obsługiwane tożsamości złożone (nazwa tożsamości zarządzanej i identyfikator aplikacji).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Dodawanie zasad dostępu":::

6. Wybierz pozycję **Dodaj**.

7. Wybierz pozycję **Zapisz** , aby zapisać zasady dostępu.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Zapisz zasady dostępu":::

## <a name="create-a-new-credential"></a>Utwórz nowe poświadczenie

Te typy poświadczeń są obsługiwane w programie kontrolą:

- Uwierzytelnianie podstawowe: **hasło** należy dodać jako wpis tajny w magazynie kluczy.
- Nazwa główna usługi: klucz jednostki **usługi** należy dodać jako wpis tajny w magazynie kluczy.
- Uwierzytelnianie SQL: **hasło** należy dodać jako wpis tajny w magazynie kluczy.
- Klucz konta: **klucz konta** należy dodać jako klucz tajny w magazynie kluczy.
- ARN roli: dla źródła danych Amazon S3 Dodaj **rolę ARN** w AWS. 

Aby uzyskać więcej informacji, zobacz [Dodawanie wpisu tajnego do Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) i [Tworzenie nowej roli AWS dla kontrolą](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

Po zapisaniu wpisów tajnych w magazynie kluczy:

1. W usłudze Azure kontrolą przejdź na stronę poświadczenia.

2. Utwórz nowe poświadczenie, wybierając pozycję **+ Nowy**.

3. Podaj wymagane informacje. Wybierz **metodę uwierzytelniania** i **połączenie Key Vault** , z którego ma zostać wybrana wartość klucza tajnego.

4. Po wypełnieniu wszystkich szczegółów wybierz pozycję **Utwórz**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nowe poświadczenie":::

5. Sprawdź, czy nowe poświadczenie jest wyświetlane w widoku listy i jest gotowe do użycia.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Wyświetl poświadczenia":::

## <a name="manage-your-key-vault-connections"></a>Zarządzanie połączeniami magazynu kluczy

1. Wyszukaj/Znajdź Key Vault połączeń według nazwy

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Wyszukaj Magazyn kluczy":::

2. Usuń co najmniej jedno Key Vault połączenia

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Usuń Magazyn kluczy":::

## <a name="manage-your-credentials"></a>Zarządzanie poświadczeniami

1. Wyszukaj/Znajdź poświadczenia według nazwy.
  
2. Wybierz i wprowadź aktualizacje istniejącego poświadczenia.

3. Usuń jedno lub więcej poświadczeń.

## <a name="next-steps"></a>Następne kroki

[Tworzenie zestawu reguł skanowania](create-a-scan-rule-set.md)
