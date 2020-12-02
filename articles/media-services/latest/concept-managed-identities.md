---
title: Zarządzane tożsamości i zaufane magazyny za pomocą usługi Media Services
description: Media Services można używać z tożsamościami zarządzanymi w celu włączenia magazynu zaufanego.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: d0811e8f9183ee334d413bcad69f2c7b32023be3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499360"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Zarządzane tożsamości i zaufane magazyny za pomocą usługi Media Services

Media Services można używać z [tożsamościami zarządzanymi](../../active-directory/managed-identities-azure-resources/overview.md) w celu włączenia magazynu zaufanego. Podczas tworzenia konta Media Services należy je skojarzyć z kontem magazynu. Media Services może uzyskać dostęp do tego konta magazynu przy użyciu uwierzytelniania systemu. Media Services sprawdza, czy konto Media Services i konto magazynu znajdują się w tej samej subskrypcji i sprawdza, czy użytkownik, który doda skojarzenie, uzyskuje dostęp do konta magazynu za pomocą Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Magazyn zaufany

Jeśli jednak chcesz użyć zapory do zabezpieczenia konta magazynu, musisz użyć uwierzytelniania tożsamości zarządzanej. Umożliwia Media Services dostępu do konta magazynu, które zostało skonfigurowane przy użyciu zapory lub ograniczenia sieci wirtualnej za pomocą zaufanego dostępu do magazynu.  Aby uzyskać więcej informacji na temat zaufanych usług firmy Microsoft, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="media-services-managed-identity-scenarios"></a>Scenariusze tożsamości zarządzanych usług Media Services

Obecnie istnieją dwa scenariusze, w których tożsamość zarządzana może być używana z Media Services:

- Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do kont magazynu.

- Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do Key Vault do uzyskiwania dostępu do kluczy klienta.

W następnych dwóch sekcjach opisano różnice w dwóch scenariuszach.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do kont magazynu

1. Utwórz konto Media Services przy użyciu tożsamości zarządzanej.
1. Udziel zarządzanej tożsamości głównej dostępu do konta magazynu, którego jesteś posiadasz.
1. Media Services może następnie uzyskać dostęp do konta magazynu w Twoim imieniu przy użyciu tożsamości zarządzanej.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Użyj zarządzanej tożsamości konta Media Services, aby uzyskać dostęp do Key Vault do uzyskiwania dostępu do kluczy klienta

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