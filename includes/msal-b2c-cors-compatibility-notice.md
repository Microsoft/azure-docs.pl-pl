---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87311549"
---
> [!IMPORTANT]
> MSAL.js 2,0 obecnie nie obsługuje Azure AD B2C do użycia z przepływem kodu autoryzacji PKCE. W tej chwili Azure AD B2C zalecamy użycie niejawnego przepływu zgodnie z opisem w [samouczku: rejestrowanie aplikacji][implicit-flow]. Aby postępować zgodnie z informacjami o tym problemie, zobacz witrynę [MSAL.js wiki][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
