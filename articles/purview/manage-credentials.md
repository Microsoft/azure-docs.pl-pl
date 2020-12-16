---
title: Tworzenie poświadczeń i zarządzanie nimi na potrzeby skanowania
description: W tym artykule opisano kroki tworzenia poświadczeń i zarządzania nimi w usłudze Azure kontrolą.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 30004306d9ff44df04a26640a2bd7a09256fce25
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516679"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Poświadczenia uwierzytelniania źródłowego w usłudze Azure kontrolą

W tym artykule opisano, jak utworzyć poświadczenia w usłudze Azure kontrolą, aby szybko ponownie wykorzystać i zastosować zapisane informacje o uwierzytelnianiu do skanowania źródła danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Magazyn kluczy platformy Azure. Aby dowiedzieć się, jak ją utworzyć, zobacz [Szybki Start: Tworzenie magazynu kluczy przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Wprowadzenie
Poświadczenie to informacje uwierzytelniające, które mogą być używane przez usługę Azure kontrolą do uwierzytelniania w zarejestrowanych źródłach danych. Obiekt Credential można utworzyć dla różnych typów scenariuszy uwierzytelniania (takich jak uwierzytelnianie podstawowe wymagające nazwy użytkownika/hasła) i przechwycenia konkretnych informacji wymaganych na podstawie wybranego typu metody uwierzytelniania. Poświadczenia używają istniejących wpisów tajnych magazynów kluczy platformy Azure do pobierania poufnych informacji o uwierzytelnianiu podczas procesu tworzenia poświadczeń.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Używanie tożsamości zarządzanej kontrolą do konfigurowania skanów
Jeśli używasz tożsamości zarządzanej kontrolą do konfigurowania skanów, nie musisz jawnie tworzyć poświadczeń i łączyć Magazyn kluczy z kontrolą, aby je zapisać. Aby uzyskać szczegółowe instrukcje dotyczące dodawania tożsamości zarządzanej kontrolą w celu uzyskania dostępu do skanowania źródeł danych, zapoznaj się z poniższymi sekcjami uwierzytelniania źródła danych:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Usługa Azure Data Lake Storage 1. generacji](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Wystąpienie zarządzane usługi Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Tworzenie połączeń magazynów kluczy platformy Azure na koncie usługi Azure kontrolą

Przed utworzeniem poświadczenia musisz najpierw skojarzyć co najmniej jedno istniejące wystąpienie Azure Key Vault z kontem usługi Azure kontrolą.

1. W menu nawigacji usługi Azure kontrolą przejdź do centrum zarządzania, a następnie przejdź do okna poświadczenia

2. Na pasku poleceń poświadczeń wybierz pozycję Zarządzaj połączeniami Key Vault

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Zarządzanie połączeniami AKV":::

3. Wybierz pozycję + Nowy w panelu Zarządzaj połączeniami Key Vault 

4. Podaj wymagane informacje, a następnie wybierz pozycję Utwórz.

5. Upewnij się, że Key Vault została pomyślnie skojarzona z kontem usługi Azure kontrolą

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Wyświetlanie połączeń AKV":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Przyznaj usłudze kontrolą zarządzaną tożsamość do Azure Key Vault

Przejdź do magazynu kluczy — > zasad dostępu — > Dodaj zasady dostępu. Udziel uprawnienia Get na liście rozwijanej uprawnienia do wpisów tajnych, a następnie wybierz pozycję podmiot zabezpieczeń jako kontrolą MSI. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Dodawanie kontrolą MSI do AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Dodawanie zasad dostępu":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Zapisz zasady dostępu":::

## <a name="create-a-new-credential"></a>Utwórz nowe poświadczenie

Typ poświadczeń obsługiwany w kontrolą Dzisiaj:
* Uwierzytelnianie podstawowe: **hasło** należy dodać jako klucz tajny w magazynie kluczy
* Nazwa główna usługi: **klucz jednostki usługi** należy dodać jako klucz tajny w magazynie kluczy 
* Uwierzytelnianie SQL: **hasło** należy dodać jako klucz tajny w magazynie kluczy
* Klucz konta: **klucz konta** zostanie dodany jako klucz tajny w magazynie kluczy

Poniżej znajduje się więcej informacji na temat dodawania wpisów tajnych do magazynu kluczy: (Wstaw artykuł z magazynu kluczy)

Po zapisaniu wpisów tajnych w magazynie kluczy Utwórz nowe poświadczenie, wybierając pozycję + Nowy na pasku poleceń poświadczenia. Podaj wymagane informacje, w tym wybierając metodę uwierzytelniania i wystąpienie Key Vault, z którego ma zostać wybrana wartość klucza tajnego. Po wypełnieniu wszystkich szczegółów kliknij pozycję Utwórz.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nowe poświadczenie":::

Sprawdź, czy nowe poświadczenie jest wyświetlane w widoku listy poświadczeń i jest gotowe do użycia

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Wyświetl poświadczenia":::

## <a name="manage-your-key-vault-connections"></a>Zarządzanie połączeniami magazynu kluczy

1. Wyszukaj/Znajdź Key Vault połączeń według nazwy

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Wyszukaj Magazyn kluczy":::

1. Usuń co najmniej jedno Key Vault połączenia
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Usuń Magazyn kluczy":::

## <a name="manage-your-credentials"></a>Zarządzanie poświadczeniami

1. Wyszukaj/Znajdź poświadczenia według nazwy
  
2. Wybierz i wprowadź aktualizacje istniejącego poświadczenia

3. Usuń jedno lub więcej poświadczeń
