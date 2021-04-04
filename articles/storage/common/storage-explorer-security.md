---
title: Przewodnik po zabezpieczeniach Eksplorator usługi Azure Storage | Microsoft Docs
description: Wskazówki dotyczące zabezpieczeń Eksplorator usługi Azure Storage
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783763"
---
# <a name="azure-storage-explorer-security-guide"></a>Przewodnik po zabezpieczeniach Eksplorator usługi Azure Storage

Eksplorator usługi Microsoft Azure Storage umożliwia bezpieczne i bezpieczne korzystanie z danych usługi Azure Storage w systemach Windows, macOS i Linux. Postępując zgodnie z tymi wskazówkami, można zagwarantować, że dane pozostają chronione.

## <a name="general"></a>Ogólne

- **Zawsze używaj najnowszej wersji Eksplorator usługi Storage.** Wersje Eksplorator usługi Storage mogą zawierać aktualizacje zabezpieczeń. Zachowanie aktualności zapewnia ogólne zabezpieczenia.
- **Połącz się tylko z zaufanymi zasobami.** Dane pobierane z niezaufanych źródeł mogą być złośliwe i przekazywanie danych do niezaufanego źródła może spowodować utratę lub kradzieży danych.
- **Używaj protokołu HTTPS wszędzie tam, gdzie to możliwe.** Eksplorator usługi Storage domyślnie używa protokołu HTTPS. Niektóre scenariusze umożliwiają użycie protokołu HTTP, ale protokół HTTP powinien być używany tylko jako ostatni.
- **Upewnij się, że tylko wymagane uprawnienia są udzielane osobom, które ich potrzebują.** Unikaj nadmiernego ograniczania dostępu do zasobów użytkownika.
- **Podczas wykonywania krytycznych operacji należy zachować ostrożność.** Niektóre operacje, takie jak usuwanie i zastępowanie, są nieodwracalne i mogą spowodować utratę danych. Przed wykonaniem tych operacji upewnij się, że pracujesz z prawidłowymi zasobami.

## <a name="choosing-the-right-authentication-method"></a>Wybieranie właściwej metody uwierzytelniania

Eksplorator usługi Storage zapewnia różne sposoby uzyskiwania dostępu do zasobów usługi Azure Storage. Niezależnie od wybranej metody, Oto nasze zalecenia.

### <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

Najłatwiejszym i najbardziej bezpiecznym sposobem uzyskiwania dostępu do zasobów usługi Azure Storage jest zalogowanie się przy użyciu konta platformy Azure. Logowanie przy użyciu uwierzytelniania usługi Azure AD pozwala:

- Udziel dostępu określonym użytkownikom i grupom.
- Odwołaj dostęp do określonych użytkowników i grup w dowolnym momencie.
- Wymuś warunki dostępu, takie jak wymaganie uwierzytelniania wieloskładnikowego.

Jeśli to możliwe, zalecamy używanie uwierzytelniania usługi Azure AD.

W tej sekcji opisano dwie technologie oparte na usłudze Azure AD, które mogą być używane do zabezpieczania zasobów magazynu.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) zapewnia precyzyjną kontrolę dostępu do zasobów platformy Azure. Rolami i uprawnieniami platformy Azure można zarządzać z poziomu Azure Portal.

Eksplorator usługi Storage obsługuje dostęp do usługi Azure RBAC do kont magazynu, obiektów blob i kolejek. Jeśli potrzebujesz dostępu do udziałów plików lub tabel, musisz przypisać role platformy Azure, które przyznają uprawnienia do wyświetlania kluczy konta magazynu.

#### <a name="access-control-lists-acls"></a>Listy kontroli dostępu (ACL)

[Listy kontroli dostępu (ACL)](../blobs/data-lake-storage-access-control.md) umożliwiają kontrolowanie dostępu na poziomie plików i folderów w kontenerach obiektów BLOB ADLS Gen2. Listami ACL można zarządzać przy użyciu Eksplorator usługi Storage.

### <a name="shared-access-signatures-sas"></a>Sygnatury dostępu współdzielonego (SAS)

Jeśli nie możesz użyć uwierzytelniania usługi Azure AD, zalecamy używanie sygnatur dostępu współdzielonego. Sygnatury dostępu współdzielonego umożliwiają:

- Zapewnianie anonimowego dostępu do zabezpieczonych zasobów.
- Natychmiast odwołaj SYGNATURę dostępu współdzielonego, jeśli zostanie wygenerowane na podstawie współużytkowanych zasad (SAP).

Jednak za pomocą sygnatur dostępu współdzielonego nie można:

