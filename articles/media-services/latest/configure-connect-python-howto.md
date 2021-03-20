---
title: Nawiązywanie połączenia z interfejsem API programu Azure Media Services v3 — Python
description: W tym artykule pokazano, jak nawiązać połączenie z interfejsem API programu Media Services v3 przy użyciu języka Python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 76df8baaf170b05762b93478a496eb1e9ed802d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94916750"
---
# <a name="connect-to-media-services-v3-api---python"></a>Nawiązywanie połączenia z interfejsem API programu Media Services v3 — Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule opisano sposób nawiązywania połączenia z zestawem SDK języka Python Azure Media Services v3 przy użyciu nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz Język Python z [Python.org](https://www.python.org/downloads/)
- Upewnij się, że ustawienie `PATH` zmienna środowiskowa
- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.
- Wykonaj kroki opisane w temacie [interfejsy API dostępu](./access-api-howto.md) . Zapisz identyfikator subskrypcji, identyfikator aplikacji (identyfikator klienta), klucz uwierzytelniania (klucz tajny) i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalacja modułów

Aby można było korzystać z Azure Media Services przy użyciu języka Python, należy zainstalować te moduły.

* `azure-mgmt-resource`Moduł, który obejmuje moduły platformy Azure dla Active Directory.
* `azure-mgmt-media`Moduł, który obejmuje jednostki Media Services.

    Upewnij się, że zapoznaj się z [najnowszą wersją zestawu SDK Media Services dla języka Python](https://pypi.org/project/azure-mgmt-media/).

Otwórz narzędzie wiersza polecenia i użyj następujących poleceń, aby zainstalować moduły.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Nawiązywanie połączenia z klientem języka Python

1. Utwórz plik z `.py` rozszerzeniem
1. Otwórz plik w ulubionym edytorze
1. Dodaj kod, który następuje po pliku. Kod importuje wymagane moduły i tworzy obiekt Active Directory poświadczeń, który należy połączyć z Media Services.

      Ustaw wartości zmiennych na wartości, które pochodzą z [interfejsów API dostępu](./access-api-howto.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Uruchom plik

## <a name="next-steps"></a>Następne kroki

- Użyj [zestawu SDK języka Python](https://aka.ms/ams-v3-python-sdk).
- Przejrzyj [dokumentację dla języka Python](/python/api/overview/azure/mediaservices/management) usługi Media Services.
