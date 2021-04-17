---
ms.openlocfilehash: 9ddc240257bda29d5db5cae3eb6a830273d339cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107589933"
---
| Język/framework | Projekt wł.<br/>GitHub                                                                                     | Pakiet                                                                               | Coraz<br/>Rozpoczął                        | Logowanie użytkowników                                         | Uzyskiwanie dostępu do internetowych interfejsów API                                                 | Ogólnie dostępne (GA) *lub*<br/>Publiczna wersja<sup>zapoznawcza 1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Elektronów             | [MsAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Biblioteka może zażądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Publiczna wersja zapoznawcza                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Biblioteka może zażądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
| macOS (Swift/Obj-C)  | [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [BIBLIOTEKA MSAL](https://cocoapods.org/pods/MSAL)                                               | [Samouczek](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![Biblioteka może zażądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
| Platforma UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Samouczek](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![Biblioteka może żądać tokenów identyfikatorów do logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Samouczek](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![Biblioteka może żądać tokenów identyfikatorów dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych internetowych interfejsów API.][y] | Ogólna dostępność                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1 Uzupełniające</sup> [warunki użytkowania wersji zapoznawczych][preview-tos] Microsoft Azure dotyczą bibliotek w publicznej *wersji zapoznawczej.*

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/