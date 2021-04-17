---
ms.openlocfilehash: 05fc91667f34262c6b510c0e1464d3e5fad339bc
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590149"
---
| Język/framework | W programie Project on<br/>GitHub                                                                                                    | Pakiet                                                                      | Coraz<br/>Rozpoczął                             | Logowanie użytkowników                                         | Uzyskiwanie dostępu do internetowych interfejsów API                                                 | Ogólnie dostępne (GA) *lub*<br/>Publiczna wersja<sup>zapoznawcza 1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Biblioteka może żądać tokenów identyfikatorów do logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Publiczna wersja zapoznawcza                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Samouczek](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Biblioteka może żądać tokenów identyfikatorów do logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Biblioteka może żądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Publiczna wersja zapoznawcza                                               |
| JavaScript           | [MSAL.js wersja 2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Samouczek](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Biblioteka może zażądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Samouczek](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Biblioteka może zażądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Biblioteka może zażądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Publiczna wersja zapoznawcza                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1 Uzupełniające</sup> [warunki użytkowania wersji zapoznawczych][preview-tos] Microsoft Azure dotyczą bibliotek w publicznej *wersji zapoznawczej.*

<sup>2 Przepływ</sup> [kodu uwierzytelniania tylko przy][auth-code-flow] użyciu protokołu PCKE (zalecane). 

<sup>3 Tylko niejawny</sup> [przepływ][implicit-flow] udzielania.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md