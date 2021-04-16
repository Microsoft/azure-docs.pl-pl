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
ms.openlocfilehash: 18f64defbc4222b46d858cb9cfd3e9b56866a4d0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513497"
---
Platforma Azure udostępnia następujące wbudowane role platformy Azure do autoryzowania dostępu do danych obiektów blob i kolejek przy użyciu usług Azure AD i OAuth:

- [Właściciel danych obiektu blob usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Służy do określania własności i zarządzania kontrolą dostępu POSIX w usłudze Azure Data Lake Storage Gen2. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Współautor danych obiektu blob usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Służy do przyznawania uprawnień do odczytu/zapisu/usuwania do zasobów usługi Blob Storage.
- [Czytelnik danych obiektu blob usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Służy do przyznawania uprawnień tylko do odczytu do zasobów usługi Blob Storage.
- [Delegator obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Pobierz klucz delegowania użytkownika, który będzie używany do utworzenia sygnatury dostępu współdzielonego podpisanej przy użyciu poświadczeń usługi Azure AD dla kontenera lub obiektu blob.
- [Współautor danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Służy do przyznawania uprawnień do odczytu/zapisu/usuwania do kolejek platformy Azure.
- [Czytelnik danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Służy do przyznawania uprawnień tylko do odczytu do kolejek platformy Azure.
- [Procesor komunikatów danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Służy do przyznawania uprawnień do wglądu, pobierania i usuwania do komunikatów w kolejkach usługi Azure Storage.
- [Nadawca komunikatów danych kolejki usługi Storage](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Służy do przyznawania uprawnień do dodawania komunikatów w kolejkach usługi Azure Storage.

Tylko role jawnie zdefiniowane dla dostępu do danych zezwalają podmiotowi zabezpieczeń na dostęp do danych obiektów blob lub kolejek. Role wbudowane, takie jak **Właściciel,** Współautor **i** Współautor konta magazynu, zezwalają podmiotowi zabezpieczeń na zarządzanie kontem magazynu, ale nie zapewniają dostępu do danych obiektu blob lub kolejki w ramach tego konta za pośrednictwem usługi Azure AD.  Jeśli jednak rola obejmuje element **Microsoft.Storage/storageAccounts/listKeys/action,** to użytkownik, do którego przypisano tę rolę, może uzyskać dostęp do danych na koncie magazynu za pośrednictwem autoryzacji kluczem współdzieloną przy użyciu kluczy dostępu do konta. Aby uzyskać więcej informacji, zobacz [Używanie Azure Portal do uzyskiwania dostępu do danych obiektów blob lub kolejek.](../articles/storage/blobs/authorize-data-operations-portal.md)

Aby uzyskać szczegółowe informacje na temat wbudowanych ról platformy Azure dla usługi Azure Storage dla usług danych i usługi zarządzania, zobacz sekcję **Magazyn** we wbudowanych rolach platformy Azure dla kontroli [dostępu na poziomie roli platformy Azure.](../articles/role-based-access-control/built-in-roles.md#storage) Aby uzyskać informacje o różnych typach ról, które zapewniają uprawnienia na platformie Azure, zobacz Role klasycznego administratora subskrypcji, role platformy [Azure i role usługi Azure AD.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)

> [!IMPORTANT]
> Propagacja przypisań ról platformy Azure może potrwać do 30 minut.
