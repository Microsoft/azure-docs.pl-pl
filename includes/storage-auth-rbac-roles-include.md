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
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99214375"
---
Platforma Azure udostępnia następujące wbudowane role platformy Azure do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- [Właściciel danych obiektu blob usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Służy do określania własności i zarządzania kontrolą dostępu POSIX w usłudze Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu blob usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Służy do przyznawania uprawnień do odczytu/zapisu/usuwania do zasobów usługi Blob Storage.
- [Czytelnik danych obiektu blob usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Służy do przyznawania uprawnień tylko do odczytu do zasobów usługi Blob Storage.
- [Delegator obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Pobierz klucz delegowania użytkownika, który będzie używany do utworzenia sygnatury dostępu współdzielonego podpisanej przy użyciu poświadczeń usługi Azure AD dla kontenera lub obiektu blob.
- [Współautor danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Służy do przyznawania uprawnień do odczytu/zapisu/usuwania do kolejek platformy Azure.
- [Czytelnik danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Służy do przyznawania uprawnień tylko do odczytu do kolejek platformy Azure.
- [Procesor komunikatów danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Służy do przyznawania uprawnień do wglądu, pobierania i usuwania do komunikatów w kolejkach usługi Azure Storage.
- [Nadawca komunikatów danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Służy do przyznawania uprawnień do dodawania komunikatów w kolejkach usługi Azure Storage.

Tylko role jawnie zdefiniowane na potrzeby dostępu do danych zezwalają podmiotowi zabezpieczeń na dostęp do danych obiektu BLOB lub kolejki. Role wbudowane, takie jak **właściciel**, **współautor** i **współautor konta magazynu** , umożliwiają podmiotowi zabezpieczeń Zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych obiektu BLOB lub kolejki w ramach tego konta za pośrednictwem usługi Azure AD. Jeśli jednak rola obejmuje element **Microsoft. Storage/storageAccounts/listKeys/Action**, użytkownik, do którego przypisano tę rolę, może uzyskać dostęp do danych na koncie magazynu za pośrednictwem autoryzacji klucza współużytkowanego przy użyciu kluczy dostępu do konta. Aby uzyskać więcej informacji, zobacz [używanie Azure Portal do uzyskiwania dostępu do danych obiektu BLOB lub kolejki](../articles/storage/blobs/authorize-data-operations-portal.md).

Aby uzyskać szczegółowe informacje na temat wbudowanych ról platformy Azure dla usługi Azure Storage dla usług danych i usługi zarządzania, zobacz sekcję dotyczącą **magazynu** w [rolach wbudowanych platformy Azure dla funkcji RBAC systemu Azure](../articles/role-based-access-control/built-in-roles.md#storage). Ponadto aby uzyskać informacje na temat różnych typów ról, które zapewniają uprawnienia na platformie Azure, zobacz [role administratora subskrypcji klasycznej, role platformy Azure i role usługi Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Propagowanie przypisań ról platformy Azure może potrwać do 30 minut.
