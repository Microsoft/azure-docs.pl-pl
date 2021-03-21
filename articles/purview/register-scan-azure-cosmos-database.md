---
title: Jak skanować usługę Azure Cosmos Database (SQL API)
description: W tym przewodniku opisano szczegóły dotyczące sposobu skanowania bazy danych Azure Cosmos Database (SQL API).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696247"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Rejestrowanie i skanowanie bazy danych Azure Cosmos Database (SQL API)

W tym artykule opisano sposób rejestrowania konta bazy danych Azure Cosmos Database (API) w usłudze Azure kontrolą i konfigurowania skanowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Usługa Azure Cosmos Database (interfejs API SQL) obsługuje pełne i przyrostowe skanowania w celu przechwytywania metadanych i schematu. Skanowanie umożliwia również automatyczne klasyfikowanie danych w oparciu o system i niestandardowe reguły klasyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Istnieje tylko jeden sposób skonfigurowania uwierzytelniania dla usługi Azure Cosmos Database (interfejs API SQL):

- Klucz konta
 
### <a name="account-key"></a>Klucz konta

Gdy wybrana metoda uwierzytelniania to **klucz konta**, należy uzyskać klucz dostępu i magazyn w magazynie kluczy:

1. Przejdź do konta Cosmos DB w Azure Portal 
1. Wybierz   >  **klucze** ustawień 
1. Skopiuj *klucz* i Zapisz go w miejscu dla następnych kroków
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *klucz* z konta magazynu
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu klucza w celu skonfigurowania skanowania

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Rejestrowanie konta bazy danych Azure Cosmos Database (API)

Aby zarejestrować nowe konto usługi Azure Cosmos Database (API) w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
1. Wybierz **źródła** na lewym pasku nawigacyjnym
1. Wybierz pozycję **zarejestruj**
1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Cosmos dB (interfejs API SQL)**
1. Wybierz przycisk **Kontynuuj**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Rejestrowanie nowego źródła danych" border="true":::

Na ekranie **Rejestr sources (Azure Cosmos dB (SQL API))** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Wybierz sposób wskazywania żądanego konta magazynu:
   1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję w polu listy rozwijanej **subskrypcja platformy Azure** i odpowiednie konto cosmosDB z listy rozwijanej **nazwa konta Cosmos DB** .
   1. Możesz też wybrać opcję **wprowadź ręcznie** i wprowadzić punkt końcowy usługi (URL).
1. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
