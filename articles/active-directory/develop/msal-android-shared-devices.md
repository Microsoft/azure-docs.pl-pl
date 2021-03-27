---
title: Tryb udostępnionego urządzenia dla urządzeń z systemem Android
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się, jak włączyć tryb udostępnionego urządzenia, aby umożliwić pracownikom teraźniejszości współużytkowanie urządzenia z systemem Android
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: 005f69473fa238d56cf7d582a8af4000166d6939
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612436"
---
# <a name="shared-device-mode-for-android-devices"></a>Tryb udostępnionego urządzenia dla urządzeń z systemem Android

Procesy robocze teraźniejszości, takie jak detaliczne jednostki sprzedaży, członkowie załóg lotów i pracownicy usług polowych, często wykorzystują udostępnione urządzenie przenośne do pracy. Sprawia to problemy po rozpoczęciu udostępniania haseł lub numerów PIN w celu uzyskania dostępu do danych klienta i firmowych na udostępnionym urządzeniu.

Tryb udostępnionego urządzenia umożliwia skonfigurowanie urządzenia z systemem Android w taki sposób, aby można je było łatwo udostępnić wielu pracownikom. Pracownicy mogą szybko logować się i uzyskiwać dostęp do informacji o klientach. Po zakończeniu przesunięcia lub przeprowadzenia zadania mogą się wylogować z urządzenia, a następnie być natychmiast gotowe do użycia przez następnego pracownika.

Tryb udostępnionego urządzenia udostępnia również zarządzanie tożsamością utworzoną przez firmę Microsoft.

Aby utworzyć aplikację trybu udostępnionego urządzenia, deweloperzy i Administratorzy urządzeń w chmurze współpracują ze sobą:

