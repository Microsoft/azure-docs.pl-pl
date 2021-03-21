---
title: 'Szybki Start: rejestrowanie i Uwidacznianie interfejsu API sieci Web | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start zarejestrujesz internetowy interfejs API z platformą tożsamości firmy Microsoft i skonfigurujesz jej zakresy, umożliwiając klientom dostęp do zasobów interfejsu API na podstawie uprawnień.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103978"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Szybki Start: Konfigurowanie aplikacji do udostępniania internetowego interfejsu API

W tym przewodniku szybki start zarejestrujesz internetowy interfejs API za pomocą platformy tożsamości firmy Microsoft i udostępnimy go aplikacjom klienckim przez dodanie przykładowego zakresu. Rejestrując internetowy interfejs API i uwidaczniając go za pośrednictwem zakresów, możesz zapewnić dostęp oparty na uprawnieniach do swoich zasobów autoryzowanym użytkownikom i aplikacjom klienckim, które uzyskują dostęp do interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją — [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Kończenie [przewodnika Szybki Start: Konfigurowanie dzierżawy](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Rejestrowanie internetowego interfejsu API

Aby zapewnić dostęp z zakresu do zasobów w internetowym interfejsie API, należy najpierw zarejestrować interfejs API na platformie tożsamości firmy Microsoft.

1. Wykonaj kroki opisane w sekcji **Rejestrowanie aplikacji** [przewodnika Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).
1. Pomiń sekcje **Dodawanie identyfikatora URI przekierowania** i **Konfigurowanie ustawień platformy** . Nie musisz konfigurować identyfikatora URI przekierowania dla internetowego interfejsu API, ponieważ żaden użytkownik nie jest interaktywnie zalogowany.
1. Pomiń sekcję **Dodaj poświadczenia** teraz. Tylko wtedy, gdy interfejs API uzyskuje dostęp do podrzędnego interfejsu API, będzie potrzebować własnych poświadczeń, a w tym artykule nie omówiono tego scenariusza.

Po zarejestrowaniu interfejsu API sieci Web możesz dodać zakresy, które mogą być używane przez kod interfejsu API w celu zapewnienia szczegółowych uprawnień użytkownikom interfejsu API.

## <a name="add-a-scope"></a>Dodawanie zakresu

Kod w aplikacji klienckiej żąda uprawnień do wykonywania operacji zdefiniowanych przez internetowy interfejs API przez przekazanie tokenu dostępu wraz z żądaniami do chronionego zasobu (internetowy interfejs API). Internetowy interfejs API wykonuje następnie żądaną operację tylko wtedy, gdy otrzymany token dostępu zawiera zakresy wymagane dla operacji.

Najpierw wykonaj następujące kroki, aby utworzyć przykładowy zakres o nazwie `Employees.Read.All` :

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę zawierający rejestrację aplikacji klienckiej.
1. Wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**, a następnie wybierz pozycję Rejestracja aplikacji interfejsu API.
1. Wybierz opcję **Uwidacznianie interfejsu API**  >  **Dodaj zakres**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Rejestracja aplikacji uwidacznia okienko interfejsu API w Azure Portal":::

1. Zostanie wyświetlony monit o ustawienie **identyfikatora URI aplikacji** , jeśli nie został on jeszcze skonfigurowany.

   Identyfikator URI aplikacji działa jako prefiks zakresów, które będą się odwoływać w kodzie interfejsu API, i musi być globalnie unikatowy. Możesz użyć podanej wartości domyślnej, która jest w postaci `api://<application-client-id>` lub określić bardziej czytelny identyfikator URI `https://contoso.com/api` .

1. Następnie określ atrybuty zakresu w okienku **Dodaj zakres** . W tym przewodniku można użyć przykładowych wartości lub określić własne.

    | Pole | Opis | Przykład |
    |-------|-------------|---------|
    | **Nazwa zakresu** | Nazwa zakresu. Wspólna konwencja nazewnictwa zakresów to `resource.operation.constraint` . | `Employees.Read.All` |
    | **Kto może wyrazić zgodę** | Określa, czy ten zakres może być uznawany przez użytkowników, czy wymagana jest zgoda administratora. Wybierz pozycję **Tylko administratorzy** dla uprawnień dających duże przywileje. | **Administratorzy i użytkownicy** |
    | **Nazwa wyświetlana zgody administratora** | Krótki opis celu zakresu, który będzie widoczny tylko dla administratorów. | `Read-only access to Employee records` |
    | **Opis na potrzeby wyrażenia zgody przez administratora** | Bardziej szczegółowy opis uprawnień przyznanych przez zakres, które będą widoczne tylko dla administratorów. | `Allow the application to have read-only access to all Employee data.` |
    | **Nazwa wyświetlana na potrzeby wyrażenia zgody przez użytkownika** | Krótki opis celu zakresu. Widoczne dla użytkowników tylko wtedy, gdy ustawisz **, kto może wyrazić zgodę** na **administratorów i użytkowników**. | `Read-only access to your Employee records` |
    | **Opis na potrzeby wyrażenia zgody przez użytkownika** | Bardziej szczegółowy opis uprawnień przyznanych przez zakres. Widoczne dla użytkowników tylko wtedy, gdy ustawisz **, kto może wyrazić zgodę** na **administratorów i użytkowników**. | `Allow the application to have read-only access to your Employee data.` |

1. Ustaw **stan** na **włączone**, a następnie wybierz pozycję **Dodaj zakres**.

1. Obowiązkowe Aby pominąć monitowanie o zgodę użytkowników aplikacji na zdefiniowane zakresy, możesz *wstępnie autoryzować* aplikację kliencką w celu uzyskania dostępu do internetowego interfejsu API. Wstępnie Autoryzuj *tylko* te aplikacje klienckie, którym ufasz, ponieważ użytkownicy nie będą mogli zrezygnować z zgody.
    1. W obszarze **autoryzowane aplikacje klienckie** wybierz pozycję **Dodaj aplikację kliencką** .
    1. Wprowadź **Identyfikator aplikacji** klienckiej, która ma zostać wstępnie autoryzowana. Na przykład, że wcześniej zarejestrowano aplikację sieci Web.
    1. W obszarze **autoryzowane zakresy** wybierz zakresy, dla których chcesz pominąć monitowanie o zgodę, a następnie wybierz pozycję **Dodaj aplikację**.

    Jeśli wykonano ten krok opcjonalny, aplikacja kliencka jest teraz wstępnie autoryzowaną aplikacją klienta (PPW) i użytkownicy nie będą monitowani o ich zgodę podczas logowania się do niego.

## <a name="add-a-scope-requiring-admin-consent"></a>Dodawanie zakresu wymagającego zgody administratora

Następnie Dodaj inny przykładowy zakres o nazwie `Employees.Write.All` tylko Administratorzy mogą wyrazić zgodę. Zakresy wymagające zgody administratora są zwykle używane do zapewniania dostępu do operacji o wyższym poziomie uprawnień i często przez aplikacje klienckie, które działają jako usługi zaplecza lub demony, które nie logują się użytkownika interaktywnie.

Aby dodać `Employees.Write.All` przykład zakresu, postępuj zgodnie z instrukcjami w sekcji [Dodawanie zakresu](#add-a-scope) i określ te wartości w okienku **Dodaj zakres** :

| Pole                          | Przykładowa wartość                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Nazwa zakresu**                 | `Employees.Write.All`                                              |
| **Kto może wyrazić zgodę**            | **Tylko Administratorzy**                                                    |
| **Nazwa wyświetlana zgody administratora** | `Write access to Employee records`                                 |
| **Opis na potrzeby wyrażenia zgody przez administratora**  | `Allow the application to have write access to all Employee data.` |
| **Nazwa wyświetlana na potrzeby wyrażenia zgody przez użytkownika**  | *Brak (pozostaw puste)*                                               |
| **Opis na potrzeby wyrażenia zgody przez użytkownika**   | *Brak (pozostaw puste)*                                               |

## <a name="verify-the-exposed-scopes"></a>Weryfikowanie uwidocznionych zakresów

Jeśli pomyślnie dodano oba przykładowe zakresy opisane w poprzednich sekcjach, zostaną one wyświetlone w okienku **Uwidacznianie interfejsu API** w ramach rejestracji aplikacji internetowego interfejsu API, podobnie jak w przypadku tego obrazu:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Zrzut ekranu przedstawiający okienko Uwidacznianie interfejsu API zawierające dwa uwidocznione zakresy.":::

Jak pokazano na obrazie, pełny ciąg zakresu jest połączeniem identyfikatora **aplikacji** internetowego interfejsu API i **nazwy zakresu** zakresu.

Na przykład, jeśli identyfikator URI aplikacji internetowego interfejsu API to `https://contoso.com/api` i nazwa zakresu to `Employees.Read.All` , pełny zakres to:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Używanie uwidocznionych zakresów

W następnym artykule z serii można skonfigurować rejestrację aplikacji klienta z dostępem do internetowego interfejsu API i zakresami zdefiniowanymi przez wykonanie kroków opisanych w tym artykule.

Po nadaniu rejestracji aplikacji klienckiej uprawnienia dostępu do internetowego interfejsu API klient może otrzymać token dostępu OAuth 2,0 za pomocą platformy tożsamości firmy Microsoft. Gdy klient wywołuje internetowy interfejs API, przedstawia token dostępu, którego zakres ( `scp` ) jest ustawiony na uprawnienia określone w rejestracji aplikacji klienta.

Jeśli jest to konieczne, później można uwidocznić dodatkowe zakresy. Należy pamiętać, że interfejs API sieci Web może uwidaczniać wiele zakresów skojarzonych z kilkoma operacjami. Zasób może kontrolować dostęp do internetowego interfejsu API w środowisku uruchomieniowym przez ocenę zakresu ( `scp` ) roszczeń w tokenie dostępu OAuth 2,0, który odbiera.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy interfejs API sieci Web został uwidoczniony przez skonfigurowanie jego zakresów, skonfiguruj rejestrację aplikacji klienta z uprawnieniami dostępu do zakresów.

> [!div class="nextstepaction"]
> [Skonfiguruj rejestrację aplikacji na potrzeby dostępu do interfejsu API sieci Web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
