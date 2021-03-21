---
title: PLATFORMY UWP — zagadnienia (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat zagadnień dotyczących używania platforma uniwersalna systemu Windows (platformy UWP) z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8a8aab447007eb574a7a4bc532d8177bd0d8b345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122480"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Zagadnienia dotyczące korzystania z platforma uniwersalna systemu Windows z MSAL.NET
Deweloperzy aplikacji korzystających z platforma uniwersalna systemu Windows (platformy UWP) z MSAL.NET powinni wziąć pod uwagę Koncepcje opisane w tym artykule.

## <a name="the-usecorporatenetwork-property"></a>Właściwość UseCorporateNetwork
Na platformie środowisko wykonawcze systemu Windows (WinRT) `PublicClientApplication` ma właściwość Boolean `UseCorporateNetwork` . Ta właściwość umożliwia aplikacjom systemu Windows 10 i aplikacjom platformy UWP korzystanie z zintegrowanego uwierzytelniania systemu Windows (IWA), jeśli użytkownik jest zalogowany do konta, które ma dzierżawę usługi federacyjnej Azure Active Directory (Azure AD). Użytkownicy zalogowani do systemu operacyjnego mogą również korzystać z logowania jednokrotnego (SSO). Po ustawieniu `UseCorporateNetwork` właściwości MSAL.NET korzysta z brokera uwierzytelniania w sieci Web (WAB).

> [!IMPORTANT]
> Ustawienie `UseCorporateNetwork` właściwości na true zakłada, że deweloper aplikacji WŁĄCZYŁ IWA w aplikacji. Aby włączyć IWA:
> - W aplikacji platformy UWP `Package.appxmanifest` na karcie **możliwości** Włącz następujące możliwości:
>   - **Uwierzytelnianie w przedsiębiorstwie**
>   - **Sieci prywatne (serwer & klienta)**
>   - **Udostępniony certyfikat użytkownika**

IWA nie jest domyślnie włączona, ponieważ Microsoft Store wymaga wysokiego poziomu weryfikacji, zanim zaakceptuje aplikacje, które zażądają możliwości uwierzytelniania przedsiębiorstwa lub udostępnionych certyfikatów użytkowników. Nie wszyscy deweloperzy chcą przeprowadzić ten poziom weryfikacji.

Na platformie platformy UWP podstawowa implementacja programu WAB nie działa prawidłowo w scenariuszach przedsiębiorstwa, w których włączono dostęp warunkowy. Użytkownicy widzą objawy tego problemu podczas próby zalogowania się przy użyciu usługi Windows Hello. Gdy użytkownik zostanie poproszony o wybranie certyfikatu:

- Nie znaleziono certyfikatu dla numeru PIN.
- Gdy użytkownik wybierze certyfikat, nie zostanie wyświetlony monit o podanie numeru PIN.

Możesz spróbować uniknąć tego problemu, korzystając z alternatywnej metody, takiej jak nazwa użytkownika i hasło i uwierzytelnianie telefonu, ale środowisko nie jest dobre.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Niektórzy klienci zgłosili następujący błąd logowania w określonych środowiskach przedsiębiorstwa, w którym wiedzą, że mają połączenie z Internetem i że połączenie współpracuje z siecią publiczną.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Można uniknąć tego problemu, upewniając się, że w sieci prywatnej jest używany program WAB (podstawowy składnik systemu Windows). Można to zrobić, ustawiając klucz rejestru:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Aby uzyskać więcej informacji, zobacz [brokera uwierzytelniania w sieci Web — programu Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Następne kroki
Poniższe przykłady zawierają więcej informacji.

Przykład | Platforma | Opis 
|------ | -------- | -----------|
|[Active-Directory-dotnet-Native-platformy UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | Platforma UWP | Aplikacja kliencka platformy UWP, która korzysta z MSAL.NET. Uzyskuje dostęp do Microsoft Graph dla użytkownika, który uwierzytelnia się za pomocą punktu końcowego usługi Azure AD 2,0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-Xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platformy UWP | Aplikacja platformy Xamarin Forms, która pokazuje, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem firmy Microsoft Identity platform. Pokazano w nim także, jak uzyskać dostęp do Microsoft Graph i wyświetlić otrzymany token. <br>![Diagram pokazujący, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem platformy tożsamości firmy Microsoft.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
