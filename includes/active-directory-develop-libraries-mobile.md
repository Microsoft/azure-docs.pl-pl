---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007900"
---
| Platforma          | Projekt włączony<br/>GitHub                                                                          | Pakiet                                                                               | Otrzymania<br/>uruchomieniu                    | Logowanie użytkowników                                         | Dostęp do interfejsów API sieci Web                                                 | Ogólnie dostępna (GA) *lub*<br/>Publiczna wersja zapoznawcza<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [BIBLIOTEKA MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Szybki start](../articles/active-directory/develop/quickstart-v2-android.md) | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [BIBLIOTEKA MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| iOS (SWIFT/obj-C) | [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [BIBLIOTEKA MSAL](https://cocoapods.org/pods/MSAL)                                               | [Samouczek](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Biblioteka może żądać tokenów identyfikatora dla logowania użytkownika.][y] | ![Biblioteka może żądać tokenów dostępu dla chronionych interfejsów API sieci Web.][y] | Ogólna dostępność                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatkowe warunki użytkowania dla][preview-tos] wersji zapoznawczych Microsoft Azure mają zastosowanie do bibliotek w *publicznej wersji zapoznawczej*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/