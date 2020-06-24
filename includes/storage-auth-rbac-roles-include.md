---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519576"
---
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- [Właściciel danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): służy do ustawiania własności i zarządzania kontrolą dostępu POSIX dla Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Użyj, aby przyznać uprawnienia do odczytu/zapisu/usuwania dla zasobów magazynu obiektów BLOB.
- [Czytnik danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): służy do przyznawania uprawnień tylko do odczytu zasobom magazynu obiektów BLOB.
- [Współautor danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Użyj, aby przyznać uprawnienia do odczytu/zapisu/usuwania do kolejek platformy Azure.
- [Czytnik danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): służy do przyznawania uprawnień tylko do odczytu dla kolejek platformy Azure.
- [Procesor komunikatów danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): służy do przyznawania uprawnień wglądu, pobierania i usuwania do komunikatów w kolejkach usługi Azure Storage.
- [Nadawca komunikatu o danych kolejki magazynu](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): służy do przyznawania uprawnień do dodawania do komunikatów w kolejkach usługi Azure Storage.

Aby uzyskać szczegółowe informacje na temat wbudowanych ról RBAC dla usługi Azure Storage dla usług danych i usługi zarządzania, zobacz sekcję dotyczącą **magazynu** w [rolach wbudowanych platformy Azure dla funkcji RBAC systemu](../articles/role-based-access-control/built-in-roles.md#storage)Azure. Ponadto, aby uzyskać informacje o różnych typach ról, które udostępniają uprawnienia na platformie Azure, zobacz [role administratora subskrypcji klasycznej, role RBAC i role usługi Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!NOTE]
> Propagowanie przypisań ról RBAC może potrwać do 5 minut.
>
> Tylko role jawnie zdefiniowane na potrzeby dostępu do danych zezwalają podmiotowi zabezpieczeń na dostęp do danych obiektu BLOB lub kolejki. Role, takie jak **właściciel**, **współautor**i **współautor konta magazynu** , umożliwiają podmiotowi zabezpieczeń Zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych obiektu BLOB lub kolejki w ramach tego konta.
>
> Dostęp do danych obiektu BLOB lub kolejki w Azure Portal można autoryzować przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Aby uzyskać więcej informacji, zobacz [używanie Azure Portal do uzyskiwania dostępu do danych obiektu BLOB lub kolejki](../articles/storage/common/storage-access-blobs-queues-portal.md).