- Ogranicz, kto może korzystać z sygnatury dostępu współdzielonego. Prawidłowym sygnaturą dostępu współdzielonego może używać każda osoba, która ją ma.
- Odwołaj SYGNATURę dostępu współdzielonego, jeśli nie zostanie wygenerowane na podstawie zasad współużytkowania (SAP).

W przypadku korzystania z sygnatury dostępu współdzielonego w Eksplorator usługi Storage zalecamy następujące wytyczne:

- **Ogranicz dystrybucję tokenów SAS i identyfikatorów URI.** Rozpowszechniać tokeny SAS i identyfikatory URI tylko dla zaufanych osób. Ograniczenie dystrybucji SYGNATURy dostępu współdzielonego zmniejsza szansę nieużycia sygnatury dostępu współdzielonego.
- **Używaj tokenów SAS i identyfikatorów URI tylko z zaufanych jednostek.**
- **Jeśli jest to możliwe, należy używać zasad dostępu współdzielonego (SAP).** SYGNATURa dostępu współdzielonego oparta na zasadach współdziałania jest bezpieczniejsza niż w przypadku systemu operacyjnego, ponieważ skojarzenia zabezpieczeń można odwołać, usuwając oprogramowanie SAP.
- **Generuj tokeny z minimalnym dostępem do zasobów i uprawnieniami.** Minimalne uprawnienia ograniczają potencjalną szkodę, którą można wykonać, jeśli sygnatura dostępu współdzielonego nie zostanie użyta.
- **Generuj tokeny, które są prawidłowe tylko w razie potrzeby.** Krótki cykl życia jest szczególnie istotny dla systemu operacyjnego, ponieważ nie ma możliwości odwołania ich po wygenerowaniu.

> [!IMPORTANT]
> Podczas udostępniania tokenów sygnatury dostępu współdzielonego i identyfikatorów URI w celu rozwiązywania problemów, takich jak w żądaniach obsługi lub raportach o błędach, należy zawsze Zredaguj co najmniej część sygnatury SAS.

### <a name="storage-account-keys"></a>Klucze kont magazynu

Klucze konta magazynu udzielają nieograniczonego dostępu do usług i zasobów w ramach konta magazynu. Z tego powodu zaleca się ograniczenie użycia kluczy w celu uzyskania dostępu do zasobów w Eksplorator usługi Storage. Aby zapewnić dostęp, użyj funkcji RBAC lub SAS platformy Azure.

Niektóre role platformy Azure przyznają uprawnienia do pobierania kluczy konta magazynu. Osoby mające te role mogą efektywnie obejść uprawnienia udzielone lub odrzucone przez kontrolę RBAC platformy Azure. Nie zaleca się udzielania tego uprawnienia, chyba że jest to konieczne.

Eksplorator usługi Storage podejmie próbę użycia kluczy konta magazynu, jeśli są dostępne, do uwierzytelniania żądań. Tę funkcję można wyłączyć w obszarze Ustawienia (**usługi > konta magazynu > wyłączyć użycie kluczy**). Niektóre funkcje nie obsługują kontroli RBAC platformy Azure, na przykład podczas pracy z klasycznymi kontami magazynu. Takie funkcje nadal wymagają kluczy i nie wpływają na to ustawienie.

Aby uzyskać dostęp do zasobów magazynu, należy użyć kluczy, zalecamy następujące wytyczne:

- **Nie udostępniaj nikomu kluczy konta.**
- **Traktuj klucze konta magazynu, takie jak hasła.** Jeśli musisz udostępnić klucze, użyj bezpiecznych rozwiązań magazynu, takich jak [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Jeśli uważasz, że klucz konta magazynu został udostępniony lub rozdystrybuowany przez pomyłkę, możesz generować nowe klucze dla konta magazynu z Azure Portal.

### <a name="public-access-to-blob-containers"></a>Publiczny dostęp do kontenerów obiektów BLOB

Eksplorator usługi Storage umożliwia modyfikowanie poziomu dostępu do kontenerów Blob Storage platformy Azure. Nieprywatne kontenery obiektów BLOB umożliwiają każdy anonimowy dostęp do odczytu do danych w tych kontenerach.

Podczas włączania dostępu publicznego do kontenera obiektów BLOB zalecamy następujące wytyczne:

- **Nie należy włączać publicznego dostępu do kontenera obiektów blob, który może zawierać potencjalnie poufne dane.** Upewnij się, że kontener obiektów BLOB jest bezpłatny dla wszystkich prywatnych danych.
- **Nie przekazuj potencjalnie poufnych danych do kontenera obiektów blob z dostępem do obiektów blob lub kontenerów.** 

## <a name="next-steps"></a>Następne kroki

- [Zalecenia dotyczące zabezpieczeń](../blobs/security-recommendations.md)