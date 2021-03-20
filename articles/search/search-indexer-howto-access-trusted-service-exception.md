---
title: Dostęp indeksatora do usługi Azure Storage przy użyciu wyjątku usługi zaufanej
titleSuffix: Azure Cognitive Search
description: Włącz dostęp do danych za pomocą indeksatora w usłudze Azure Wyszukiwanie poznawcze do danych przechowywanych bezpiecznie w usłudze Azure Storage.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101379"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Dostęp indeksatora do usługi Azure Storage przy użyciu wyjątku usługi zaufanej (Azure Wyszukiwanie poznawcze)

Indeksatory w usłudze Wyszukiwanie poznawcze platformy Azure, które uzyskują dostęp do danych na kontach usługi Azure Storage, mogą korzystać z funkcji [zaufanego wyjątku usług](../storage/common/storage-network-security.md#exceptions) , aby bezpiecznie uzyskiwać dostęp do danych. Ten mechanizm umożliwia klientom, którzy nie są w stanie udzielić [dostępu indeksatora przy użyciu reguł zapory IP](search-indexer-howto-access-ip-restricted.md) , proste, bezpieczne i bezpłatne rozwiązanie do uzyskiwania dostępu do danych na kontach magazynu.

> [!NOTE]
> Obsługa uzyskiwania dostępu do danych w kontach magazynu za pomocą zaufanego wyjątku usługi jest ograniczona do magazynu obiektów blob platformy Azure i Azure Data Lake magazynu Gen2. Usługa Azure Table Storage nie jest obsługiwana.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Krok 1. Konfigurowanie połączenia przy użyciu tożsamości zarządzanej

Postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie połączenia z kontem usługi Azure Storage przy użyciu tożsamości zarządzanej](search-howto-managed-identities-storage.md). Po zakończeniu usługa wyszukiwania zostanie zarejestrowana za pomocą Azure Active Directory jako usługi zaufanej, a użytkownik otrzyma uprawnienia do usługi Azure Storage, która daje prawo do wyszukiwania danych lub informacji.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Krok 2. umożliwienie zaufanym usługom firmy Microsoft uzyskiwania dostępu do konta magazynu

W Azure Portal przejdź do karty **zapory i sieci wirtualne** na koncie magazynu. Upewnij się, że opcja **Zezwalaj na dostęp do tego konta magazynu zaufanych usług firmy Microsoft** jest zaznaczona. Ta opcja zezwala na dostęp do danych na koncie magazynu tylko określonemu wystąpieniu usługi wyszukiwania z odpowiednim dostępem opartym na rolach (silnego uwierzytelniania), nawet jeśli jest on zabezpieczony przez reguły zapory adresów IP.

![Wyjątek usługi zaufanej](media\search-indexer-howto-secure-access\exception.png "Wyjątek usługi zaufanej")

Indeksatory będą teraz mogły uzyskiwać dostęp do danych na koncie magazynu, nawet jeśli konto jest zabezpieczone za pomocą reguł zapory adresów IP.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o indeksatorach usługi Azure Storage:

- [Indeksator usługi Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indeksator](search-howto-index-azure-data-lake-storage.md)
- [Indeksator tabeli platformy Azure](search-howto-indexing-azure-tables.md)