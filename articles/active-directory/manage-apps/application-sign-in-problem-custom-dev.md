---
title: Problemy z logowaniem do aplikacji opracowanej niestandardowo | Microsoft Docs
description: Typowe błędy, które mogą spowodować, że nie można zalogować się do aplikacji opracowanej przy użyciu usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "65825237"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemy z logowaniem do aplikacji opracowanej niestandardowo

Istnieje kilka błędów, które mogą spowodować, że nie można zalogować się do aplikacji. Największą przyczyną występowania tego problemu jest błędna konfiguracja aplikacji.

## <a name="errors-related-to--misconfigured-apps"></a>Błędy związane ze niepoprawnie skonfigurowanymi aplikacjami

* Sprawdź, czy konfiguracje w portalu pasują do zawartości w aplikacji. W tym celu Porównaj identyfikatory klienta/aplikacji, adresy URL odpowiedzi, klucze tajne klienta/klucza i identyfikator URI aplikacji.

* Porównaj zasób, do którego żądasz dostępu, w kodzie ze skonfigurowanymi uprawnieniami na karcie **wymagane zasoby** , aby upewnić się, że zażądano tylko skonfigurowanych zasobów.

* Zobacz [usługi Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) , aby poznać podobne błędy lub problemy.

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Wyrażanie zgody i Integrowanie aplikacji z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Wyrażanie zgody i uprawnień dla aplikacji w usłudze Azure AD v 2.0 z zbieżnymi aplikacjami](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Usługa Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
