---
title: Informacje o wersji zestawu SDK czytnika immersyjny
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat Nowości w zestawie SDK języka JavaScript w czytniku immersyjny.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93133597"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Informacje o wersji zestawu SDK języka JavaScript dla czytnika immersyjny

## <a name="version-110"></a>1.1.0 wersja

Ta wersja zawiera nowe funkcje, poprawki zabezpieczeń, poprawki błędów, aktualizacje przykładów kodu i opcje konfiguracji.

#### <a name="new-features"></a>Nowe funkcje

* Włącz zapisywanie i ładowanie preferencji użytkownika w różnych przeglądarkach i urządzeniach
* Włącz Konfigurowanie opcji wyświetlania domyślnego
* Dodawanie opcji do ustawiania języka tłumaczenia, włączania tłumaczenia słów i włączania tłumaczenia dokumentu podczas uruchamiania czytnika immersyjny
* Dodawanie obsługi konfigurowania odczytywania na głos za pośrednictwem opcji
* Dodaj możliwość wyłączenia pierwszego uruchomienia środowiska
* Dodaj ImmersiveReaderView dla platformy UWP

#### <a name="improvements"></a>Ulepszenia

* Aktualizowanie przykładowego HTML kodu dla systemu Android do pracy z najnowszym zestawem SDK
* Zaktualizuj odpowiedź uruchamiania, aby zwrócić liczbę przetworzonych znaków
* Zaktualizuj przykłady kodu, aby użyć programu v 1.1.0
* Nie Zezwalaj na wywoływanie launchAsync w przypadku jego już ładowania
* Sprawdź nieprawidłową zawartość przez zignorowanie komunikatów, w których dane nie są ciągiem
* Zawijaj wywołanie do okna w klauzuli IF, aby sprawdzić pomoc techniczną dla przeglądarki

#### <a name="fixes"></a>Poprawki

* Naprawianie dependabot przez usunięcie przędzy. Blokada z GITIGNORE
* Usuń lukę w zabezpieczeniach, uaktualniając Pug do wersji v 3.0.0 w programie szybki start — przykład kodu NodeJS
* Popraw wiele luk w zabezpieczeniach, uaktualniając pakiety języka TypeScript i
* Napraw lukę w zabezpieczeniach, uaktualniając Microsoft. IdentityModel. clients. ActiveDirectory do wersji v 5.2.0

<br>

## <a name="version-100"></a>Wersja 1.0.0

Ta wersja zawiera istotne zmiany, nowe funkcje, ulepszenia przykładów kodu i poprawki błędów.

#### <a name="breaking-changes"></a>Zmiany powodujące niezgodność

* Wymagaj tokenu i poddomeny usługi Azure AD i oznacza, że tokeny używane w poprzednich wersjach są przestarzałe.
* Ustaw CookiePolicy na wyłączone. Przechowywanie preferencji użytkownika jest domyślnie wyłączone. Czytnik jest uruchamiany z ustawieniami domyślnymi za każdym razem, gdy CookiePolicy jest ustawiony na włączone.

#### <a name="new-features"></a>Nowe funkcje

* Dodawanie obsługi do włączania lub wyłączania plików cookie
* Przykład dodawania kodu szybkiego uruchomienia Kotlin systemu Android
* Przykład dodawania kodu szybkiego startu Java dla systemu Android
* Przykład dodawania kodu Node.js Szybki Start

#### <a name="improvements"></a>Ulepszenia

* Aktualizacja Node.js Advanced README.md
* Zmień przykładowy kod języka Python z zaawansowanego na szybki start
* Przenoszenie przykładowego kodu SWIFT systemu iOS na kod js/przykłady
* Zaktualizuj przykłady kodu, aby użyć programu v 1.0.0

#### <a name="fixes"></a>Poprawki

* Poprawka dla przykładu zaawansowanego kodu Node.js
* Dodaj brakujące pliki dla zaawansowanego-CSharp-wielu zasobów
* Usuń pl-US z hiperlinków

<br>

## <a name="version-003"></a>0.0.3 wersja

Ta wersja zawiera nowe funkcje, ulepszenia przykładów kodu, poprawki dotyczące luk w zabezpieczeniach i poprawki błędów.

#### <a name="new-features"></a>Nowe funkcje

