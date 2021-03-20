---
title: Jak skanować pliki Azure
description: W tym przewodniku opisano szczegóły dotyczące sposobu skanowania plików platformy Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554889"
---
# <a name="register-and-scan-azure-files"></a>Rejestruj i Skanuj Azure Files

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Azure Files obsługuje pełne i przyrostowe skanowania w celu przechwytywania metadanych i stosowania klasyfikacji metadanych na podstawie klasyfikacji systemu i klienta.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed zarejestrowaniem źródeł danych Utwórz konto usługi Azure kontrolą. Aby uzyskać więcej informacji na temat tworzenia konta kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
- Aby skonfigurować i zaplanować skanowanie, musisz być administratorem źródła danych, aby uzyskać szczegółowe informacje, zobacz [uprawnienia katalogu](catalog-permissions.md) .

## <a name="register-an-azure-files-storage-account"></a>Rejestrowanie konta magazynu Azure Files

Aby zarejestrować nowe konto Azure Files w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do kontrolą Data Catalog.
1. Na lewym pasku nawigacyjnym wybierz pozycję **centrum zarządzania** .
1. Wybierz pozycję **źródła danych** w obszarze **źródła i skanowanie**.
1. Wybierz pozycję **+ Nowe**.
1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure Files**. Wybierz opcję **Kontynuuj**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="Rejestrowanie nowego źródła danych" border="true":::

Na ekranie **Rejestr sources (Azure Files)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Wybierz sposób wskazywania żądanego konta magazynu:
   1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję w polu listy rozwijanej **subskrypcja platformy Azure** i odpowiednie konto magazynu z listy rozwijanej **nazwa konta magazynu** .
   1. Możesz też wybrać opcję **wprowadź ręcznie** i wprowadzić punkt końcowy usługi (URL).
1. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Aby skonfigurować uwierzytelnianie dla magazynu Azure Files przy użyciu klucza konta, wykonaj następujące czynności:

1. Wybierz metodę uwierzytelniania jako **klucz konta**.
2. Wybierz opcję **z subskrypcji platformy Azure** .
3. Wybierz subskrypcję platformy Azure, w której istnieje konto Azure Files.
4. Wybierz nazwę konta magazynu z listy.
5. Kliknij przycisk **Finish** (Zakończ).

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)