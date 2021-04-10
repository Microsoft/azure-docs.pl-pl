---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007973"
---
| Język/struktura | Projekt włączony<br/>GitHub                                                                                     | Pakiet                                                                               | Otrzymania<br/>uruchomieniu                        | Logowanie użytkowników                                         | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Elektron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Publiczna wersja zapoznawcza                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| macOS (SWIFT/obj-C)  | [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [BIBLIOTEKA MSAL](https://cocoapods.org/pods/MSAL)                                               | [Samouczek](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Platforma UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Samouczek](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Samouczek](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/