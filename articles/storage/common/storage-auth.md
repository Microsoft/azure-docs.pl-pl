---
title: Autoryzowanie operacji na danych
titleSuffix: Azure Storage
description: Dowiedz się więcej o różnych sposobach autoryzowania dostępu do usługi Azure Storage, w tym usługi Azure Active Directory, autoryzacji klucza udostępnionego lub sygnatur dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8db6140789a9e4f46e368b913cacacd21609ccd9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529726"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autoryzowanie dostępu do danych w usłudze Azure Storage

Za każdym razem, gdy uzyskujesz dostęp do danych na koncie magazynu, klient żąda za pośrednictwem protokołu HTTP/HTTPS do usługi Azure Storage. Każde żądanie wysyłane do bezpiecznego zasobu musi być autoryzowane w celu zagwarantowania, że klient ma uprawnienia wymagane do uzyskania dostępu do danych.

W poniższej tabeli opisano opcje, które usługa Azure Storage oferuje w celu autoryzowania dostępu do zasobów:

|  |Klucz udostępniony (klucz konta magazynu)  |Sygnatura dostępu współdzielonego (SAS)  |Azure Active Directory (Azure AD)  |Lokalne Usługi domenowe Active Directory (wersja zapoznawcza) |Anonimowy publiczny dostęp do odczytu  |
|---------|---------|---------|---------|---------|---------|
|Obiekty blob platformy Azure     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |[Obsługiwane](storage-auth-aad.md)         |Nieobsługiwane|[Obsługiwane](../blobs/storage-manage-access-to-resources.md)         |
|Pliki platformy Azure (SMB)     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |Nieobsługiwane         |[Obsługiwane tylko w usługach domenowych AAD](../files/storage-files-active-directory-overview.md)         |[Obsługiwane poświadczenia muszą być synchronizowane z usługą Azure AD](../files/storage-files-active-directory-overview.md)|Nieobsługiwane         |
|Pliki platformy Azure (REST)     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |Nieobsługiwane         |Nieobsługiwane |Nieobsługiwane         |
|Azure Queues     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |[Obsługiwane](storage-auth-aad.md)         |Nieobsługiwane | Nieobsługiwane         |
|Tabele platformy Azure     |[Obsługiwane](/rest/api/storageservices/authorize-with-shared-key/)         |[Obsługiwane](storage-sas-overview.md)         |Nieobsługiwane         |Nieobsługiwane| Nieobsługiwane         |

Każda opcja autoryzacji jest krótko opisana poniżej:

- **Integracja usługi Azure Active Directory (Azure AD)** dla obiektów blob i kolejek. Usługa Azure AD zapewnia kontrolę dostępu opartą na rolach (RBAC) do kontroli nad dostępem klienta do zasobów na koncie magazynu. Aby uzyskać więcej informacji dotyczących integracji usług Azure AD dla obiektów blob i kolejek, zobacz [Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory.](storage-auth-aad.md)

- **Uwierzytelnianie usługi domenowe Usługi active directory (Azure AD DS)** dla usług Azure Files. Usługa Azure Files obsługuje autoryzację opartą na tożsamości za pośrednictwem bloku komunikatów serwera (SMB) za pośrednictwem usługi Azure AD DS. Funkcji RBAC można używać do precyzyjnej kontroli nad dostępem klienta do zasobów usługi Azure Files na koncie magazynu. Aby uzyskać więcej informacji dotyczących uwierzytelniania plików Platformy Azure przy użyciu usług domenowych, zapoznaj się z [omówieniem](../files/storage-files-active-directory-overview.md).

- **Lokalne uwierzytelnianie (w wersji zapoznawczej) usług domenowych Active Directory (AD DS) (wersja zapoznawcza)** dla usługi Azure Files. Usługa Azure Files obsługuje autoryzację opartą na tożsamościach za pośrednictwem SMB za pośrednictwem usług AD DS. Środowisko usług AD DS może być hostowane na komputerach lokalnych lub na maszynach wirtualnych platformy Azure. Dostęp SMB do plików jest obsługiwany przy użyciu poświadczeń usług AD DS z komputerów przyłączonych do domeny, lokalnie lub na platformie Azure. Można użyć kombinacji RBAC do kontroli dostępu na poziomie udziału i DACL NTFS dla wymuszania uprawnień katalogu/pliku. Aby uzyskać więcej informacji dotyczących uwierzytelniania plików Platformy Azure przy użyciu usług domenowych, zapoznaj się z [omówieniem](../files/storage-files-active-directory-overview.md).

- **Autoryzacja klucza udostępnionego** dla obiektów blob, plików, kolejek i tabel. Klient używający klucza udostępnionego przekazuje nagłówek z każdym żądaniem podpisanym przy użyciu klucza dostępu do konta magazynu. Aby uzyskać więcej informacji, zobacz [Autoryzowanie za pomocą klucza udostępnionego](/rest/api/storageservices/authorize-with-shared-key/).
- **Sygnatury dostępu współdzielonego** dla obiektów blob, plików, kolejek i tabel. Sygnatury dostępu współdzielonego zapewniają ograniczony delegowany dostęp do zasobów na koncie magazynu. Dodawanie ograniczeń dotyczących przedziału czasu, dla którego podpis jest prawidłowy lub uprawnień, które przyznaje, zapewnia elastyczność w zarządzaniu dostępem. Aby uzyskać więcej informacji, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
- **Anonimowy publiczny dostęp do odczytu** kontenerów i obiektów blob. Autoryzacja nie jest wymagana. Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowym dostępem do odczytu kontenerów i obiektów blob.](../blobs/storage-manage-access-to-resources.md)  

Domyślnie wszystkie zasoby w usłudze Azure Storage są zabezpieczone i są dostępne tylko dla właściciela konta. Chociaż można użyć dowolnej strategii autoryzacji opisanych powyżej, aby udzielić klientom dostępu do zasobów na koncie magazynu, firma Microsoft zaleca korzystanie z usługi Azure AD, jeśli jest to możliwe, aby zapewnić maksymalne bezpieczeństwo i łatwość użycia.

## <a name="next-steps"></a>Następne kroki

- [Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](storage-auth-aad.md)
- [Autoryzacja przy użyciu klucza wspólnego](/rest/api/storageservices/authorize-with-shared-key/)
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
