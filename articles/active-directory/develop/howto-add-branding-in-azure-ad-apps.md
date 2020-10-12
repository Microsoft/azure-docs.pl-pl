---
title: Wskazówki dotyczące znakowania aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat wytycznych dotyczących znakowania aplikacji dla platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: ryanwi
ms.reviewer: arielgo, jiml
ms.custom: aaddev, signin_art
ms.openlocfilehash: 3ee59226853f4ea5aabf57a8866ba014aa874774
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706204"
---
# <a name="branding-guidelines-for-applications"></a>Wytyczne dotyczące oznaczania aplikacji marką

Podczas tworzenia aplikacji za pomocą platformy tożsamości firmy Microsoft należy skierować klientów do korzystania z konta służbowego (zarządzanego w usłudze Azure AD) lub konta osobistego na potrzeby rejestracji i logowania do aplikacji.

W tym artykule opisano następujące czynności:

- Poznasz dwa rodzaje kont użytkowników zarządzanych przez firmę Microsoft i dowiesz się, jak odwoływać się do konta usługi Azure AD w aplikacji.
- Dowiedz się, co należy zrobić, aby dodać logo firmy Microsoft do użycia w aplikacji
- Pobierzesz oficjalne obrazy **Zaloguj się** lub **Zaloguj się przy użyciu konta Microsoft** do wykorzystania w aplikacji.
- Poznasz zalecenia oraz zakazy dotyczące znakowania i nawigacji.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Konta osobiste a służbowe firmy Microsoft

Firma Microsoft zarządza dwoma rodzajami kont użytkowników:

- **Konta osobiste** (wcześniej konta Windows Live ID). Te konta reprezentują relację między *poszczególnymi* użytkownikami i firmą Microsoft oraz umożliwiają uzyskiwanie dostępu do urządzeń klientów i usług firmy Microsoft. Te konta są przeznaczone do użytku osobistego.
- **Konta służbowe.** Te konta są zarządzane przez firmę Microsoft w imieniu organizacji, która używa usługi Azure Active Directory. Te konta są używane do logowania się do Microsoft 365 i innych usług firmowych firmy Microsoft.

Konta służbowe firmy Microsoft zwykle są przypisane do użytkowników końcowych (pracowników, uczniów, studentów, pracowników federalnych) przez ich organizacje (firmy, szkoły, agencje rządowe). Te konta są główne bezpośrednio w chmurze (na platformie Azure AD) lub zsynchronizowane z usługą Azure AD z katalogu lokalnego, takiego jak Windows Server Active Directory. Firma Microsoft jest *nadzorcą* kont służbowych, ale należą one do organizacji i są przez nią kontrolowane.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Odwoływanie się do kont usługi Azure AD w aplikacji

Firma Microsoft nie pokazuje użytkownikom końcowym nazw marek platformy Azure lub usługi Active Directory i nie należy tego robić.

- Gdy użytkownicy są zalogowani, należy możliwie najczęściej używać nazwy i logo organizacji. Jest to lepsze rozwiązanie niż używanie ogólnych terminów, takich jak „Twoja organizacja”.
- Gdy użytkownicy nie są zalogowani, należy nazywać ich konta „kontami służbowymi” i używać logo firmy Microsoft, aby pokazać, że te konta są zarządzane przez firmę Microsoft. Nie należy używać takich terminów jak „konto przedsiębiorstwa”, „konto biznesowe” lub „konto firmowe”, które są mylące dla użytkowników.

## <a name="user-account-pictogram"></a>Piktogram konta użytkownika

We wcześniejszej wersji tych wytycznych zalecamy używanie piktogramu niebieskiej odznaki. Na podstawie opinii użytkowników i deweloperów teraz zalecamy używanie logo firmy Microsoft zamiast tego. Logo firmy Microsoft pomoże użytkownikom zrozumieć, że mogą oni ponownie używać konta, które są używane przez usługi Microsoft 365 lub innych usług firmy Microsoft do logowania się do aplikacji.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Tworzenie konta i logowanie się za pomocą usługi Azure AD

Aplikacja może mieć różne procedury tworzenia konta i logowania. W poniższych sekcjach przedstawiono wizualne wskazówki dla obu scenariuszy.

**Jeśli aplikacja obsługuje tworzenie kont użytkowników końcowych, np. w ramach bezpłatnej wersji próbnej lub modelu bezpłatnych funkcji premium (freemium)**: można wyświetlić przycisk **logowania**, który umożliwia użytkownikom dostęp do aplikacji przy użyciu konta służbowego lub osobistego. Przy pierwszym uzyskaniu dostępu do aplikacji w usłudze Azure AD jest wyświetlany monit o wyrażenie zgody.

**Jeśli organizacja wymaga uprawnień, na które zgodę mogą wyrazić tylko administratorzy, lub aplikacja wymaga licencjonowania organizacji**: należy oddzielić dostęp administratora od logowania użytkowników. Użycie **przycisku „Pobierz tę aplikację”** powoduje przekierowanie administratorów do logowania, a następnie wyświetlenie monitu o wyrażenie zgody w imieniu użytkowników w organizacji, co ma dodatkową zaletę w postaci pomijania monitów o wyrażenie zgody użytkowników końcowych w aplikacji.

## <a name="visual-guidance-for-app-acquisition"></a>Wizualne wskazówki dotyczące pozyskiwania aplikacji

Użycie linku „Pobierz aplikację” musi powodować przekierowanie użytkownika do strony udzielania dostępu (autoryzacji) usługi Azure AD, aby umożliwić administratorowi organizacji autoryzowanie aplikacji na potrzeby dostępu do danych organizacji hostowanych przez firmę Microsoft. Szczegółowe informacje dotyczące żądania dostępu przedstawiono w artykule [Integrating Applications with Azure Active Directory (Integrowanie aplikacji w usłudze Azure Active Directory)](./quickstart-register-app.md).

