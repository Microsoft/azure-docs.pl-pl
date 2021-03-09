---
title: Uwierzytelnianie w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat różnych sposobów uwierzytelniania aplikacji lub usługi w ramach usług komunikacyjnych.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b702afe9c4359d9f8711846d93fd79df9fc2f42e
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485965"
---
# <a name="authenticate-to-azure-communication-services"></a>Uwierzytelnianie w usłudze Azure Communications Services

Każdy klient korzystający z usług Azure Communications Services musi zostać uwierzytelniony. W typowej architekturze, zobacz [architektury klienta i serwera](./client-and-server-architecture.md), *klucze dostępu* lub *tożsamość zarządzana* są używane w usłudze zaufanego dostępu użytkowników w celu tworzenia użytkowników i wystawiania tokenów. *Token dostępu użytkowników* wystawiony przez zaufaną usługę dostępu użytkowników jest używany przez aplikacje klienckie do uzyskiwania dostępu do innych usług komunikacyjnych, na przykład rozmowy lub usługi wywołującej.

Usługa SMS usługi Azure Communication Services akceptuje również *klucze dostępu* lub *zarządzaną tożsamość* na potrzeby uwierzytelniania. Jest to zwykle wykonywane w aplikacji usługi działającej w środowisku zaufanym usługi.

Każdą opcję autoryzacji można krótko opisać poniżej:

- **Uzyskaj dostęp do uwierzytelniania klucza** dla SMS i operacji tożsamości. Uwierzytelnianie klucza dostępu jest odpowiednie dla aplikacji usługi działających w środowisku zaufanej usługi. Klucz dostępu można znaleźć w portalu usługi Azure Communications Services. Aby uwierzytelnić się przy użyciu klucza dostępu, aplikacja usługi używa klucza dostępu jako poświadczenia do inicjowania odpowiednich bibliotek programu SMS lub klienta tożsamości, zobacz [Tworzenie tokenów dostępu i zarządzanie nimi](../quickstarts/access-tokens.md). Ponieważ klucz dostępu jest częścią parametrów połączenia zasobu, zobacz [Tworzenie zasobów usług komunikacyjnych i zarządzanie nimi](../quickstarts/create-communication-resource.md), uwierzytelnianie za pomocą parametrów połączenia jest równoważne z uwierzytelnianiem przy użyciu klucza dostępu.
- Uwierzytelnianie **tożsamości zarządzanej** na potrzeby operacji SMS i tożsamości. Tożsamość zarządzana, zobacz [tożsamość zarządzana](../quickstarts/managed-identity.md), jest odpowiednia dla aplikacji usługi działających w środowisku zaufanej usługi. Aby można było uwierzytelnić się przy użyciu tożsamości zarządzanej, aplikacja usługi tworzy poświadczenia z IDENTYFIKATORem i kluczem tajnym tożsamości zarządzanej, a następnie inicjuje odpowiednie biblioteki klienta programu SMS lub tożsamości, zobacz temat [Tworzenie tokenów dostępu i zarządzanie nimi](../quickstarts/access-tokens.md).
- Uwierzytelnianie **tokenu dostępu użytkownika** na potrzeby rozmowy i wywoływania. Tokeny dostępu użytkowników pozwalają aplikacjom klienckim uwierzytelniać się w usłudze Azure Communication chat i usłudze wywołującej. Te tokeny są generowane w postaci "usługa dostępu do użytkowników zaufanych", którą tworzysz. Są one następnie udostępniane urządzeniom klienckim, które używają tokenu do inicjowania rozmowy i wywoływania bibliotek klienckich. Aby uzyskać więcej informacji, zobacz [Dodawanie rozmowy do aplikacji](../quickstarts/chat/get-started.md) na przykład.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie zasobów](../quickstarts/create-communication-resource.md) 
>  usług komunikacyjnych i zarządzanie nimi [Tworzenie aplikacji do zarządzania tożsamościami Azure Active Directory przy użyciu interfejsu wiersza polecenia](../quickstarts/managed-identity-from-cli.md) 
>  platformy Azure [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
