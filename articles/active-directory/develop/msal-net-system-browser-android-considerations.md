---
title: Zagadnienia dotyczące przeglądarki systemu Android w programie (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat zagadnień dotyczących używania przeglądarek systemu w programie Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063471"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Zagadnienia dotyczące korzystania z usługi MSAL.NET w przeglądarce systemu Android dla platformy Xamarin

W tym artykule omówiono zagadnienia, które należy wziąć pod uwagę podczas korzystania z przeglądarki systemu w programie Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).

Począwszy od programu MSAL.NET 2.4.0 Preview, MSAL.NET obsługuje przeglądarki inne niż Chrome. Na urządzeniu z systemem Android nie trzeba już instalować programu Chrome w celu uwierzytelnienia.

Zalecamy używanie przeglądarek, które obsługują niestandardowe karty. Oto kilka przykładów tych przeglądarek:

| Przeglądarki z obsługą kart niestandardowych | Nazwa pakietu |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. Browser|
|Brave | com. Brave. Browser|

Oprócz identyfikowania przeglądarek, które oferują obsługę kart niestandardowych, nasze testy wskazują, że niektóre przeglądarki, które nie obsługują niestandardowych kart, również działają w celu uwierzytelniania. Te przeglądarki obejmują program Opera, Opera Mini, inbrowser i Maxthon. 

## <a name="tested-devices-and-browsers"></a>Przetestowane urządzenia i przeglądarki
W poniższej tabeli wymieniono urządzenia i przeglądarki, które zostały przetestowane pod kątem zgodności z uwierzytelnianiem.

| Urządzenie | Przeglądarka     |  Wynik  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | Chrome\* | Zdane|
| Huawei/jeden + | Edge\* | Zdane|
| Huawei/jeden + | Firefox\* | Zdane|
| Huawei/jeden + | Brave\* | Zdane|
| Jeden + | Ecosia\* | Zdane|
| Jeden + | Kiwi\* | Zdane|
| Huawei/jeden + | Opera | Zdane|
| Huawei | OperaMini | Zdane|
| Huawei/jeden + | Brak przeglądarki | Zdane|
| Jeden + | Maxthon | Zdane|
| Huawei/jeden + | DuckDuckGo | Użytkownik anulował uwierzytelnianie|
| Huawei/jeden + | Przeglądarka UC | Użytkownik anulował uwierzytelnianie|
| Jeden + | Tuńczyk | Użytkownik anulował uwierzytelnianie|
| Jeden + | Przeglądarka CM | Użytkownik anulował uwierzytelnianie|
| Huawei/jeden + | Brak zainstalowanych | Wyjątek AndroidActivityNotFound|

\* Obsługuje karty niestandardowe

## <a name="known-issues"></a>Znane problemy

Jeśli użytkownik nie ma włączonej przeglądarki na urządzeniu, MSAL.NET zgłosi `AndroidActivityNotFound` wyjątek.  
  - Środki **zaradcze**: poproszenie użytkownika o włączenie przeglądarki na swoim urządzeniu. Zaleca się, aby przeglądarka obsługiwała niestandardowe karty.

Jeśli uwierzytelnianie nie powiedzie się (na przykład w przypadku uruchamiania uwierzytelniania z DuckDuckGo), MSAL.NET zwróci `AuthenticationCanceled MsalClientException` . 
  - **Problem z głównym**: przeglądarka, która obsługuje niestandardowe karty, nie została włączona na urządzeniu. Uwierzytelnianie zostało uruchomione przy użyciu przeglądarki, która nie mogła ukończyć uwierzytelniania. 
  - Środki **zaradcze**: poproszenie użytkownika o włączenie przeglądarki na swoim urządzeniu. Zaleca się, aby przeglądarka obsługiwała niestandardowe karty.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji i przykładów kodu, zobacz [Wybieranie między osadzoną przeglądarką internetową i przeglądarką systemową w systemie Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) i [osadzonym i systemowym interfejsie użytkownika sieci Web](msal-net-web-browsers.md#embedded-vs-system-web-ui).  