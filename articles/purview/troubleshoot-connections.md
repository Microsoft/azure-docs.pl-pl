---
title: Rozwiązywanie problemów z połączeniami w usłudze Azure kontrolą
description: W tym artykule opisano kroki rozwiązywania problemów z połączeniami w usłudze Azure kontrolą.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677655"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Rozwiązywanie problemów z połączeniami w usłudze Azure kontrolą

W tym artykule opisano sposób rozwiązywania problemów z błędami połączenia podczas konfigurowania skanów dla źródeł danych w usłudze Azure kontrolą.

## <a name="permission-the-credential-on-the-data-source"></a>Uprawnienie do poświadczenia źródła danych

Jeśli używasz tożsamości zarządzanej lub nazwy głównej usługi jako metody uwierzytelniania dla skanów, musisz zezwolić tym tożsamościom na dostęp do źródła danych.

Istnieją konkretne instrukcje dla każdego typu źródła:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Usługa Azure Data Lake Storage 1. generacji](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database wystąpienie zarządzane](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Przechowywanie poświadczeń w magazynie kluczy i korzystanie z odpowiednich nazw i wersji klucza tajnego

Musisz również przechowywać swoje poświadczenia w wystąpieniu Azure Key Vault i korzystać z odpowiednich nazw i wersji klucza tajnego.

Sprawdź to, wykonując poniższe kroki:

1. Przejdź do Key Vault.
1. Wybierz pozycję **Ustawienia** > **Wpisy tajne**.
1. Wybierz klucz tajny, którego używasz do uwierzytelniania względem źródła danych na potrzeby skanowania.
1. Wybierz wersję, której zamierzasz używać, i sprawdź, czy hasło lub klucz konta są poprawne, klikając pozycję **Pokaż wartość klucza tajnego**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Zweryfikuj uprawnienia dla tożsamości zarządzanej przez kontrolą w Azure Key Vault

Sprawdź, czy skonfigurowano odpowiednie uprawnienia dla tożsamości zarządzanej kontrolą, aby uzyskać dostęp do Azure Key Vault.

Aby to sprawdzić, wykonaj następujące czynności:

1. Przejdź do magazynu kluczy i w sekcji **zasady dostępu**

1. Sprawdź, czy tożsamość zarządzana kontrolą jest wyświetlana w sekcji *bieżące zasady dostępu* z co najmniej uprawnieniami **pobierania** i **wyświetlania** na wpisach tajnych

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Obraz przedstawiający listę rozwijaną opcji uprawnień get i list":::

Jeśli nie widzisz swojej tożsamości zarządzanej w usłudze kontrolą, postępuj zgodnie z instrukcjami w temacie [Tworzenie i zarządzanie poświadczeniami do skanowania](manage-credentials.md) , aby je dodać. 

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
