---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c0ca96d1829a73f856de021d1286e53007b03219
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368981"
---
Platforma Azure udostępnia następujące wbudowane role platformy Azure do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- [Właściciel danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): służy do ustawiania własności i zarządzania kontrolą dostępu POSIX dla Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Użyj, aby przyznać uprawnienia do odczytu/zapisu/usuwania dla zasobów magazynu obiektów BLOB.
- [Czytnik danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): służy do przyznawania uprawnień tylko do odczytu zasobom magazynu obiektów BLOB.
- [Delegat obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Uzyskaj klucz delegowania użytkownika, który ma być używany do tworzenia sygnatury dostępu współdzielonego podpisanego przy użyciu poświadczeń usługi Azure AD dla kontenera lub obiektu BLOB.
- [Współautor danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Użyj, aby przyznać uprawnienia do odczytu/zapisu/usuwania do kolejek platformy Azure.
- [Czytnik danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): służy do przyznawania uprawnień tylko do odczytu dla kolejek platformy Azure.
- [Procesor komunikatów danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): służy do przyznawania uprawnień wglądu, pobierania i usuwania do komunikatów w kolejkach usługi Azure Storage.
- [Nadawca komunikatu o danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): służy do przyznawania uprawnień do dodawania do komunikatów w kolejkach usługi Azure Storage.

Tylko role jawnie zdefiniowane na potrzeby dostępu do danych zezwalają podmiotowi zabezpieczeń na dostęp do danych obiektu BLOB lub kolejki. Role wbudowane, takie jak **właściciel**, **współautor**i **współautor konta magazynu** , umożliwiają podmiotowi zabezpieczeń Zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych obiektu BLOB lub kolejki w ramach tego konta za pośrednictwem usługi Azure AD. Jeśli jednak rola obejmuje element **Microsoft. Storage/storageAccounts/listKeys/Action**, użytkownik, do którego przypisano tę rolę, może uzyskać dostęp do danych na koncie magazynu za pośrednictwem autoryzacji klucza współużytkowanego przy użyciu kluczy dostępu do konta. Aby uzyskać więcej informacji, zobacz [używanie Azure Portal do uzyskiwania dostępu do danych obiektu BLOB lub kolejki](../articles/storage/common/storage-access-blobs-queues-portal.md).

Aby uzyskać szczegółowe informacje na temat wbudowanych ról platformy Azure dla usługi Azure Storage dla usług danych i usługi zarządzania, zobacz sekcję dotyczącą **magazynu** w [rolach wbudowanych platformy Azure dla funkcji RBAC systemu Azure](../articles/role-based-access-control/built-in-roles.md#storage). Ponadto, aby uzyskać informacje o różnych typach ról, które udostępniają uprawnienia na platformie Azure, zobacz [role administratora subskrypcji klasycznej, role RBAC i role usługi Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Propagowanie przypisań ról RBAC może potrwać do 5 minut.
