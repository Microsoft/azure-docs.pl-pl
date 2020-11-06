---
title: Interfejs API REST usługi Azure App Configuration — uwierzytelnianie
description: Strony referencyjne do uwierzytelniania za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424331"
---
# <a name="authentication"></a>Authentication

Wszystkie żądania HTTP muszą zostać uwierzytelnione. Obsługiwane są następujące schematy uwierzytelniania.

## <a name="hmac"></a>FUNKCJE

[Uwierzytelnianie HMAC](./rest-api-authentication-hmac.md) używa losowo wygenerowanego klucza tajnego do podpisywania ładunku żądania. Szczegółowe informacje na temat sposobu autoryzacji żądań przy użyciu tej metody uwierzytelniania można znaleźć w sekcji [autoryzacja HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Uwierzytelnianie Azure Active Directory (Azure AD)](/azure/active-directory/authentication/overview-authentication) wykorzystuje token okaziciela uzyskany z Azure Active Directory do uwierzytelniania żądań. Szczegółowe informacje na temat sposobu autoryzacji żądań przy użyciu tej metody uwierzytelniania można znaleźć w sekcji [autoryzacja usługi Azure AD](./rest-api-authorization-azure-ad.md) .
