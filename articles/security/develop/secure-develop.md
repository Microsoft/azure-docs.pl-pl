---
title: Tworzenie bezpiecznych aplikacji na platformie Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, które należy wziąć pod uwagę podczas fazy wdrażania i weryfikacji projektu aplikacji sieci web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 03f5b0124f95465c4a5da5043364a2f5816dae62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685748"
---
# <a name="develop-secure-applications-on-azure"></a>Tworzenie bezpiecznych aplikacji na platformie Azure
W tym artykule przedstawiamy działania zabezpieczeń i kontrole do rozważenia podczas tworzenia aplikacji dla chmury. Pytania zabezpieczające i pojęcia, które należy wziąć pod uwagę podczas fazy wdrażania i weryfikacji cyklu życia programu Microsoft [Security Development (SDL).](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) Celem jest pomoc w definiowaniu działań i usług platformy Azure, których można użyć do opracowania bezpieczniejszej aplikacji.

W tym artykule uwzględnione są następujące fazy SDL:

- Implementacja
- Weryfikacja

## <a name="implementation"></a>Implementacja
Faza wdrażania koncentruje się na ustanowieniu najlepszych praktyk w zakresie wczesnego zapobiegania oraz wykrywaniu i usuwaniu problemów z bezpieczeństwem z kodu.
Załóżmy, że aplikacja będzie używana w sposób, który nie ma zamiaru być używany. Pomaga to chronić przed przypadkowym lub celowym niewłaściwym użyciem aplikacji.

### <a name="perform-code-reviews"></a>Wykonywanie przeglądów kodu

Przed zaewidencjonowaniem kodu należy przeprowadzić [przeglądy kodu,](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) aby zwiększyć ogólną jakość kodu i zmniejszyć ryzyko tworzenia błędów. Za pomocą [programu Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) można zarządzać procesem przeglądu kodu.

### <a name="perform-static-code-analysis"></a>Wykonywanie statycznej analizy kodu

