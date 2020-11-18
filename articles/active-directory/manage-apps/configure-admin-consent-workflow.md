---
title: Konfigurowanie przepływu pracy zgody administratora — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować sposób, w jaki użytkownicy końcowi zażądają dostępu do aplikacji, które wymagają zgody administratora.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fc688ca0981024e5e08c9fb20ecb6704efbd7f2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658778"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurowanie przepływu pracy zgody administratora (wersja zapoznawcza)

W tym artykule opisano sposób włączania funkcji przepływu pracy zgody administratora (wersja zapoznawcza), która umożliwia użytkownikom końcowym żądanie dostępu do aplikacji, które wymagają zgody administratora.

Bez przepływu pracy zgody administratora użytkownik w dzierżawie, na którym jest wyłączana zgoda użytkownika, zostanie zablokowany, gdy spróbuje uzyskać dostęp do dowolnej aplikacji, która wymaga uprawnień dostępu do danych organizacji. Użytkownik widzi ogólny komunikat o błędzie z informacją, że nie ma autoryzacji do uzyskania dostępu do aplikacji i powinien poprosił administratora o pomoc. Jednak często użytkownik nie wie, kto kontaktuje się z nim, aby w aplikacji albo utworzyć nowe konto lokalne. Nawet gdy administrator zostanie powiadomiony, nie zawsze jest to usprawniony proces, który pomaga administratorowi udzielić dostępu i powiadomić ich użytkowników.
 
Przepływ pracy zgody administratora zapewnia administratorom bezpieczny sposób udzielania dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Gdy użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może zapewnić zgody, może wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane za pośrednictwem poczty e-mail do administratorów, którzy zostali wyznaczeni jako recenzenci. Recenzent wykonuje akcję na żądanie, a użytkownik jest powiadamiany o akcji.

Aby można było zatwierdzić żądania, Recenzent musi być administratorem globalnym, administratorem aplikacji w chmurze lub administratorem aplikacji. Recenzent musi mieć już jedną z tych przypisanych ról administratora; po prostu wyznaczanie ich jako recenzenta nie Podnieś poziomu uprawnień.

## <a name="enable-the-admin-consent-workflow"></a>Włączanie przepływu pracy zgody administratora