- Deweloperzy piszą aplikację jednokontową (aplikacje z wieloma kontami nie są obsługiwane w trybie udostępnionego urządzenia), dodają `"shared_device_mode_supported": true` do konfiguracji aplikacji i zapisują kod do obsługi takich elementów jak udostępnione urządzenie do wylogowania.
- Administratorzy urządzeń przygotowuje urządzenie do udostępnienia przez zainstalowanie aplikacji Authenticator i ustawienie trybu udostępniania urządzenia przy użyciu aplikacji Authenticator. Tylko użytkownicy znajdujący się w roli [administratora urządzenia w chmurze](../roles/permissions-reference.md#cloud-device-administrator) mogą umieścić urządzenie w trybie udostępniania przy użyciu [aplikacji Authenticator](../user-help/user-help-auth-app-overview.md). Członkostwo w rolach organizacyjnych można skonfigurować w Azure Portal za pośrednictwem: **Azure Active Directory**  >  **role i Administratorzy**  >  **administrator urządzeń w chmurze**.

 Ten artykuł koncentruje się głównie na tym, co deweloperzy powinni wziąć pod uwagę.

## <a name="single-vs-multiple-account-applications"></a>Pojedyncze aplikacje vs z wieloma kontami

Aplikacje opracowane przy użyciu zestawu SDK Microsoft Authentication Library (MSAL) mogą zarządzać pojedynczym kontem lub wieloma kontami. Aby uzyskać szczegółowe informacje, zobacz [tryb pojedynczego konta lub tryb wielu kont](single-multi-account.md). Funkcje platformy tożsamości firmy Microsoft dostępne dla aplikacji różnią się w zależności od tego, czy aplikacja działa w trybie pojedynczego konta, czy też w trybie wielu kont.

**Aplikacje trybu udostępnionego urządzenia działają tylko w trybie jednego konta**.

> [!IMPORTANT]
> Aplikacje obsługujące tryb wielu kont nie mogą być uruchamiane na urządzeniu udostępnionym. Jeśli pracownik załaduje aplikację, która nie obsługuje trybu pojedynczego konta, nie zostanie uruchomiona na urządzeniu udostępnionym.
>
> Aplikacje utworzone przed zestawem SDK MSAL zostały uruchomione w trybie wielu kont i należy je zaktualizować w celu obsługi trybu pojedynczego konta, zanim będą mogły być uruchamiane na urządzeniu trybu współużytkowanego.

**Obsługa zarówno jednego konta, jak i wielu kont**

Aplikację można skompilować do obsługi uruchamiania na urządzeniach osobistych i na urządzeniach udostępnionych. Jeśli aplikacja obsługuje obecnie wiele kont i chcesz obsługiwać tryb udostępnionego urządzenia, Dodaj obsługę trybu pojedynczego konta.

Możesz również zmienić zachowanie aplikacji, w zależności od typu urządzenia, na którym działa. Użyj `ISingleAccountPublicClientApplication.isSharedDevice()` , aby określić, kiedy program ma być uruchamiany w trybie jednego konta.

Istnieją dwa różne interfejsy reprezentujące typ urządzenia, na którym aplikacja jest włączona. Po zażądaniu wystąpienia aplikacji z fabryki aplikacji MSAL, poprawny obiekt aplikacji jest dostarczany automatycznie.

Poniższy model obiektów ilustruje typ obiektu, który może zostać wyświetlony i co oznacza w kontekście urządzenia udostępnionego:

![model dziedziczenia aplikacji klienta publicznego](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Należy wykonać sprawdzanie typu i rzutować do odpowiedniego interfejsu podczas pobierania `PublicClientApplication` obiektu. Poniższy kod sprawdza tryb wielu kont lub pojedynczy tryb konta i rzutuje odpowiednio obiekt aplikacji:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Poniższe różnice mają zastosowanie w zależności od tego, czy aplikacja jest uruchomiona na urządzeniu udostępnionym lub osobistym:

|  | Urządzenie trybu udostępnionego  | Urządzenie osobiste |
|---------|---------|---------|
| **Konta**     | Pojedyncze konto | Wiele kont |
| **Logowanie** | Globalnie | Globalnie |
| **Wylogowywanie** | Globalnie | Każda aplikacja może kontrolować, czy wylogowywanie jest lokalne dla aplikacji, czy dla rodziny aplikacji. |
| **Obsługiwane typy kont** | Tylko konta służbowe | Obsługiwane konta osobiste i służbowe  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Dlaczego warto obsługiwać tylko tryb pojedynczego konta

Jeśli piszesz aplikację, która będzie używana tylko dla procesów roboczych teraźniejszości przy użyciu urządzenia udostępnionego, zalecamy zapisanie aplikacji w celu zapewnienia obsługi tylko trybu pojedynczego konta. Obejmuje to większość aplikacji, które są zadaniami, takimi jak aplikacje do rekordów medycznych, aplikacje fakturowania i większość aplikacji biznesowych. Tryb tylko do obsługi jednego konta upraszcza programowanie, ponieważ nie trzeba implementować dodatkowych funkcji, które są częścią aplikacji z wieloma kontami.

## <a name="what-happens-when-the-device-mode-changes"></a>Co się stanie, gdy zmieni się tryb urządzenia

Jeśli aplikacja jest uruchomiona w trybie wielu kont, a administrator umieści urządzenie w trybie udostępnionego urządzenia, wszystkie konta na urządzeniu zostaną wyczyszczone z aplikacji i przejścia aplikacji do trybu pojedynczego konta.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Logowanie do udostępnionego urządzenia i ogólny cykl życia aplikacji

Po wylogowaniu się użytkownika należy podjąć działania w celu ochrony prywatności i danych użytkownika. Jeśli na przykład tworzysz aplikację do rejestrowania medycznego, upewnij się, że gdy użytkownik wyloguje wcześniej wyświetlone rekordy pacjenta są usuwane. Aplikacja musi być przygotowana do tego celu i sprawdzać przy każdym uruchomieniu pierwszego planu.

Gdy aplikacja korzysta z usługi MSAL do wylogowania użytkownika w aplikacji uruchomionej na urządzeniu w trybie udostępnionym, konto zalogowane i tokeny w pamięci podręcznej zostaną usunięte zarówno z aplikacji, jak i urządzenia.

Na poniższym diagramie przedstawiono ogólny cykl życia aplikacji oraz typowe zdarzenia, które mogą wystąpić podczas działania aplikacji. Diagram obejmuje od momentu uruchomienia działania, logowania się i wylogowywania konta oraz sposobu, w jaki zdarzenia takie jak Wstrzymywanie, wznawianie i zatrzymywanie działania mieszczą się w programie.

![Cykl życia aplikacji dla urządzenia udostępnionego](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj [tryb użytkowania urządzeń udostępnionych w aplikacji dla systemu Android](tutorial-v2-shared-device-mode.md) , który pokazuje, jak uruchomić aplikację teraźniejszości Worker na urządzeniu z systemem Android w trybie współdzielonym.
