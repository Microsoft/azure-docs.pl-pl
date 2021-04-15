---
title: Konfigurowanie przepływu pracy zgody administratora — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować dla użytkowników końcowych sposób żądania dostępu do aplikacji, które wymagają zgody administratora.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: iangithinji
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9811c3d1833a02ad3cbaf22b9f0b31fd2da5bb6d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375189"
---
# <a name="configure-the-admin-consent-workflow"></a>Konfigurowanie przepływu pracy wyrażania zgody przez administratora

W tym artykule opisano sposób włączania funkcji przepływu pracy wyrażania zgody przez administratora, która umożliwia użytkownikom końcowych żądanie dostępu do aplikacji wymagających zgody administratora.

Bez przepływu pracy zgody administratora użytkownik w dzierżawie, w której zgoda użytkownika jest wyłączona, zostanie zablokowany podczas próby uzyskania dostępu do dowolnej aplikacji, która wymaga uprawnień dostępu do danych organizacji. Użytkownik zobaczy ogólny komunikat o błędzie informujący, że nie ma autoryzacji dostępu do aplikacji, i powinien poprosić administratora o pomoc. Jednak często użytkownik nie wie, z kim się skontaktować, dlatego poda lub utworzy nowe konto lokalne w aplikacji. Nawet jeśli administrator zostanie powiadomiony, nie zawsze istnieje usprawniony proces ułatwiający administratorowi udzielanie dostępu i powiadamianie użytkowników.
 
Przepływ pracy zgody administratora zapewnia administratorom bezpieczny sposób udzielania dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Gdy użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może wyrazić zgody, może wysłać żądanie zatwierdzenia przez administratora. Żądanie jest wysyłane pocztą e-mail do administratorów wyznaczonych jako recenzentzy. Recenzent podejmuje działania dotyczące żądania, a użytkownik jest powiadamiany o akcji.

Aby zatwierdzić żądania, recenzent musi być administratorem globalnym, administratorem aplikacji w chmurze lub administratorem aplikacji. Recenzent musi już mieć przypisaną jedną z tych ról administratora. Samo wyznaczenie ich jako recenzenta nie podnosi ich uprawnień.

## <a name="enable-the-admin-consent-workflow"></a>Włączanie przepływu pracy zgody administratora

