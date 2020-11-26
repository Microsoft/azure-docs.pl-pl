---
title: Interfejs API REST usługi Azure App Configuration — uwierzytelnianie
description: Strony referencyjne do uwierzytelniania za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183468"
---
# <a name="authentication"></a>Authentication

Wszystkie żądania HTTP muszą zostać uwierzytelnione. Obsługiwane są następujące schematy uwierzytelniania.

## <a name="hmac"></a>FUNKCJE

[Uwierzytelnianie HMAC](./rest-api-authentication-hmac.md) używa losowo wygenerowanego klucza tajnego do podpisywania ładunku żądania. Szczegółowe informacje na temat sposobu autoryzacji żądań przy użyciu tej metody uwierzytelniania można znaleźć w sekcji [autoryzacja HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Uwierzytelnianie Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) wykorzystuje token okaziciela uzyskany z Azure Active Directory do uwierzytelniania żądań. Szczegółowe informacje na temat sposobu autoryzacji żądań przy użyciu tej metody uwierzytelniania można znaleźć w sekcji [autoryzacja usługi Azure AD](./rest-api-authorization-azure-ad.md) .