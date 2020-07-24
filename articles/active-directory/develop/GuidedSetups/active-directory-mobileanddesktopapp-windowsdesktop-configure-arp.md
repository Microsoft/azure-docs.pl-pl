---
title: Azure AD v2 Windows Desktop Wprowadzenie-config
description: Jak aplikacja Windows Desktop .NET (XAML) może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy Azure Active Directory v2.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87162726"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Dodawanie informacji rejestracyjnych aplikacji do aplikacji
W tym kroku należy dodać identyfikator aplikacji do projektu.

1.  Otwórz `App.xaml.cs` i Zastąp wiersz zawierający `ClientId` :

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Co dalej

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