Po udzieleniu przez administratorów zgody na Twoją aplikację może ona zdecydować się na dodanie jej do Microsoft 365 środowiska uruchamiania aplikacji (dostępnego w Gofr i z [https://portal.office.com/myapps](https://portal.office.com/myapps) ). Aby informować o tej możliwości, można użyć takich terminów jak „Dodaj tę aplikację do organizacji” i wyświetlić przycisk, jak w poniższym przykładzie:

![Przycisk pokazujący logo firmy Microsoft i tekst "Dodaj do mojej organizacji"](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

Jednak zalecamy napisanie tekstu objaśnienia, aby nie trzeba było używać tylko przycisków. Na przykład:

> *Jeśli firma Microsoft korzysta już z usług Microsoft 365 lub innych firm, można udzielić <your_app_name> dostępu do danych organizacji. Dzięki temu użytkownicy będą mogli uzyskiwać dostęp do <your_app_name> z istniejącymi kontami służbowymi.*

Aby pobrać oficjalne logo firmy Microsoft do użycia w aplikacji, kliknij prawym przyciskiem myszy logo, którego chcesz użyć, a następnie zapisz je w komputerze.

| Zasób                                | Format PNG | Format SVG |
| ------------------------------------ | ---------- | ---------- |
| Logo firmy Microsoft  | ![Logo Microsoft do pobrania w formacie PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Logo Microsoft do pobrania w formacie SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Wizualne wskazówki dotyczące logowania

W aplikacji powinien być wyświetlany przycisk logowania, którego użycie powoduje przekierowanie użytkowników do punktu końcowego logowania zgodnego z protokołem używanym na potrzeby integracji z usługą Azure AD. W poniższej sekcji przedstawiono szczegółowe zalecenia dotyczące wyglądu tego przycisku.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram i tekst „Zaloguj się przy użyciu konta Microsoft”

Połączenie logo firmy Microsoft i tekstu „Zaloguj się przy użyciu konta Microsoft” pozwalają jednoznacznie odróżnić usługę Azure AD od innych dostawców tożsamości obsługiwanych przez aplikację. Jeśli nie masz wystarczającej ilości miejsca na tekst „Zaloguj się przy użyciu konta Microsoft”, możesz skrócić go do tekstu „Zaloguj się”. Możesz użyć jasnego lub ciemnego schematu kolorów przycisków.

Na poniższym diagramie pokazano granice zalecane przez firmę Microsoft podczas używania elementów zawartości w aplikacji. Te granice dotyczą tekstu „Zaloguj się przy użyciu konta Microsoft” i jego krótszej wersji („Zaloguj się”).

![Pokazuje "Zaloguj się przy użyciu konta Microsoft" redlines](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Aby pobrać oficjalne obrazy do użycia w aplikacji, kliknij prawym przyciskiem myszy obraz, którego chcesz użyć, a następnie zapisz go w komputerze.

| Zasób                                | Format PNG | Format SVG |
| ------------------------------------ | ---------- | ---------- |
| Zaloguj się przy użyciu konta Microsoft (motyw ciemny)  | ![Do pobrania "Zaloguj się przy użyciu konta Microsoft" ciemny motyw przycisku PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | !["Zaloguj się przy użyciu konta Microsoft" — ciemny przycisk formatu SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Zaloguj się przy użyciu konta Microsoft (motyw jasny) | ![Możliwe do pobrania "Zaloguj się przy użyciu konta Microsoft" motyw jasny PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![Możliwe do pobrania "Zaloguj się przy użyciu konta Microsoft" motyw jasny SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Zaloguj się (motyw ciemny)                 | ![Do pobrania "Zaloguj się" krótki motyw ciemny, PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![Plik do pobrania "Logowanie krótkie" — ciemny motyw SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Zaloguj się (motyw jasny)                | ![Do pobrania "Zaloguj się" krótki motyw jasny przycisku PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![Do pobrania "Zaloguj się" krótki motyw uproszczony](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Zalecenia dotyczące oznaczania marką

**NALEŻY** używać konta służbowego w połączeniu z przyciskiem „Zaloguj się przy użyciu konta Microsoft” w celu dodatkowego wyjaśnienia, aby pomóc użytkownikom końcowym określić, czy mogą użyć konta. **NIE NALEŻY** używać innych zwrotów, takich jak „konto przedsiębiorstwa”, „konto biznesowe” lub „konto firmowe”.

**Nie** używaj "Microsoft 365 identyfikator" lub "identyfikator platformy Azure". Microsoft 365 jest również nazwą oferty odbiorcy od firmy Microsoft, która nie korzysta z usługi Azure AD do uwierzytelniania.

**NIE NALEŻY** zmieniać logo firmy Microsoft.

**NIE NALEŻY** pokazywać użytkownikom końcowym marki platformy Azure lub usługi Active Directory. Można jednak używać tych zwrotów podczas zwracania się do deweloperów, informatyków i administratorów.

## <a name="navigation-dos-and-donts"></a>Zalecenia dotyczące nawigacji

**NALEŻY** umożliwić użytkownikom wylogowanie się i przełączenie się do innego konta użytkownika. Większość osób ma jedno konto osobiste Microsoft/Facebook/Google/Twitter, jednak użytkownicy są często skojarzeni z więcej niż jedną organizacją. Wkrótce zostanie wprowadzona obsługa wielu zalogowanych użytkowników.