Aby włączyć przepływ pracy zgody administratora i wybrać recenzentów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.
2. Kliknij pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie. Zostanie otwarte **rozszerzenie Azure Active Directory** .
3. W polu wyszukiwania filtru wpisz "**Azure Active Directory**" i wybierz element **Azure Active Directory** .
4. W menu nawigacji kliknij pozycję **aplikacje dla przedsiębiorstw**. 
5. W obszarze **Zarządzaj** wybierz pozycję **Ustawienia użytkownika**.
6. W obszarze **prośby o zgodę administratora (wersja zapoznawcza)** ustaw, aby **użytkownicy mogli żądać zgody administratora na aplikacje, których nie mogą wyrazić zgody** na **wartość tak**.

   ![Konfigurowanie ustawień przepływu pracy zgody administratora](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Skonfiguruj następujące ustawienia:

   * **Wybierz pozycję Użytkownicy, aby przejrzeć żądania zgody administratora**. Wybierz recenzentów tego przepływu pracy z zestawu użytkowników, którzy mają role Administrator globalny, administrator aplikacji w chmurze i administrator aplikacji.
   * **Wybrani użytkownicy będą otrzymywać powiadomienia e-mail o żądaniach**. Włącza lub wyłącza powiadomienia e-mail dla recenzentów po wysłaniu żądania.  
   * **Wybrani użytkownicy otrzymają przypomnienia o wygaśnięciu żądania**. Włącz lub Wyłącz powiadomienia e-mail z przypomnieniem dla recenzentów, gdy żądanie wkrótce wygaśnie.  
   * **Żądanie zgody wygasa po (w dniach)**. Określ, jak długo żądania pozostają ważne.

7. Wybierz pozycję **Zapisz**. Włączenie tej funkcji może potrwać do godziny.

> [!NOTE]
> Możesz dodawać lub usuwać recenzentów tego przepływu pracy, modyfikując listę **Wybierz recenzentów żądań zgody administratora** . Należy zauważyć, że bieżące ograniczenie tej funkcji polega na tym, że Recenzenci mogą zachować możliwość przeglądania żądań, które zostały wprowadzone, gdy zostały wyszukane jako recenzent.

## <a name="how-users-request-admin-consent"></a>Jak użytkownicy żądają zgody administratora

Po włączeniu przepływu pracy zgody administratora użytkownicy mogą zażądać zatwierdzenia przez administratora dla aplikacji, do której nie ma zgody. Poniższe kroki opisują środowisko użytkownika podczas żądania zatwierdzenia. 

1. Użytkownik próbuje zalogować się do aplikacji.

2. Zostanie wyświetlony komunikat **wymagane zatwierdzenie** . Użytkownik wpisuje uzasadnienie dostępu do aplikacji, a następnie wybiera **żądanie zatwierdzenia**.

   ![Zrzut ekranu przedstawia okno dialogowe wymagane zatwierdzenie, w którym można zażądać zatwierdzenia.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Wiadomość **wysłana przez żądanie** potwierdza, że żądanie zostało przesłane do administratora. Jeśli użytkownik wyśle kilka żądań, tylko pierwsze żądanie zostanie przesłane do administratora.

   ![Zrzut ekranu przedstawia potwierdzenie wysłania żądania.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Użytkownik otrzymuje wiadomość e-mail z powiadomieniem, gdy żądanie zostanie zatwierdzone, odrzucone lub zablokowane. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Przejrzyj prośby o zgodę administratora i podejmij działania

Aby przejrzeć żądania zgody administratora i podjąć działania:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako jeden z zarejestrowanych recenzentów przepływu pracy zgody administratora.
2. Wybierz pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie. Zostanie otwarte **rozszerzenie Azure Active Directory** .
3. W polu wyszukiwania filtru wpisz "**Azure Active Directory**" i wybierz element **Azure Active Directory** .
4. W menu nawigacji kliknij pozycję **aplikacje dla przedsiębiorstw**.
5. W obszarze **działanie** wybierz pozycję **żądania zgody administratora (wersja zapoznawcza)**.

   > [!NOTE]
   > Recenzenci będą widzieć tylko żądania administratora, które zostały utworzone po wyznaczeniu przez Ciebie jako recenzenta.

1. Wybierz żądaną aplikację.
2. Przejrzyj szczegółowe informacje na temat żądania:  

   * Aby zobaczyć, kto żąda dostępu i dlaczego, wybierz kartę **żądane przez** .
   * Aby sprawdzić, jakie uprawnienia są wymagane przez aplikację, wybierz pozycję **Przejrzyj uprawnienia i wyrażanie zgody**.

8. Oceń żądanie i podejmij odpowiednie działanie:

   * **Zatwierdź żądanie**. Aby zatwierdzić żądanie, Udziel uprawnienia administratora aplikacji. Po zatwierdzeniu żądania wszyscy osoby żądające zostaną powiadomieni o udzieleniu im dostępu.  
   * **Odmów żądania**. Aby odmówić żądania, należy podać uzasadnienie, które zostanie przekazane wszystkim zleceniodawcom. Po odmowie żądania wszystkie osoby żądające zostaną powiadomione o odmowie dostępu do aplikacji. Odrzucenie żądania nie uniemożliwi użytkownikom powtórnego żądania zgody administratora na aplikację w przyszłości.  
   * **Zablokuj żądanie**. Aby zablokować żądanie, należy podać uzasadnienie, które zostanie przekazane wszystkim zleceniodawcom. Po zablokowaniu żądania wszystkie osoby żądające otrzymają powiadomienie o odmowie dostępu do aplikacji. Zablokowanie żądania powoduje utworzenie obiektu jednostki usługi dla aplikacji w dzierżawie w stanie wyłączenia. Użytkownicy nie będą mogli prosić o zgodę administratora aplikacji w przyszłości.
 
## <a name="email-notifications"></a>Powiadomienia e-mail
 
W przypadku skonfigurowania wszyscy recenzenci otrzymają powiadomienia e-mail, gdy:

* Utworzono nowe żądanie
* Żądanie wygasło
* Żądanie zbliża się do daty wygaśnięcia  
 
Osoby żądające otrzymają powiadomienia e-mail, gdy:

* Przesyłają one nowe żądanie dostępu
* Ich żądanie wygasło
* Żądanie zostało odrzucone lub zablokowane
* Żądanie zostało zatwierdzone
 
## <a name="audit-logs"></a>Dzienniki inspekcji 
 
W poniższej tabeli przedstawiono scenariusze i wartości inspekcji dostępne dla przepływu pracy zgody administratora. 

> [!NOTE]
> W wszystkich scenariuszach nie ma obecnie kontekstu użytkownika dotyczącego inspekcji. Jest to znane ograniczenie w wersji zapoznawczej.


|Scenariusz  |Usługa inspekcji  |Kategorii inspekcji  |Inspekcja aktywności  |Aktor inspekcji  |Ograniczenia dziennika inspekcji  |
|---------|---------|---------|---------|---------|---------|
|Administrator włączania przepływu pracy żądania zgody        |Przeglądy dostępu           |UserManagement           |Utwórz szablon zasad ładu          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika            |
|Administrator wyłączający przepływ pracy żądania zgody       |Przeglądy dostępu           |UserManagement           |Usuń szablon zasad ładu          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Administrator zaktualizował konfiguracje przepływu pracy zgody        |Przeglądy dostępu           |UserManagement           |Aktualizuj szablon zasad ładu          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Użytkownik końcowy tworzy żądanie zgody administratora dla aplikacji       |Przeglądy dostępu           |Zasady         |Utwórz żądanie           |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Recenzenci zatwierdzający żądanie zgody administratora       |Przeglądy dostępu           |UserManagement           |Zatwierdź wszystkie żądania w usłudze Flow          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika lub identyfikatora aplikacji, dla którego udzielono zgody administratora.           |
|Recenzenci odmawiają żądania zgody administratora       |Przeglądy dostępu           |UserManagement           |Zatwierdź wszystkie żądania w usłudze Flow          |Kontekst aplikacji            | Obecnie nie można znaleźć kontekstu użytkownika aktora, który odmówił żądania zgody administratora          |

## <a name="faq"></a>Często zadawane pytania 

**Po włączeniu tego przepływu pracy, ale podczas testowania funkcji, dlaczego nie mogę zobaczyć nowego monitu o zatwierdzenie, które umożliwia mi żądanie dostępu?**

Po włączeniu tej funkcji może upłynąć do 60 minut, zanim użytkownicy końcowi zobaczą aktualizację. Aby sprawdzić, czy konfiguracja została prawidłowo przeprowadzona, można wyświetlić wartość **EnableAdminConsentRequests** w `https://graph.microsoft.com/beta/settings` interfejsie API.

**Dlaczego nie widzę wszystkich oczekujących żądań jako recenzenta?**

Recenzenci mogą przeglądać tylko te żądania, które zostały utworzone po wyznaczeniu ich jako recenzenta. Dlatego jeśli niedawno dodano Cię jako recenzenta, nie zobaczysz żadnych żądań, które zostały utworzone przed przypisaniem.

**Dlaczego jako recenzent widzimy wiele żądań dla tej samej aplikacji?**
  
Jeśli deweloper aplikacji skonfigurował aplikację do używania statycznej i dynamicznej zgody na żądanie dostępu do danych użytkownika końcowego, zobaczysz dwie żądania zgody administratora. Jedno żądanie reprezentuje uprawnienia statyczne, a drugi reprezentuje uprawnienia dynamiczne.

**Czy jako obiekt żądający można sprawdzić stan mojego żądania?**  

Nie. obecnie osoby żądające mogą pobierać aktualizacje za pośrednictwem powiadomień e-mail.

**Czy jako recenzent jest możliwe zatwierdzenie aplikacji, ale nie dla wszystkich?**
 
Jeśli chodzi o przyznanie zgody administratora i zezwolenie wszystkim użytkownikom w dzierżawie na korzystanie z aplikacji, zalecamy odrzucenie żądania. Następnie ręcznie Udziel zgody administratora przez ograniczenie dostępu do aplikacji przez wymaganie przypisania użytkownika i przypisanie użytkowników lub grup do aplikacji. Aby uzyskać więcej informacji, zobacz [metody przypisywania użytkowników i grup](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, zobacz [Azure Active Directory](../develop/consent-framework.md).

[Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md)

[Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

[Usługa Azure AD w systemie StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)