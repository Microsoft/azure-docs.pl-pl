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
ms.openlocfilehash: fd92eed127ec50a3d3a86f667d9aa764b79c190a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585397"
---
# <a name="trusted-storage-for-media-services"></a>Zaufany magazyn dla Media Services

Podczas tworzenia konta Media Services należy je skojarzyć z kontem magazynu. Media Services może uzyskać dostęp do tego konta magazynu przy użyciu uwierzytelniania systemu lub uwierzytelniania tożsamości zarządzanej. Media Services sprawdza, czy konto Media Services i konto magazynu znajdują się w tej samej subskrypcji i sprawdza, czy użytkownik, który doda skojarzenie, uzyskuje dostęp do konta magazynu za pomocą Azure Resource Manager RBAC.

>[!NOTE]
>Magazyn zaufany jest dostępny tylko w interfejsie API i nie jest obecnie włączony w Azure Portal.

## <a name="trusted-storage-with-a-firewall"></a>Zaufany magazyn z zaporą

Jeśli jednak chcesz użyć zapory do zabezpieczenia konta magazynu i włączenia magazynu zaufanego, preferowaną opcją jest uwierzytelnianie [tożsamości zarządzanych](concept-managed-identities.md) . Umożliwia Media Services dostępu do konta magazynu, które zostało skonfigurowane przy użyciu zapory lub ograniczenia sieci wirtualnej za pomocą zaufanego dostępu do magazynu.

## <a name="tutorial"></a>Samouczek

Możesz dowiedzieć się więcej na temat włączania zaufanego magazynu za pomocą samouczka [Media Services zaufanego magazynu](tutorial-trusted-storage-rest.md) .

> [!NOTE]
> Aby Media Services mieć możliwość odczytywania i zapisywania danych w ramach konta magazynu, należy przyznać funkcji dostępu współautora dla magazynu tożsamości zarządzanego przez usługi AMS.  Przyznanie roli współautor generycznemu nie będzie możliwe, ponieważ nie spowoduje to włączenia prawidłowych uprawnień do płaszczyzny danych.

## <a name="further-reading"></a>Dodatkowe informacje

Aby zrozumieć metody tworzenia zaufanego magazynu z tożsamościami zarządzanymi, Odczytaj [zarządzane tożsamości i Media Services](concept-managed-identities.md).

Aby uzyskać więcej informacji na temat zaufanych usług firmy Microsoft, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jakie zarządzane tożsamości można wykonać dla Ciebie i aplikacji platformy Azure, zobacz [tożsamość zarządzana usługi Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
