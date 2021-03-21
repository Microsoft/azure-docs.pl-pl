---
title: Tożsamości zarządzane
description: Media Services można używać z tożsamościami zarządzanymi przez platformę Azure.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258443"
---
# <a name="managed-identities"></a>Tożsamości zarządzane

Typowym wyzwaniem dla deweloperów jest zarządzanie kluczami tajnymi i poświadczeniami w celu zabezpieczenia komunikacji między różnymi usługami. Na platformie Azure tożsamości zarządzane eliminują konieczność zarządzania poświadczeniami przy użyciu tożsamości dla zasobów platformy Azure w usłudze Azure AD i uzyskiwania tokenów Azure Active Directory (Azure AD).

Obecnie istnieją dwa scenariusze, w których zarządzane tożsamości mogą być używane z Media Services:

- Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do kont magazynu.

- Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do Key Vault do uzyskiwania dostępu do kluczy klienta.

W dwóch następnych sekcjach opisano kroki dwóch scenariuszy.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do kont magazynu

1. Utwórz konto Media Services przy użyciu tożsamości zarządzanej.
1. Udziel zarządzanej tożsamości głównej dostępu do konta magazynu, którego jesteś posiadasz.
1. Media Services może następnie uzyskać dostęp do konta magazynu w Twoim imieniu przy użyciu tożsamości zarządzanej.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do Key Vault do uzyskiwania dostępu do kluczy klienta

1. Utwórz konto Media Services przy użyciu tożsamości zarządzanej.
1. Przyznaj podmiotowi zabezpieczeń tożsamości zarządzanej dostęp do Key Vault, którego jesteś członkiem.
1. Skonfiguruj konto Media Services tak, aby korzystało z szyfrowania konta opartego na kluczu klienta.
1. Media Services dostęp do Key Vault w Twoim imieniu przy użyciu tożsamości zarządzanej.

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta i Key Vault, zobacz temat [przenoszenie własnego klucza (kluczy zarządzanych przez klienta) przy użyciu Media Services](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Samouczki

Te samouczki obejmują oba scenariusze wymienione powyżej.

- [Użyj Azure Portal, aby użyć kluczy zarządzanych przez klienta lub BYOK z Media Services](tutorial-byok-portal.md)
- [Użyj kluczy zarządzanych przez klienta lub BYOK za pomocą interfejsu API REST Media Services](tutorial-byok-postman.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jakie zarządzane tożsamości można wykonać dla Ciebie i aplikacji platformy Azure, zobacz [tożsamość zarządzana usługi Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
