---
title: Autoryzowanie operacji na danych
titleSuffix: Azure Storage
description: Dowiedz się więcej o różnych sposobach autoryzacji dostępu do usługi Azure Storage, w tym Azure Active Directory, udostępnionej autoryzacji klucza lub sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1db26e6e1308299325e15b4c856fc2ebbaed8326
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807608"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autoryzowanie dostępu do danych w usłudze Azure Storage

Za każdym razem, gdy uzyskujesz dostęp do danych na koncie magazynu, klient wysyła żądanie za pośrednictwem protokołu HTTP/HTTPS do usługi Azure Storage. Każde żądanie wysyłane do bezpiecznego zasobu musi być autoryzowane w celu zagwarantowania, że klient ma uprawnienia wymagane do uzyskania dostępu do danych.

W poniższej tabeli opisano opcje oferowane przez usługę Azure Storage do autoryzowania dostępu do zasobów:

|  |Klucz współużytkowany (klucz konta magazynu)  |Sygnatura dostępu współdzielonego (SAS)  |Azure Active Directory (Azure AD)  |Active Directory Domain Services lokalnego (wersja zapoznawcza) |Anonimowy publiczny dostęp do odczytu  |
|---------|---------|---------|---------|---------|---------|
|Obiekty blob platformy Azure     |[Obsługiwał](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwał](storage-sas-overview.md)         |[Obsługiwał](storage-auth-aad.md)         |Nieobsługiwane|[Obsługiwał](../blobs/storage-manage-access-to-resources.md)         |
|Azure Files (SMB)     |[Obsługiwał](/rest/api/storageservices/authorize-with-shared-key/)         |Nieobsługiwane         |[Obsługiwane tylko w przypadku usług domenowych w usłudze AAD](../files/storage-files-active-directory-overview.md)         |[Obsługiwane, należy synchronizować poświadczenia z usługą Azure AD](../files/storage-files-active-directory-overview.md)|Nieobsługiwane         |
|Azure Files (REST)     |[Obsługiwał](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwał](storage-sas-overview.md)         |Nieobsługiwane         |Nieobsługiwane |Nieobsługiwane         |
|Azure Queues     |[Obsługiwał](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwał](storage-sas-overview.md)         |[Obsługiwał](storage-auth-aad.md)         |Nieobsługiwane | Nieobsługiwane         |
|Tabele platformy Azure     |[Obsługiwał](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwał](storage-sas-overview.md)         |Nieobsługiwane         |Nieobsługiwane| Nieobsługiwane         |

Każdą opcję autoryzacji można krótko opisać poniżej:

- **Azure Active Directory (Azure AD) integracja** obiektów blob i kolejek. Usługa Azure AD zapewnia kontrolę dostępu opartą na rolach (RBAC) na potrzeby kontroli dostępu klienta do zasobów na koncie magazynu. Aby uzyskać więcej informacji na temat integracji usługi Azure AD dla obiektów blob i kolejek, zobacz temat [autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).

- **Azure Active Directory Domain Services (Azure AD DS) uwierzytelnianie** dla Azure Files. Azure Files obsługuje autoryzację opartą na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem usługi Azure AD DS. Można użyć RBAC do szczegółowej kontroli nad dostępem klienta do Azure Files zasobów na koncie magazynu. Więcej informacji dotyczących uwierzytelniania Azure Files przy użyciu usług domenowych można znaleźć w temacie [Omówienie](../files/storage-files-active-directory-overview.md).

- **Active Directory Domain Services lokalnego (AD DS lub lokalnego AD DS) uwierzytelniania (wersja zapoznawcza)** dla Azure Files. Azure Files obsługuje autoryzację opartą na tożsamościach za pośrednictwem protokołu SMB za pośrednictwem AD DS. Środowisko AD DS może być hostowane na maszynach lokalnych lub na maszynach wirtualnych platformy Azure. Dostęp do plików SMB jest obsługiwany przy użyciu poświadczeń AD DS z komputerów przyłączonych do domeny, lokalnie lub na platformie Azure. Można użyć kombinacji RBAC na potrzeby kontroli dostępu na poziomie udziału i list DACL systemu plików NTFS dla wymuszania uprawnień na poziomie katalogu/pliku. Więcej informacji dotyczących uwierzytelniania Azure Files przy użyciu usług domenowych można znaleźć w temacie [Omówienie](../files/storage-files-active-directory-overview.md).

- **Autoryzacja klucza współużytkowanego** dla obiektów blob, plików, kolejek i tabel. Klient korzystający z klucza współużytkowanego przekazuje nagłówek z każdym żądaniem podpisanym przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key/).
- **Sygnatury dostępu współdzielonego** dla obiektów blob, plików, kolejek i tabel. Sygnatury dostępu współdzielonego (SAS) zapewniają ograniczony dostęp delegowany do zasobów na koncie magazynu. Dodanie ograniczeń w przedziale czasowym, dla którego podpis jest prawidłowy lub na uprawnienia, które ma w ten sposób zapewnia elastyczność zarządzania dostępem. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
- **Anonimowy publiczny dostęp do odczytu** dla kontenerów i obiektów BLOB. Autoryzacja nie jest wymagana. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym do odczytu do kontenerów i obiektów BLOB](../blobs/storage-manage-access-to-resources.md).  

Domyślnie wszystkie zasoby w usłudze Azure Storage są zabezpieczone i są dostępne tylko dla właściciela konta. Chociaż można użyć dowolnej z tych strategii autoryzacji w celu udzielenia klientom dostępu do zasobów na koncie magazynu, firma Microsoft zaleca korzystanie z usługi Azure AD, gdy jest to możliwe, aby uzyskać maksymalne zabezpieczenia i łatwość użycia.

## <a name="next-steps"></a>Następne kroki

- [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md)
- [Autoryzacja przy użyciu klucza wspólnego](/rest/api/storageservices/authorize-with-shared-key/)
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