[Analiza kodu statycznego](https://www.owasp.org/index.php/Static_Code_Analysis) (znana również jako *analiza kodu źródłowego)* jest zwykle wykonywana w ramach przeglądu kodu. Statyczna analiza kodu często odnosi się do uruchamiania narzędzi do analizy kodu statycznego w celu znalezienia potencjalnych luk w niepracujących kodach przy użyciu technik, takich jak [sprawdzanie skazy](https://en.wikipedia.org/wiki/Taint_checking) i [analiza przepływu danych.](https://en.wikipedia.org/wiki/Data-flow_analysis)

Usługa Azure Marketplace oferuje [narzędzia deweloperskie,](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) które wykonują statyczną analizę kodu i pomagają w przeglądach kodu.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Sprawdzanie poprawności i dezynfekcji każdego wejścia dla aplikacji

Traktuj wszystkie dane wejściowe jako niezaufane, aby chronić aplikację przed najpopularniejszymi lukami w zabezpieczeniach aplikacji sieci web. Dane niezaufane są nośnikiem ataków iniekcyjnych. Dane wejściowe dla aplikacji zawiera parametry w adresie URL, dane wejściowe od użytkownika, dane z bazy danych lub z interfejsu API i wszystko, co jest przekazywane w tym użytkownik może potencjalnie manipulować. Aplikacja powinna sprawdzić, czy dane są syntaktycznie i semantycznie [prawidłowe,](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) zanim aplikacja użyje danych w jakikolwiek sposób (w tym wyświetlanie ich z powrotem użytkownikowi).

Sprawdź poprawność danych wejściowych na wczesnym etapie przepływu danych, aby upewnić się, że tylko poprawnie utworzone dane wejdą do przepływu pracy. Nie chcesz, aby nieprawidłowo sformułowane dane utrwalały się w bazie danych lub wyzwalały usterkę w składniku podrzędnym.

Czarna lista i umieszczanie na białej liście to dwa ogólne podejścia do sprawdzania poprawności składni wejściowej:

  - Czarna lista próbuje sprawdzić, czy dane wejściowe danego użytkownika nie zawierają zawartości "znanej jako złośliwa".

  - Umieszczanie na białej liście próbuje sprawdzić, czy dane wejściowe danego użytkownika są zgodne z zestawem danych wejściowych "znane dobre". Białej listy opartej na znakach jest formą umieszczania na białej liście, w której aplikacja sprawdza, czy dane wejściowe użytkownika zawierają tylko "znane dobre" znaki lub że dane wejściowe są zgodne ze znanym formatem.
    Na przykład może to obejmować sprawdzenie, czy nazwa użytkownika zawiera tylko znaki alfanumeryczne lub że zawiera dokładnie dwie liczby.

Umieszczanie na białej liście jest preferowanym podejściem do tworzenia bezpiecznego oprogramowania.
Czarna lista jest podatna na błędy, ponieważ nie można myśleć o pełnej liście potencjalnie złych danych wejściowych.

Wykonaj tę pracę na serwerze, a nie po stronie klienta (lub na serwerze i po stronie klienta).

### <a name="verify-your-applications-outputs"></a>Weryfikowanie wyników aplikacji

Wszelkie dane wyjściowe, które są prezentowane wizualnie lub w dokumencie powinny być zawsze zakodowane i zmienione. [Ucieczka](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), znany również jako *kodowanie danych wyjściowych,* służy do zapewnienia, że niezaufane dane nie jest pojazdem do ataku wtrysku. Ucieczka, w połączeniu z sprawdzanie poprawności danych, zapewnia warstwowe zabezpieczenia w celu zwiększenia bezpieczeństwa systemu jako całości.

Ucieczka zapewnia, że wszystko jest wyświetlane jako *wyjście.* Ucieczka pozwala również interpreter wiedzieć, że dane nie są przeznaczone do wykonania, a to zapobiega atakom z pracy. Jest to kolejna powszechna technika ataku o nazwie *skrypty krzyżowe* (XSS).

Jeśli korzystasz z struktury internetowej od strony trzeciej, możesz zweryfikować opcje kodowania danych wyjściowych na stronach internetowych za pomocą [arkusza owasp XSS zapobiegawczynego.](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Używanie zapytań sparametryzowanych podczas kontaktowania się z bazą danych

Nigdy nie należy tworzyć wbudowane zapytanie bazy danych "na bieżąco" w kodzie i wysłać go bezpośrednio do bazy danych. Złośliwy kod wstawiony do aplikacji może potencjalnie spowodować kradzież, wymazanie lub zmodyfikowanie bazy danych. Aplikacja może również służyć do uruchamiania złośliwych poleceń systemu operacyjnego w systemie operacyjnym, w którym znajduje się baza danych.

Zamiast tego należy użyć sparametryzowanych zapytań lub procedur przechowywanych. Korzystając z kwerend sparametryzowanych, można wywołać procedurę z kodu bezpiecznie i przekazać go ciąg bez obawy, że będzie traktowany jako część instrukcji kwerendy.

### <a name="remove-standard-server-headers"></a>Usuwanie standardowych nagłówków serwera

Nagłówki takie jak Serwer, X-Powered-By i X-AspNet-Version ujawniają informacje o serwerze i podstawowych technologiach. Zaleca się wyłączenie tych nagłówków, aby uniknąć pobierania odcisków palców od aplikacji.
Zobacz [usuwanie standardowych nagłówków serwerów w witrynach sieci Web platformy Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segreguj dane produkcyjne

Dane produkcyjne lub "prawdziwe" dane nie powinny być wykorzystywane do opracowywania, testowania ani do innych celów niż zamierzone przez firmę. Do wszystkich programów deweloperskich i testowych należy użyć zamaskowanego[(zanonimizowany)](https://en.wikipedia.org/wiki/Data_anonymization)zestawu danych.

Oznacza to, że mniej osób ma dostęp do prawdziwych danych, co zmniejsza powierzchnię ataku. Oznacza to również, że mniej pracowników widzi dane osobowe, co eliminuje potencjalne naruszenie poufności.

### <a name="implement-a-strong-password-policy"></a>Wdrażanie silnej zasady haseł

Aby bronić się przed zgadywaniem opartym na brutalnej sile i słowniku, należy zaimplementować zasadę silnego hasła, aby upewnić się, że użytkownicy tworzą złożone hasło (na przykład 12 znaków minimalnej długości i wymagających znaków alfanumerycznej i specjalnej).

Za pomocą struktury tożsamości można tworzyć i wymuszać zasady haseł. Usługa Azure AD B2C ułatwia zarządzanie hasłami, udostępniając [wbudowane zasady,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [samoobsługowe resetowanie haseł](../../active-directory-b2c/user-flow-self-service-password-reset.md)i inne.

Aby chronić się przed atakami na konta domyślne, sprawdź, czy wszystkie klucze i hasła są wymienne i czy są generowane lub zastępowane po zainstalowaniu zasobów.

Jeśli aplikacja musi automatycznie generować hasła, upewnij się, że wygenerowane hasła są losowe i że mają wysoką entropię.

### <a name="validate-file-uploads"></a>Sprawdzanie poprawności przekazywania plików

Jeśli aplikacja zezwala na [przesyłanie plików,](https://www.owasp.org/index.php/Unrestricted_File_Upload)należy wziąć pod uwagę środki ostrożności, które można podjąć dla tej ryzykownej działalności. Pierwszym krokiem w wielu ataków jest uzyskanie jakiegoś złośliwego kodu do systemu, który jest atakowany. Użycie przekazywania plików pomaga osobie atakującej to osiągnąć. OWASP oferuje rozwiązania do sprawdzania poprawności pliku, aby upewnić się, że plik, który przesyłasz, jest bezpieczny.

Ochrona przed złośliwym oprogramowaniem pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Można zainstalować program [Microsoft Antimalware](../fundamentals/antimalware.md) lub rozwiązanie do ochrony punktów końcowych partnera firmy Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)i [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

[Ochrona przed złośliwym oprogramowaniem firmy Microsoft](../fundamentals/antimalware.md) zawiera funkcje, takie jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowanie złośliwego oprogramowania, aktualizacje podpisów, aktualizacje aparatu, raportowanie próbek i zbieranie zdarzeń wykluczenia. Rozwiązania microsoft antimalware i partnerów z [usługą Azure Security Center](../../security-center/security-center-partner-integration.md) ułatwiają wdrażanie i wbudowane wykrywania (alerty i zdarzenia).

### <a name="dont-cache-sensitive-content"></a>Nie buforuj poufnych treści

Nie buforuj poufnych treści w przeglądarce. Przeglądarki mogą przechowywać informacje dotyczące buforowania i historii. Pliki buforowane są przechowywane w folderze takim jak folder Tymczasowe pliki internetowe, w przypadku programu Internet Explorer. Gdy te strony są ponownie odwoływane, przeglądarka wyświetla strony z jego pamięci podręcznej. Jeśli poufne informacje (adres, dane karty kredytowej, numer ubezpieczenia społecznego, nazwa użytkownika) są wyświetlane użytkownikowi, informacje mogą być przechowywane w pamięci podręcznej przeglądarki i można je odzyskać, sprawdzając pamięć podręczną przeglądarki lub po prostu naciskając przycisk **Wstecz** przeglądarki.

## <a name="verification"></a>Weryfikacja
Faza weryfikacji obejmuje kompleksowe wysiłki w celu zapewnienia, że kod spełnia zasady bezpieczeństwa i prywatności, które zostały ustanowione w poprzednich fazach.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Znajdowanie i naprawianie luk w zależnościach aplikacji

Skanowanie aplikacji i jej bibliotek zależnych w celu zidentyfikowania wszystkich znanych składników podlegać usy w trudnej sytuacji. Produkty, które są dostępne do wykonania tego skanowania obejmują [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)i [Black Duck](https://www.blackducksoftware.com/).

Skanowanie luk w zabezpieczeniach obsługiwane przez [tinfoil security](https://www.tinfoilsecurity.com/) jest dostępne dla aplikacji sieci Web usługi Azure App Service. [Skanowanie tinfoil Security za pośrednictwem usługi App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferuje deweloperom i administratorom szybki, zintegrowany i ekonomiczny sposób wykrywania i usuwania luk w zabezpieczeniach, zanim złośliwy aktor będzie mógł z nich skorzystać.

> [!NOTE]
> Można również [zintegrować zabezpieczenia folii tinfoil z usługą Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Integracja zabezpieczeń folii tinfoil z usługą Azure AD zapewnia następujące korzyści:
>  - W usłudze Azure AD można kontrolować, kto ma dostęp do tinfoil zabezpieczeń.
>  - Użytkownicy mogą być automatycznie zalogowany do Tinfoil Security (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
>  - Możesz zarządzać kontami w jednej, centralnej lokalizacji, w witrynie Azure portal.

### <a name="test-your-application-in-an-operating-state"></a>Testowanie aplikacji w stanie roboczym

Dynamiczne testowanie zabezpieczeń aplikacji (DAST) to proces testowania aplikacji w stanie operacyjnym w celu znalezienia luk w zabezpieczeniach. Narzędzia DAST analizują programy podczas wykonywania, aby znaleźć luki w zabezpieczeniach, takie jak uszkodzenie pamięci, niezabezpieczona konfiguracja serwera, skrypty między witrynami, problemy z uprawnieniami użytkownika, iniekcja SQL i inne krytyczne problemy z bezpieczeństwem.

DAST różni się od statycznych testów zabezpieczeń aplikacji (SAST). Narzędzia SAST analizują kod źródłowy lub skompilowane wersje kodu, gdy kod nie jest wykonywany w celu znalezienia wad zabezpieczeń.

Wykonaj DAST, najlepiej z pomocą pracownika ochrony [(testera penetracji](../fundamentals/pen-testing.md) lub asesora podatności). Jeśli specjalista od zabezpieczeń nie jest dostępny, możesz samodzielnie wykonać dast za pomocą internetowego skanera proxy i szkolenia. Podłącz skaner DAST na wczesnym etapie, aby upewnić się, że nie wprowadzisz oczywistych problemów z zabezpieczeniami do kodu. Zobacz witrynę [OWASP,](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) aby uzyskać listę skanerów luk w zabezpieczeniach aplikacji sieci Web.

### <a name="perform-fuzz-testing"></a>Wykonaj testy fuzz

W [testach fuzz,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)można wywołać awarię programu przez celowe wprowadzenie nieprawidłowo sformułowanych lub losowych danych do aplikacji. Indukowanie awarii programu pomaga ujawnić potencjalne problemy z zabezpieczeniami przed wydaniem aplikacji.

[Wykrywanie zagrożeń bezpieczeństwa](https://docs.microsoft.com/security-risk-detection/) to unikalna usługa testowania rozmytego firmy Microsoft służąca do znajdowania błędów o krytycznym znaczeniu dla bezpieczeństwa w oprogramowaniu.

### <a name="conduct-attack-surface-review"></a>Przeprowadzanie przeglądu powierzchni ataku

Przeglądanie powierzchni ataku po zakończeniu kodu pomaga upewnić się, że wszystkie zmiany projektu lub implementacji aplikacji lub systemu zostały uwzględnione. Pomaga zapewnić, że wszystkie nowe wektory ataku, które zostały utworzone w wyniku zmian, w tym modeli zagrożeń, został sprawdzony i złagodzone.

Można utworzyć obraz powierzchni ataku, skanując aplikację. Firma Microsoft oferuje narzędzie do analizy powierzchni ataku o nazwie [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Można wybierać spośród wielu komercyjnych narzędzi lub usług do testowania dynamicznego i skanowania luk w zabezpieczeniach, w tym [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)i [w3af](http://w3af.sourceforge.net/). Te narzędzia skanujące indeksują aplikację i mapują części aplikacji, które są dostępne w internecie. Możesz również przeszukać witrynę Azure Marketplace w poszukiwaniu podobnych [narzędzi deweloperskich.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>Przeprowadzanie testów penetracji zabezpieczeń

Upewnienie się, że aplikacja jest bezpieczna, jest równie ważne, jak testowanie innych funkcji. Spraw, aby [testy penetracyjne](../fundamentals/pen-testing.md) były standardową częścią procesu kompilacji i wdrażania. Zaplanuj regularne testy zabezpieczeń i skanowanie luk w zabezpieczeniach wdrożonych aplikacji oraz monitoruj otwarte porty, punkty końcowe i ataki.

### <a name="run-security-verification-tests"></a>Uruchamianie testów weryfikacji zabezpieczeń

[Zestaw Secure DevOps dla platformy Azure](https://azsk.azurewebsites.net/index.html) (AzSK) zawiera svts dla wielu usług platformy Azure. Te SVTs okresowo, aby upewnić się, że subskrypcja platformy Azure i różne zasoby, które składają się na aplikację są w bezpiecznym stanie. Można również zautomatyzować te testy przy użyciu funkcji ciągłej integracji/ciągłego wdrażania (CI/CD) rozszerzenia AzSK, która sprawia, że SVTs dostępne jako rozszerzenie programu Visual Studio.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zaleca się kontrolki zabezpieczeń i działania, które mogą pomóc w projektowaniu i wdrażaniu bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Wdrażanie bezpiecznych aplikacji](secure-deploy.md)
