---
title: Uwierzytelnianie — Data Lake Storage Gen1 z usługą Azure AD
description: Dowiedz się, jak uwierzytelniać się za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106573"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory

Azure Data Lake Storage Gen1 używa Azure Active Directory do uwierzytelniania. Przed utworzeniem aplikacji, która współpracuje z Data Lake Storage Gen1, należy zdecydować, jak uwierzytelnić aplikację przy użyciu Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opcje uwierzytelniania

* **Uwierzytelnianie użytkownika końcowego** — poświadczenia platformy Azure użytkownika końcowego są używane do uwierzytelniania za pomocą Data Lake Storage Gen1. Aplikacja, którą tworzysz, z Data Lake Storage Gen1 monitowani o te poświadczenia użytkownika. W związku z tym mechanizm uwierzytelniania jest *interaktywny* , a aplikacja jest uruchamiana w kontekście zalogowanego użytkownika. Aby uzyskać więcej informacji i instrukcje, zobacz [uwierzytelnianie użytkowników końcowych w celu Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Uwierzytelnianie** między usługami — Użyj tej opcji, jeśli chcesz, aby aplikacja mogła uwierzytelnić się przy użyciu Data Lake Storage Gen1. W takich przypadkach należy utworzyć aplikację Azure Active Directory (AD) i użyć klucza z aplikacji usługi Azure AD do uwierzytelniania przy użyciu Data Lake Storage Gen1. W związku z tym mechanizm uwierzytelniania nie jest *interaktywny*. Aby uzyskać więcej informacji i instrukcje, zobacz temat [uwierzytelnianie między usługami dla Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

W poniższej tabeli przedstawiono sposób obsługi mechanizmów uwierzytelniania użytkowników końcowych i usług w ramach usługi Data Lake Storage Gen1. Oto jak odczytać tabelę.

* Symbol ✔ * oznacza, że opcja uwierzytelniania jest obsługiwana i zawiera linki do artykułu, w którym pokazano, jak używać opcji uwierzytelniania. 
* Symbol ✔ oznacza, że opcja uwierzytelniania jest obsługiwana. 
* Puste komórki oznaczają, że opcja uwierzytelniania nie jest obsługiwana.


|Użyj tej opcji uwierzytelniania z...                   |.NET         |Java     |PowerShell |Interfejs wiersza polecenia platformy Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Użytkownik końcowy (bez usługi MFA * *)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(przestarzałe)     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Użytkownik końcowy (z usługą MFA)                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Service-to-Service (przy użyciu klucza klienta)         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Service-to-Service (przy użyciu certyfikatu klienta) |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Kliknij symbol <b>✔ \* </b> . Jest to link.</i><br>
<i>* * Uwierzytelnianie wieloskładnikowe oznacza usługi uwierzytelniania wieloskładnikowego</i>

Aby uzyskać więcej informacji na temat używania Azure Active Directory do uwierzytelniania, zobacz  [scenariusze uwierzytelniania dla Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md) .

## <a name="next-steps"></a>Następne kroki

* [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)