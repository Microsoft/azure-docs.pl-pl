---
title: Korzystanie z kluczy zarządzanych przez klienta lub portalu BYOK
description: W tym samouczku Użyj Azure Portal, aby włączyć klucze zarządzane przez klienta lub przenieść własny klucz (BYOK) przy użyciu konta magazynu Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: e34649162c7a30d4ab43aa068d2c864b5c2d90e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955703"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Samouczek: Użyj Azure Portal, aby użyć kluczy zarządzanych przez klienta lub BYOK z Media Services

Za pomocą interfejsu API 2020-05-01 można użyć klucza RSA zarządzanego przez klienta przy użyciu konta Azure Media Services, które ma tożsamość zarządzaną przez system. Ten samouczek obejmuje kroki opisane w Azure Portal.

Używane usługi to:

- Azure Storage
- Azure Key Vault
- Azure Media Services

W ramach tego samouczka nauczysz się używać Azure Portal do:

> [!div class="checklist"]
> - Utwórz grupę zasobów.
> - Utwórz konto magazynu z tożsamością zarządzaną przez system.
> - Utwórz konto Media Services z tożsamością zarządzaną przez system.
> - Utwórz magazyn kluczy do przechowywania klucza RSA zarządzanego przez klienta.

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Klucze zarządzane przez system

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> W przypadku następujących kroków tworzenia konta magazynu należy wybrać opcję klucz zarządzany przez system w obszarze Ustawienia zaawansowane.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> W przypadku następujących kroków szyfrowania magazynu należy wybrać opcję **klucz zarządzany przez klienta**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Zmień klucz

Media Services automatycznie wykrywa, kiedy klucz zostanie zmieniony. OPCJONALNE: Aby przetestować ten proces, należy utworzyć inną wersję klucza dla tego samego klucza. Media Services powinien wykryć, że klucz został zmieniony.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z utworzonych zasobów, nie *musisz już otrzymywać opłat*, usuń je.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Koduj plik zdalny na podstawie adresu URL i strumieniowego wideo przy użyciu interfejsu REST](stream-files-tutorial-with-rest.md)
