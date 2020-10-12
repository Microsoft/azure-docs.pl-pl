---
title: Zezwalaj na dostęp do magazynu za pomocą zaufanego wyjątku usługi
titleSuffix: Azure Cognitive Search
description: Przewodnik, który opisuje sposób konfigurowania wyjątku usługi zaufanej w celu bezpiecznego dostępu do danych z kont magazynu.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320476"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Bezpieczne uzyskiwanie dostępu do danych z kont magazynu za pomocą wyjątku usługi zaufanej

Indeksatory, które uzyskują dostęp do danych na kontach magazynu, mogą korzystać z funkcji [zaufanego wyjątku usługi](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) , aby bezpiecznie uzyskiwać dostęp do danych. Ten mechanizm umożliwia klientom, którzy nie mogą udzielić [dostępu indeksatora za pośrednictwem reguł zapory adresów IP](search-indexer-howto-access-ip-restricted.md) , prostą, bezpieczną i bezpłatną alternatywą dla dostępu do danych na kontach magazynu.

> [!NOTE]
> Obsługa uzyskiwania dostępu do danych na kontach magazynu za pośrednictwem zaufanego wyjątku usługi jest ograniczona do magazynu obiektów blob platformy Azure i Azure Data Lake magazynu Gen2. Usługa Azure Table Storage nie jest obsługiwana.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Krok 1. Konfigurowanie połączenia z kontem magazynu za pośrednictwem tożsamości

Aby skonfigurować indeksatory do uzyskiwania dostępu do kont magazynu za pośrednictwem zarządzanej tożsamości usługi wyszukiwania, postępuj zgodnie ze szczegółowymi informacjami w [przewodniku](search-howto-managed-identities-storage.md) dotyczącym dostępu do tożsamości.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Krok 2. umożliwienie zaufanym usługom firmy Microsoft uzyskiwania dostępu do konta magazynu

W Azure Portal przejdź do karty "zapory i sieci wirtualne" na koncie magazynu. Upewnij się, że opcja "Zezwalaj na dostęp do tego konta magazynu zaufanych usług firmy Microsoft" jest zaznaczona. Ta opcja zezwala na dostęp do danych na koncie magazynu tylko określonemu wystąpieniu usługi wyszukiwania z odpowiednim dostępem opartym na rolach (silnego uwierzytelniania), nawet jeśli jest on zabezpieczony przez reguły zapory adresów IP.

![Wyjątek usługi zaufanej](media\search-indexer-howto-secure-access\exception.png "Wyjątek usługi zaufanej")

Indeksatory będą teraz mogły uzyskiwać dostęp do danych na koncie magazynu, nawet jeśli konto jest zabezpieczone za pomocą reguł zapory adresów IP.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o indeksatorach usługi Azure Storage:

- [Indeksator usługi Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indeksator](search-howto-index-azure-data-lake-storage.md)
- [Indeksator tabeli platformy Azure](search-howto-indexing-azure-tables.md)
