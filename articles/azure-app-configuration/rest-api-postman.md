---
title: Azure Active Directory interfejsu API REST — test przy użyciu programu Poster
description: Korzystanie z programu Poster do testowania interfejsu API REST konfiguracji aplikacji platformy Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932493"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Testowanie interfejsu API REST usługi Azure App Configuration przy użyciu programu Poster

Aby przetestować interfejs API REST przy użyciu programu [Poster](https://www.getpostman.com/), należy uwzględnić nagłówki HTTP wymagane do [uwierzytelniania](./rest-api-authentication-hmac.md) w żądaniach. Poniżej przedstawiono sposób konfigurowania programu Poster do testowania interfejsu API REST, co powoduje automatyczne wygenerowanie nagłówków uwierzytelniania:

1. Utwórz nowe [żądanie](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Dodaj `signRequest` funkcję z [przykładu uwierzytelnianie JavaScript](./rest-api-authentication-hmac.md#javascript) do [skryptu przed żądaniem](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) żądania
1. Dodaj następujący kod na końcu skryptu poprzedzającego żądanie. Zaktualizuj klucz dostępu zgodnie ze wskazaniem komentarza do wykonania

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Wysyłanie żądania