* Przykład dodawania kodu SWIFT systemu iOS
* Dodawanie przykładowego kodu w języku C# z zaawansowaną użyciem wielu zasobów 
* Dodaj obsługę, aby wyłączyć funkcję przełączania pełnoekranowego
* Dodaj obsługę, aby ukryć przycisk Zakończ aplikacji czytnika immersyjny
* Dodawanie funkcji wywołania zwrotnego, która może być używana przez aplikację hosta podczas kończenia czytnika immersyjny
* Zaktualizuj przykłady kodu, aby używać uwierzytelniania Azure Active Directory

#### <a name="improvements"></a>Ulepszenia

* Aktualizowanie zaawansowanego przykładowego kodu w języku C# w celu uwzględnienia dokumentu programu Word
* Zaktualizuj przykłady kodu, aby użyć programu v 0.0.3

#### <a name="fixes"></a>Poprawki

* Uaktualnij lodash do wersji 4.17.14, aby naprawić lukę w zabezpieczeniach
* Aktualizowanie biblioteki MSAL języka C# w celu usunięcia luki w zabezpieczeniach
* Uaktualnienie domieszki-głębokie do wersji 1.3.2 w celu usunięcia luki w zabezpieczeniach
* Uaktualnienie to, WebPack i WebPack-CLI, które były używane do rozwiązywania luk w zabezpieczeniach z podatnymi wersjami opcji Set-Value i domieszki-głębokimi

<br>

## <a name="version-002"></a>0.0.2 wersja

Ta wersja zawiera nowe funkcje, ulepszenia przykładów kodu, poprawki dotyczące luk w zabezpieczeniach i poprawki błędów.

#### <a name="new-features"></a>Nowe funkcje

* Przykład dodawania zaawansowanego kodu języka Python
* Przykład dodawania kodu szybkiego startu Java
* Dodawanie prostego przykładu kodu

#### <a name="improvements"></a>Ulepszenia

* Zmień nazwę elementu Resource na cogSvcsSubdomain
* Przenoszenie wpisów tajnych poza kod i Używanie zmiennych środowiskowych
* Zaktualizuj przykłady kodu, aby użyć programu v 0.0.2

#### <a name="fixes"></a>Poprawki

* Usuń błędy ułatwień dostępu do przycisku czytnika immersyjny
* Naprawiono uszkodzenie przewijania
* Uaktualnij pakiet kierownicy do wersji 4.1.2, aby rozwiązać luki w zabezpieczeniach
* Naprawia usterki w testach jednostkowych zestawu SDK
* Poprawki w języku JavaScript błędy zgodności programu Internet Explorer 11
* Adresy URL SDK aktualizacji

<br>

## <a name="version-001"></a>0.0.1 wersja

Początkowa wersja zestawu SDK języka JavaScript czytnika immersyjny.

* Dodaj zestaw SDK JavaScript czytnika immersyjny
* Dodaj obsługę, aby określić język interfejsu użytkownika
* Dodaj limit czasu, aby określić, kiedy funkcja launchAsync powinna kończyć się niepowodzeniem z błędem przekroczenia limitu czasu
* Dodaj obsługę, aby określić indeks z elementu IFRAME w czytniku immersyjny
* Dodawanie obsługi do używania tagu WebView zamiast elementu IFRAME w celu zapewnienia zgodności z aplikacjami programu Chrome
* Dodawanie testów jednostkowych zestawu SDK
* Dodawanie przykładu zaawansowanego kodu Node.js
* Dodaj przykładowy kod w języku C#
* Przykład dodawania kodu szybkiego uruchomienia języka C#
* Dodawanie konfiguracji pakietu, przędzy i innych plików kompilacji
* Dodaj pliki konfiguracji narzędzia Git
* Dodaj pliki README.md do przykładów kodu i zestawu SDK
* Dodaj licencję MIT
* Dodaj instrukcje dla współautora
* Dodaj statyczny przycisk ikon elementy zawartości SVG

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy z czytnikiem immersyjnym:

* Przeczytaj [Informacje o bibliotece klienta czytnika immersyjny](./reference.md)
* Eksplorowanie [biblioteki klienta czytnika immersyjny w serwisie GitHub](https://github.com/microsoft/immersive-reader-sdk)
