---
title: Interfejs API REST usługi Azure App Configuration — uwierzytelnianie
description: Strony referencyjne do uwierzytelniania za pomocą interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932697"
---
# <a name="authentication"></a>Authentication

Wszystkie żądania HTTP muszą zostać uwierzytelnione. Obsługiwane są następujące schematy uwierzytelniania.

## <a name="hmac"></a>FUNKCJE

[Uwierzytelnianie HMAC](./rest-api-authentication-hmac.md) używa losowo wygenerowanego klucza tajnego do podpisywania ładunku żądania. Szczegółowe informacje na temat sposobu autoryzacji żądań przy użyciu tej metody uwierzytelniania można znaleźć w sekcji [autoryzacja HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Uwierzytelnianie Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) wykorzystuje token okaziciela uzyskany z Azure Active Directory do uwierzytelniania żądań. Szczegółowe informacje na temat sposobu autoryzacji żądań przy użyciu tej metody uwierzytelniania można znaleźć w sekcji [autoryzacja usługi Azure AD](./rest-api-authorization-azure-ad.md) .