---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008006"
---
| Język/struktura | Projekt włączony<br/>GitHub                                                                 | Pakiet                                                                                | Otrzymania<br/>uruchomieniu                           | Logowanie użytkowników                                            | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Szybki start](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Węzeł               | [Węzeł MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal — węzeł](https://www.npmjs.com/package/@azure/msal-node)  | [Szybki start](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal — Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Szybki start](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![Biblioteka nie może żądać tokenów identyfikatora dla logowania użytkownika.][n] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/