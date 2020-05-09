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
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0654bce86cf5fb0b5bd117e444721e95f137dd47
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652691"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Zagadnienia dotyczące korzystania z platforma uniwersalna systemu Windows z MSAL.NET
Deweloperzy aplikacji korzystających z platforma uniwersalna systemu Windows (platformy UWP) z MSAL.NET powinni wziąć pod uwagę Koncepcje opisane w tym artykule.

## <a name="the-usecorporatenetwork-property"></a>Właściwość UseCorporateNetwork
Na platformie środowisko wykonawcze systemu Windows (WinRT) `PublicClientApplication` ma właściwość `UseCorporateNetwork`Boolean. Ta właściwość umożliwia aplikacjom Windows 8.1 i aplikacjom platformy UWP korzystanie z zintegrowanego uwierzytelniania systemu Windows (IWA), jeśli użytkownik jest zalogowany na koncie z dzierżawcą Azure Active Directory federacyjnego (Azure AD). Użytkownicy zalogowani do systemu operacyjnego mogą również korzystać z logowania jednokrotnego (SSO). Po ustawieniu `UseCorporateNetwork` właściwości MSAL.NET korzysta z brokera uwierzytelniania w sieci Web (WAB).

> [!IMPORTANT]
> Ustawienie `UseCorporateNetwork` właściwości na true zakłada, że deweloper aplikacji włączył IWA w aplikacji. Aby włączyć IWA:
> - W aplikacji `Package.appxmanifest`platformy UWP na karcie **możliwości** Włącz następujące możliwości:
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

Aby uzyskać więcej informacji, zobacz [brokera uwierzytelniania w sieci Web — programu Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Następne kroki
Poniższe przykłady zawierają więcej informacji.

Przykład | Platforma | Opis 
|------ | -------- | -----------|
|[Active-Directory-dotnet-Native-platformy UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Aplikacja kliencka platformy UWP, która korzysta z MSAL.NET. Uzyskuje dostęp do Microsoft Graph dla użytkownika, który uwierzytelnia się za pomocą punktu końcowego usługi Azure AD 2,0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-Xamarin-Native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, platformy UWP | Prosta aplikacja Xamarin Forms, która pokazuje, jak używać MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem punktu końcowego usługi Azure AD 2,0. Pokazano w nim także, jak uzyskać dostęp do Microsoft Graph i wyświetlić otrzymany token. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
