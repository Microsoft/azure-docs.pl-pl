---
title: Jak skanować Eksplorator danych platformy Azure
description: W tym przewodniku opisano szczegóły dotyczące sposobu skanowania Eksplorator danych platformy Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896116"
---
# <a name="register-and-scan-azure-data-explorer"></a>Rejestrowanie i skanowanie Eksplorator danych platformy Azure

W tym artykule opisano sposób rejestrowania konta usługi Azure Eksplorator danych w usłudze Azure kontrolą i konfigurowania skanowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Usługa Azure Eksplorator danych obsługuje pełne i przyrostowe skanowania w celu przechwytywania metadanych i schematu. Skanowanie umożliwia również automatyczne klasyfikowanie danych w oparciu o system i niestandardowe reguły klasyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Musisz być administratorem źródła danych usługi Azure kontrolą

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Istnieje tylko jeden sposób skonfigurowania uwierzytelniania dla Eksploratora danych Azure:

- Jednostka usługi

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

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Udzielanie dostępu do nazwy głównej usługi do wystąpienia programu Azure Data Explorer

1. Przejdź do witryny Azure Portal. Następnie przejdź do wystąpienia usługi Azure Data Explorer.

1. Dodaj nazwę główną usługi do roli **AllDatabasesViewer** na karcie **uprawnienia** , jak pokazano na poniższym zrzucie ekranu.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Zrzut ekranu przedstawiający Dodawanie nazwy głównej usługi w uprawnieniach" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Rejestrowanie konta usługi Azure Eksplorator danych

Aby zarejestrować nowe konto usługi Azure Eksplorator danych (Kusto) w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
1. Wybierz **źródła** na lewym pasku nawigacyjnym
1. Wybierz pozycję **zarejestruj**
1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Eksplorator danych**
1. Wybierz przycisk **Kontynuuj**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Rejestrowanie nowego źródła danych" border="true":::

Na ekranie **Rejestr sources (Azure Eksplorator danych (Kusto))** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Wybierz sposób wskazywania żądanego konta magazynu:
   1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję z listy rozwijanej **subskrypcja platformy Azure** i odpowiedni klaster w polu listy rozwijanej **klaster** .
   1. Możesz też wybrać opcję **wprowadź ręcznie** i wprowadzić punkt końcowy usługi (URL).
1. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