Aby włączyć przepływ pracy zgody administratora i wybrać recenzentów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.
2. Kliknij **pozycję Wszystkie usługi** w górnej części menu nawigacji po lewej stronie. Zostanie **Azure Active Directory Rozszerzenie** aplikacji.
3. W polu wyszukiwania filtru wpisz **"Azure Active Directory"** i wybierz **Azure Active Directory** element.
4. W menu nawigacji kliknij pozycję **Aplikacje dla przedsiębiorstw.** 
5. W **obszarze Zarządzaj** wybierz pozycję Ustawienia **użytkownika.**
6. W **obszarze Żądania zgody administratora** ustaw opcję Użytkownicy mogą żądać zgody administratora dla aplikacji, na które nie mogą wyrazić **zgody, na** wartość **Tak.**

   ![Konfigurowanie ustawień przepływu pracy zgody administratora](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Skonfiguruj następujące ustawienia:

   * **Wybierz użytkowników, aby przejrzeć żądania zgody administratora.** Wybierz recenzentów dla tego przepływu pracy z zestawu użytkowników, którzy mają role administratora globalnego, administratora aplikacji w chmurze i administratora aplikacji.
   * **Wybrani użytkownicy będą otrzymywać powiadomienia e-mail dotyczące żądań**. Włącz lub wyłącz powiadomienia e-mail do recenzentów, gdy zostanie wykonane żądanie.  
   * **Wybrani użytkownicy otrzymają przypomnienia o wygaśnięciu żądania.** Włącz lub wyłącz powiadomienia e-mail z przypomnieniem do recenzentów, gdy żądanie wygaśnie.  
   * **Żądanie zgody wygasa po (dniach)**. Określ, jak długo żądania pozostają prawidłowe.

7. Wybierz pozycję **Zapisz**. Może upłynieć godzina, aż funkcja zostanie włączona.

> [!NOTE]
> Recenzentów dla tego przepływu pracy można dodawać lub usuwać, modyfikując listę weryfikatorów Wybierz żądania zgody **administratora.** Należy pamiętać, że bieżące ograniczenie tej funkcji jest takie, że recenzentzy mogą zachować możliwość przeglądania żądań, które zostały wprowadzone, gdy zostały wyznaczone jako recenzent.

## <a name="how-users-request-admin-consent"></a>Jak użytkownicy żądają zgody administratora

Po włączeniu przepływu pracy zgody administratora użytkownicy mogą zażądać zatwierdzenia przez administratora dla aplikacji, na które nie mają autoryzacji. W poniższych krokach opisano środowisko użytkownika podczas żądania zatwierdzenia. 

1. Użytkownik próbuje zalogować się do aplikacji.

2. Zostanie **wyświetlony komunikat Wymagane** zatwierdzenie. Użytkownik typuje uzasadnienie konieczności uzyskania dostępu do aplikacji, a następnie wybiera pozycję **Zażądaj zatwierdzenia.**

   ![Zrzut ekranu przedstawia okno dialogowe Wymagane zatwierdzenie, w którym można zażądać zatwierdzenia.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Komunikat **Wysłane żądanie** potwierdza, że żądanie zostało przesłane do administratora. Jeśli użytkownik wyśle kilka żądań, tylko pierwsze żądanie zostanie przesłane do administratora.

   ![Zrzut ekranu przedstawia potwierdzenie Wysłane żądanie.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Gdy żądanie zostanie zatwierdzone, odrzucone lub zablokowane, użytkownik otrzyma powiadomienie e-mail. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Przeglądanie i działanie w przypadku żądań zgody administratora

Aby przejrzeć żądania zgody administratora i podjąć działania:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako jeden z zarejestrowanych recenzentów przepływu pracy wyrażania zgody administratora.
2. Wybierz **pozycję Wszystkie usługi** w górnej części menu nawigacji po lewej stronie. Zostanie **otwarte Azure Active Directory Rozszerzenie** aplikacji.
3. W polu wyszukiwania filtru wpisz **"Azure Active Directory"** i wybierz **Azure Active Directory** element.
4. W menu nawigacji kliknij pozycję **Aplikacje dla przedsiębiorstw.**
5. W **obszarze Działanie** wybierz pozycję Żądania zgody **administratora.**

   > [!NOTE]
   > Recenzentzy będą widzieć tylko żądania administratora utworzone po ich wyznaczeniu jako recenzenta.

1. Wybierz żądaną aplikację.
2. Przejrzyj szczegółowe informacje o żądaniu:  

   * Aby zobaczyć, kto żąda dostępu i dlaczego, wybierz **kartę Żądane** przez.
   * Aby zobaczyć, jakie uprawnienia są żądane przez aplikację, wybierz pozycję **Przejrzyj uprawnienia i wyrażaj zgodę.**

8. Oceń żądanie i podjąć odpowiednie działania:

   * **Zatwierdź żądanie**. Aby zatwierdzić żądanie, udzielić zgody administratora aplikacji. Po zatwierdzeniu żądania wszyscy żądające są powiadamiani o tym, że przyznano im dostęp.  
   * **Odmów żądania**. Aby odrzucić żądanie, należy podać uzasadnienie, które zostanie podane wszystkim żądających. Po odmowie żądania wszyscy żądające są powiadamiani o odmowie dostępu do aplikacji. Odmówienie żądania nie uniemożliwi użytkownikom żądania zgody administratora na aplikację ponownie w przyszłości.  
   * **Blokuj żądanie**. Aby zablokować żądanie, należy podać uzasadnienie, które zostanie podane wszystkim żądających. Po zablokowaniu żądania wszyscy żądające są powiadamiani o odmowie dostępu do aplikacji. Zablokowanie żądania powoduje utworzenie obiektu jednostki usługi dla aplikacji w dzierżawie w stanie wyłączonym. W przyszłości użytkownicy nie będą mogli zażądać zgody administratora na aplikację.
 
## <a name="email-notifications"></a>Powiadomienia e-mail
 
W przypadku skonfigurowania wszyscy recenzentzy będą otrzymywać powiadomienia e-mail, gdy:

* Utworzono nowe żądanie
* Żądanie wygasło
* Żądanie zbliża się do daty wygaśnięcia  
 
Żądających będą otrzymywać powiadomienia e-mail, gdy:

* Przesyłają nowe żądanie dostępu
* Żądanie wygasło
* Żądanie zostało odrzucone lub zablokowane
* Ich żądanie zostało zatwierdzone
 
## <a name="audit-logs"></a>Dzienniki inspekcji 
 
W poniższej tabeli przedstawiono scenariusze i wartości inspekcji dostępne dla przepływu pracy zgody administratora.

|Scenariusz  |Usługa inspekcji  |Kategorii inspekcji  |Działanie inspekcji  |Audit Actor  |Ograniczenia dziennika inspekcji  |
|---------|---------|---------|---------|---------|---------|
|Administrator włączający przepływ pracy żądania zgody        |Przeglądy dostępu           |UserManagement           |Tworzenie szablonu zasad ładu          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika            |
|Administrator wyłącza przepływ pracy żądania zgody       |Przeglądy dostępu           |UserManagement           |Usuwanie szablonu zasad nadzoru          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Administrator aktualizuje konfiguracje przepływu pracy wyrażania zgody        |Przeglądy dostępu           |UserManagement           |Aktualizowanie szablonu zasad ładu          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Użytkownik końcowy tworzący żądanie zgody administratora dla aplikacji       |Przeglądy dostępu           |Zasady         |Tworzenie żądania           |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Recenzentzy zatwierdzający żądanie zgody administratora       |Przeglądy dostępu           |UserManagement           |Zatwierdzanie wszystkich żądań w przepływie biznesowym          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika ani identyfikatora aplikacji, do których udzielono zgody administratora.           |
|Recenzentzy odrzucają żądanie zgody administratora       |Przeglądy dostępu           |UserManagement           |Zatwierdzanie wszystkich żądań w przepływie biznesowym          |Kontekst aplikacji            | Obecnie nie można znaleźć kontekstu użytkownika aktora, który odrzucił żądanie zgody administratora          |

## <a name="faq"></a>Często zadawane pytania 

**Ten przepływ pracy został włączony, ale podczas testowania funkcji nie widzę nowego monitu "Wymagane zatwierdzenie", który umożliwia zażądanie dostępu?**

Po włączeniu tej funkcji użytkownicy końcowi mogą zobaczyć aktualizację dopiero po 60 minutach. Możesz sprawdzić, czy konfiguracja została prawidłowo wzięte pod uwagę, wyświetlając wartość **EnableAdminConsentRequests** w interfejsie `https://graph.microsoft.com/beta/settings` API.

**Dlaczego jako recenzent nie widzę wszystkich oczekujących żądań?**

Recenzentzy widzą tylko żądania administratora utworzone po ich wyznaczeniu jako recenzenta. Dlatego jeśli ostatnio dodano Cię jako recenzenta, nie zobaczysz żadnych żądań, które zostały utworzone przed przypisaniem.

**Dlaczego jako recenzent widzę wiele żądań dla tej samej aplikacji?**
  
Jeśli deweloper aplikacji skonfigurował aplikację do używania statycznej i dynamicznej zgody na żądanie dostępu do danych użytkownika końcowego, zobaczysz dwa żądania zgody administratora. Jedno żądanie reprezentuje uprawnienia statyczne, a drugie reprezentuje uprawnienia dynamiczne.

**Czy mogę sprawdzić stan mojego żądania jako żądać?**  

Nie, na razie żądających można uzyskać aktualizacje tylko za pośrednictwem powiadomień e-mail.

**Czy jako recenzent możesz zatwierdzić aplikację, ale nie dla wszystkich?**
 
Jeśli nie chcesz udzielać zgody administratora i zezwalać wszystkim użytkownikom w dzierżawie na korzystanie z aplikacji, zalecamy odmowę żądania. Następnie ręcznie udzielić zgody administratora, ograniczając dostęp do aplikacji przez wymaganie przypisania użytkownika i przypisanie użytkowników lub grup do aplikacji. Aby uzyskać więcej informacji, [zobacz Metody przypisywania użytkowników i grup](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wyrażania zgody na aplikacje, [zobacz Azure Active Directory platformę wyrażania zgody.](../develop/consent-framework.md)

[Konfigurowanie sposobu, w jaki użytkownicy końcowi wyrażają zgodę na aplikacje](configure-user-consent.md)

[Udzielanie zgody administratora całej dzierżawy dla aplikacji](grant-admin-consent.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/v2-permissions-and-consent.md)

[Usługa Azure AD w witrynie Microsoft Q&A](/answers/topics/azure-active-directory.html)
