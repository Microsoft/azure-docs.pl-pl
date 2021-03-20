---
title: Zabezpieczanie aplikacji PaaS przy użyciu usługi Azure Storage | Microsoft Docs
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń usługi Azure Storage na potrzeby zabezpieczania aplikacji internetowych i mobilnych PaaS.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093976"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Najlepsze rozwiązania dotyczące zabezpieczania aplikacji PaaS Web i Mobile przy użyciu usługi Azure Storage
W tym artykule omówiono zbiór najlepszych rozwiązań z zakresu bezpieczeństwa usługi Azure Storage na potrzeby zabezpieczania aplikacji internetowych i mobilnych platformy jako usługi (PaaS). Te najlepsze rozwiązania wynikają z naszych rozwiązań związanych z platformą Azure i klientami.

Platforma Azure umożliwia wdrażanie i Używanie magazynu w sposób niełatwo osiągalny w środowisku lokalnym. Usługa Azure Storage pozwala uzyskać wysoką skalowalność i dostępność przy stosunkowo małym wysiłku. Usługa Azure Storage jest nietylko podstawą dla systemu Windows i Linux Azure Virtual Machines, ale może również obsługiwać duże aplikacje rozproszone.

Usługa Azure Storage udostępnia następujące cztery usługi: BLOB Storage, Table Storage, queue storage i File Storage. Aby dowiedzieć się więcej, zobacz [wprowadzenie do Microsoft Azure Storage](../../storage/common/storage-introduction.md).

[Przewodnik po zabezpieczeniach usługi Azure Storage](../../storage/blobs/security-recommendations.md) to doskonałe źródło dla szczegółowych informacji o usłudze Azure Storage i zabezpieczeniach. W artykule dotyczącym najlepszych rozwiązań znajdują się na wysokim poziomie niektóre koncepcje dotyczące zabezpieczeń oraz linki do przewodnika zabezpieczeń, a także inne źródła, aby uzyskać więcej informacji.

W tym artykule opisano następujące najlepsze rozwiązania:

- Sygnatury dostępu współdzielonego (SAS)
- Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)
- Szyfrowanie po stronie klienta dla danych o wysokiej wartości
- Szyfrowanie usługi Storage


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Używanie sygnatury dostępu współdzielonego zamiast klucza konta magazynu
Kontrola dostępu jest krytyczna. Aby ułatwić kontrolowanie dostępu do usługi Azure Storage, na platformie Azure generowane są 2 512-bitowe klucze kont magazynu (SAKs) podczas tworzenia konta magazynu. Poziom nadmiarowości klucza pozwala uniknąć przerw w działaniu usługi podczas rutynowego rotacji kluczy. 

Klucze dostępu do magazynu to wpisy tajne o wysokim priorytecie i powinny być dostępne tylko dla osób odpowiedzialnych za kontrolę dostępu do magazynu. Jeśli niewłaściwe osoby uzyskują dostęp do tych kluczy, będą mieć pełną kontrolę nad magazynem i mogą zastąpić, usunąć lub dodać pliki do magazynu. Obejmuje to złośliwe oprogramowanie i inne typy zawartości, które mogą potencjalnie naruszyć bezpieczeństwo organizacji lub klientów.

Nadal potrzebny jest sposób zapewnienia dostępu do obiektów w magazynie. Aby zapewnić bardziej szczegółowy dostęp, możesz skorzystać z sygnatury dostępu współdzielonego (SAS). Sygnatura dostępu współdzielonego umożliwia udostępnianie określonych obiektów w magazynie dla wstępnie zdefiniowanego interwału czasu i z określonymi uprawnieniami. Sygnatura dostępu współdzielonego umożliwia zdefiniowanie:

- Interwał, w jakim sygnatura dostępu współdzielonego jest prawidłowa, łącznie z czasem rozpoczęcia i czasem wygaśnięcia.
- Uprawnienia przyznane przez sygnaturę dostępu współdzielonego. Na przykład sygnatura dostępu współdzielonego w obiekcie blob może przyznać użytkownikowi uprawnienia do odczytu i zapisu do tego obiektu BLOB, ale nie usuwać uprawnień.
- Opcjonalny adres IP lub zakres adresów IP, z których usługa Azure Storage akceptuje sygnaturę dostępu współdzielonego. Można na przykład określić zakres adresów IP należących do organizacji. Zapewnia to kolejną miarę zabezpieczeń dla sygnatury dostępu współdzielonego.
- Protokół, w którym usługa Azure Storage akceptuje sygnaturę dostępu współdzielonego. Tego parametru opcjonalnego można użyć, aby ograniczyć dostęp do klientów przy użyciu protokołu HTTPS.

