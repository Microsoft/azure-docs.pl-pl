---
title: Zaufany magazyn dla Media Services
description: Uwierzytelnianie tożsamości zarządzanych pozwala Media Services uzyskać dostęp do konta magazynu skonfigurowanego za pomocą zapory lub ograniczenia sieci wirtualnej za pomocą zaufanego dostępu do magazynu.
keywords: zaufany magazyn, zarządzane tożsamości
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408518"
---
# <a name="trusted-storage-for-media-services"></a>Zaufany magazyn dla Media Services

Podczas tworzenia konta Media Services należy je skojarzyć z kontem magazynu. Media Services może uzyskać dostęp do tego konta magazynu przy użyciu uwierzytelniania systemu. Media Services sprawdza, czy konto Media Services i konto magazynu znajdują się w tej samej subskrypcji i sprawdza, czy użytkownik, który doda skojarzenie, uzyskuje dostęp do konta magazynu za pomocą Azure Resource Manager RBAC.

Jeśli jednak chcesz użyć zapory do zabezpieczenia konta magazynu i włączenia magazynu zaufanego, musisz użyć uwierzytelniania [tożsamości zarządzanych](concept-managed-identities.md) . Umożliwia Media Services dostępu do konta magazynu, które zostało skonfigurowane przy użyciu zapory lub ograniczenia sieci wirtualnej za pomocą zaufanego dostępu do magazynu.

Aby zrozumieć metody tworzenia zaufanego magazynu z tożsamościami zarządzanymi, Odczytaj [zarządzane tożsamości i Media Services](concept-managed-identities.md).

Aby uzyskać więcej informacji na temat kluczy zarządzanych przez klienta i Key Vault, zobacz temat [przenoszenie własnego klucza (kluczy zarządzanych przez klienta) przy użyciu Media Services](concept-use-customer-managed-keys-byok.md)

Aby uzyskać więcej informacji na temat zaufanych usług firmy Microsoft, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="tutorials"></a>Samouczki

Te samouczki obejmują oba scenariusze wymienione powyżej.

- [Użyj Azure Portal, aby użyć kluczy zarządzanych przez klienta lub BYOK z Media Services](tutorial-byok-portal.md)
- [Użyj kluczy zarządzanych przez klienta lub BYOK za pomocą interfejsu API REST Media Services](tutorial-byok-postman.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jakie zarządzane tożsamości można wykonać dla Ciebie i aplikacji platformy Azure, zobacz [tożsamość zarządzana usługi Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).