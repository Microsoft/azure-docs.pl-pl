---
title: Uwierzytelnianie w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat różnych sposobów uwierzytelniania aplikacji lub usługi w ramach usług komunikacyjnych.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110869"
---
# <a name="authenticate-to-azure-communication-services"></a>Uwierzytelnianie w usłudze Azure Communications Services

Każdy klient korzystający z usług Azure Communications Services musi zostać uwierzytelniony. W typowej architekturze zapoznaj się z tematem [Architektura klienta i serwera](./client-and-server-architecture.md), *klucze dostępu* lub *tożsamości zarządzane* są używane do uwierzytelniania.

Inny typ uwierzytelniania korzysta z *tokenów dostępu użytkowników* do uwierzytelniania względem usług, które wymagają udziału użytkownika. Na przykład usługa Chat lub wywołująca korzysta z *tokenów dostępu użytkowników* , aby umożliwić Dodawanie użytkowników w wątku i konwersację ze sobą.

## <a name="authentication-options"></a>Opcje uwierzytelniania

W poniższej tabeli przedstawiono zestawy SDK usług Azure Communication Services i ich opcje uwierzytelniania:

| SDK    | Opcja uwierzytelniania                               |
| ----------------- | ----------------------------------------------------|
| Tożsamość          | Klucz dostępu lub tożsamość zarządzana                      |
| SMS               | Klucz dostępu lub tożsamość zarządzana                      |
| Numery telefonów     | Klucz dostępu lub tożsamość zarządzana                      |
| Rozmów           | Token dostępu użytkownika                                   |
| Czat              | Token dostępu użytkownika                                   |

Każdą opcję autoryzacji można krótko opisać poniżej:

### <a name="access-key"></a>Klucz dostępu

Uwierzytelnianie klucza dostępu jest odpowiednie dla aplikacji usługi działających w środowisku zaufanej usługi. Klucz dostępu można znaleźć w portalu usługi Azure Communication Services. Aplikacja usługi używa jej jako poświadczeń do inicjowania odpowiednich zestawów SDK. Zobacz przykład sposobu użycia w [zestawie SDK tożsamości](../quickstarts/access-tokens.md). 

Ponieważ klucz dostępu jest częścią parametrów połączenia zasobu, uwierzytelnianie za pomocą parametrów połączenia jest równoważne z uwierzytelnianiem przy użyciu klucza dostępu.

Jeśli chcesz ręcznie wywołać interfejsy API usługi ACS przy użyciu klucza dostępu, musisz podpisać żądanie. Podpisywanie żądania zostało szczegółowo opisane w [samouczku](../tutorials/hmac-header-tutorial.md).

### <a name="managed-identity"></a>Tożsamość zarządzana

Zarządzane tożsamości, zapewniają lepsze zabezpieczenia i łatwość użycia nad innymi opcjami autoryzacji. Na przykład przy użyciu usługi Azure AD unika się, aby nie trzeba było przechowywać klucza dostępu do konta w kodzie, jak w przypadku autoryzacji klucza dostępu. Mimo że można nadal korzystać z autoryzacji klucza dostępu przy użyciu aplikacji usług komunikacyjnych, firma Microsoft zaleca przechodzenie do usługi Azure AD, jeśli to możliwe. 

Aby skonfigurować tożsamość zarządzaną, [Utwórz zarejestrowaną aplikację przy użyciu interfejsu wiersza polecenia platformy Azure](../quickstarts/managed-identity-from-cli.md). Następnie punkt końcowy i poświadczenia mogą służyć do uwierzytelniania zestawów SDK. Zobacz przykłady użycia [tożsamości zarządzanej](../quickstarts/managed-identity.md) .

### <a name="user-access-tokens"></a>Tokeny dostępu użytkowników

Tokeny dostępu użytkowników są generowane przy użyciu zestawu SDK tożsamości i są skojarzone z użytkownikami utworzonymi w zestawie SDK tożsamości. Zobacz przykład [tworzenia użytkowników i generowania tokenów](../quickstarts/access-tokens.md). Następnie tokeny dostępu użytkowników są używane do uwierzytelniania uczestników dodanych do konwersacji w programie Chat lub wywołaniu zestawu SDK. Aby uzyskać więcej informacji, zobacz [Dodawanie rozmowy do aplikacji](../quickstarts/chat/get-started.md). Uwierzytelnianie tokenu dostępu użytkowników jest różne w porównaniu z kluczem dostępu i uwierzytelnianiem tożsamości zarządzanej w celu uwierzytelnienia użytkownika, a nie zabezpieczonego zasobu platformy Azure.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie zasobów](../quickstarts/create-communication-resource.md) 
>  usług komunikacyjnych i zarządzanie nimi [Tworzenie aplikacji do zarządzania tożsamościami Azure Active Directory przy użyciu interfejsu wiersza polecenia](../quickstarts/managed-identity-from-cli.md) 
>  platformy Azure [Utwórz tokeny dostępu użytkowników](../quickstarts/access-tokens.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