Sygnatura dostępu współdzielonego umożliwia udostępnianie zawartości w taki sposób, w jaki chcesz udostępnić ją bez podawania kluczy konta magazynu. Zawsze używanie sygnatury dostępu współdzielonego w aplikacji jest bezpiecznym sposobem udostępniania zasobów magazynu bez naruszania kluczy konta magazynu.

Aby dowiedzieć się więcej na temat sygnatury dostępu współdzielonego, zobacz [Używanie sygnatur dostępu współdzielonego](../../storage/common/storage-sas-overview.md). 

## <a name="use-azure-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach na platformie Azure
Innym sposobem zarządzania dostępem jest użycie [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Dzięki kontroli RBAC platformy Azure koncentruje się na umożliwieniu pracownikom dokładnych wymaganych uprawnień w oparciu o konieczność znajomości i najniższych zasad zabezpieczeń. Zbyt wiele uprawnień może uwidocznić konto w osobach atakujących. Zbyt mało uprawnień oznacza, że pracownicy nie mogą wydajnie pracować. Usługa Azure RBAC pomaga rozwiązać ten problem, oferując szczegółowe zarządzanie dostępem na platformie Azure. Jest to konieczne w przypadku organizacji, które chcą wymusić zasady zabezpieczeń na potrzeby dostępu do danych.

Do przypisywania uprawnień użytkownikom można używać wbudowanych ról platformy Azure na platformie Azure. Na przykład współautor konta magazynu dla operatorów chmury, które muszą zarządzać kontami magazynu i rolą współautor klasycznego konta magazynu, aby zarządzać kontami klasycznego magazynu. W przypadku operatorów chmury, które muszą zarządzać maszynami wirtualnymi, ale nie z siecią wirtualną lub kontem magazynu, z którym są połączone, można je dodać do roli współautor maszyny wirtualnej.

Organizacje, które nie wymuszają kontroli dostępu do danych za pomocą funkcji, takich jak Azure RBAC, mogą uzyskać więcej uprawnień niż jest to konieczne dla swoich użytkowników. Może to prowadzić do złamania danych przez umożliwienie niektórym użytkownikom dostępu do danych, które nie powinny znajdować się w pierwszym miejscu.

Aby dowiedzieć się więcej na temat usługi Azure RBAC, zobacz:

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Wbudowane role platformy Azure](../../role-based-access-control/built-in-roles.md)
- [Azure Storage security guide](../../storage/blobs/security-recommendations.md) (Przewodnik po zabezpieczeniach usługi Azure Storage) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Korzystanie z szyfrowania po stronie klienta dla danych o wysokiej wartości
Szyfrowanie po stronie klienta umożliwia programowe szyfrowanie danych podczas przesyłania przed przekazaniem ich do usługi Azure Storage oraz programowo odszyfrowywanie danych podczas ich pobierania. Zapewnia to szyfrowanie danych w trakcie przesyłania, ale również zapewnia szyfrowanie danych przechowywanych w spoczynku. Szyfrowanie po stronie klienta to najbezpieczniejsza Metoda szyfrowania danych, ale wymaga to wprowadzenia programistycznych zmian w aplikacji oraz umieszczenia procesów zarządzania kluczami.

Szyfrowanie po stronie klienta umożliwia również kontrolę nad kluczami szyfrowania. Można generować własne klucze szyfrowania i zarządzać nimi. Wykorzystuje technikę koperty, w której Biblioteka klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest następnie opakowany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być parę kluczy asymetrycznych lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w [Azure Key Vault](../../key-vault/general/overview.md).

Szyfrowanie po stronie klienta jest wbudowane w środowisko Java i biblioteki klienckie programu .NET Storage. Zapoznaj się z tematem [szyfrowanie po stronie klienta i Azure Key Vault dla Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md) , aby uzyskać informacje dotyczące szyfrowania danych w aplikacjach klienckich oraz generowania własnych kluczy szyfrowania i zarządzania nimi.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Włącz szyfrowanie usługi Storage dla danych przechowywanych w spoczynku
Po włączeniu [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md) dla usługi File Storage dane są szyfrowane automatycznie przy użyciu szyfrowania AES-256. Firma Microsoft obsługuje wszystkie szyfrowanie, odszyfrowywanie i zarządzanie kluczami. Ta funkcja jest dostępna dla typów nadmiarowości LRS i GRS.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono zbiór najlepszych rozwiązań dotyczących zabezpieczeń usługi Azure Storage na potrzeby zabezpieczania aplikacji internetowych i mobilnych programu PaaS. Aby dowiedzieć się więcej o zabezpieczaniu wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](paas-deployments.md)
- [Zabezpieczanie aplikacji PaaS Web i Mobile przy użyciu usługi Azure App Services](paas-applications-using-app-services.md)
- [Zabezpieczanie baz danych PaaS na platformie Azure](paas-applications-using-sql.md)