---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010709"
---
| Język/struktura | Projekt włączony<br/>GitHub                                                                                                    | Pakiet                                                                      | Otrzymania<br/>uruchomieniu                             | Logowanie użytkowników                                         | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL kątowy v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
| Angular              | [MSAL kątowy](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Samouczek](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Samouczek](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
|JavaScript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Samouczek](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| React                | [MSAL reagować](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

<sup>2</sup> [przepływ kodu uwierzytelniania][auth-code-flow] z PCKE tylko (zalecane). 

<sup>3</sup> [niejawny przepływ dotacji][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md