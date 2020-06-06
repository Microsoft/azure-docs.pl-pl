---
title: Wybieranie platformy deweloperskiej frontonu do tworzenia aplikacji klienckich za pomocą programu Visual Studio i usług platformy Azure
description: Poznaj obsługiwane języki natywne i Międzyplatformowe umożliwiające tworzenie aplikacji klienckich.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 3fb8057d0619193237567b619ca93b92526a3496
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450843"
---
# <a name="choose-mobile-development-frameworks"></a>Wybierz platformy deweloperskie dla urządzeń przenośnych
Deweloperzy mogą używać technologii po stronie klienta do kompilowania aplikacji mobilnych, korzystając z określonych struktur i wzorców dla podejścia międzyplatformowego. W oparciu o czynniki decyzyjne deweloperzy mogą tworzyć:
- Natywne aplikacje pojedynczej platformy przy użyciu języków, takich jak cele C i Java
- Aplikacje dla wielu platform za pomocą platformy Xamarin, .NET i C #
- Aplikacje hybrydowe przy użyciu oprogramowania Cordova i jego wariantów

## <a name="native-platforms"></a>Platformy natywne
Tworzenie aplikacji natywnych wymaga specyficznych dla platformy języków programowania, zestawów SDK, środowisk programistycznych i innych narzędzi oferowanych przez dostawców systemu operacyjnego.

### <a name="ios"></a>iOS
Utworzona i opracowana przez firmę Apple system iOS służy do kompilowania aplikacji na urządzeniach firmy Apple, a mianowicie dla telefonu iPhone i tabletu iPad.

- **Języki programowania**: obiektyw-C, SWIFT
- **IDE**: Xcode
- **Zestaw SDK**: zestaw SDK systemu iOS

### <a name="android"></a>Android
SYSTEM Android jest przeznaczony do tworzenia aplikacji, które mogą być uruchamiane na różnych urządzeniach smartphone i tabletach.

- **Język programowania**: Java, Kotlin 
- **IDE**: Android Studio i narzędzia deweloperskie dla systemu Android 
- **Zestaw SDK**: Android SDK

### <a name="windows"></a>Windows
- **Język programowania**: C #
- **IDE**: Visual Studio, Visual Studio Code
- **Zestaw SDK**: Windows SDK

#### <a name="pros"></a>Zalety
- Dobre środowisko użytkownika
- Reagowanie aplikacji o wysokiej wydajności i możliwości interfejsów z natywnymi bibliotekami
- Wysoce bezpieczne aplikacje

#### <a name="cons"></a>Wady
- Aplikacja działa tylko na jednej platformie
- Większa intensywność zasobów dla deweloperów i kosztowna kompilacja aplikacji
- Obniż użycie kodu

## <a name="cross-platforms-and-hybrid-applications"></a>Aplikacje Międzyplatformowe i hybrydowe
Aplikacje dla wielu platform umożliwiają jednoczesne pisanie natywnych aplikacji mobilnych, udostępnianie kodu i uruchamianie ich w systemach iOS, Android i Windows.

### <a name="xamarin"></a>Xamarin
Należące do firmy Microsoft, Platforma [Xamarin](https://visualstudio.microsoft.com/xamarin/) służy do tworzenia niezawodnych, międzyplatformowych aplikacji mobilnych w języku C#. Platforma Xamarin ma bibliotekę klas i środowisko uruchomieniowe działające na wielu platformach, takich jak iOS, Android i Windows. Kompiluje także natywne (nieinterpretowane) aplikacje, które zapewniają wysoką wydajność. Platforma Xamarin łączy wszystkie możliwości natywnych platform i dodaje wiele zaawansowanych funkcji.

- **Język programowania**: C #
- **IDE**: program Visual Studio w systemie Windows lub Mac

### <a name="react-native"></a>Zareaguj na natywny
W serwisie Facebook w systemie 2015 system [reaguje na natywną](https://facebook.github.io/react-native/) platformę [JavaScript do](https://github.com/facebook/react-native) pisania w rzeczywistości natywnego renderowania aplikacji mobilnych dla systemów iOS i Android. Jest on oparty na reakcji, biblioteki języka JavaScript w serwisie Facebook do tworzenia interfejsów użytkownika. Zamiast wskazywać przeglądarkę, jest przeznaczona dla platform mobilnych. Reagowanie natywnie używa składników natywnych zamiast składników sieci Web jako bloków konstrukcyjnych.
 
- **Język programowania**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Aparat Unity jest zoptymalizowany pod kątem tworzenia gier. Za jego pomocą można wystawić wysokiej jakości aplikacje 2D lub 3D przy użyciu języka C# dla platform takich jak Windows, iOS, Android i Xbox.

### <a name="cordova"></a>Cordova
Program Cordova umożliwia tworzenie hybrydowych aplikacji przy użyciu Visual Studio Tools Apache Cordova lub Visual Studio Code z rozszerzeniami dla oprogramowania Cordova. Korzystając z podejścia hybrydowego, można udostępniać składniki witrynom sieci Web i ponownie używać aplikacji opartych na serwerze internetowym z podejściem hostowanych aplikacji sieci Web opartym na oprogramowaniu Cordova.

#### <a name="pros"></a>Zalety
- Zwiększenie użyteczności kodu przez utworzenie jednej bazy kodu dla wielu platform
- Zadbaj do szerszego grona użytkowników na wielu platformach
- Znacząca redukcja czasu projektowania
- Łatwa do uruchomienia i aktualizacji

#### <a name="cons"></a>Wady
- Niższa wydajność
- Brak elastyczności
- Każda platforma ma unikatowy zestaw funkcji i funkcji, które ułatwiają tworzenie aplikacji natywnych
- Zwiększony czas projektowania interfejsu użytkownika
- Ograniczenie narzędzi
