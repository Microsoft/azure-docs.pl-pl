---
title: Opracowywanie bezpiecznych aplikacji na Microsoft Azure
description: W tym artykule opisano najlepsze rozwiązania, które należy wziąć pod uwagę podczas fazy implementacji i weryfikacji projektu aplikacji sieci Web.
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
ms.openlocfilehash: 52c93bef4529f27ad38677f17209e7b48e997368
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548448"
---
# <a name="develop-secure-applications-on-azure"></a>Tworzenie bezpiecznych aplikacji na platformie Azure
W tym artykule opisano działania związane z bezpieczeństwem i kontrolki, które należy wziąć pod uwagę podczas opracowywania aplikacji w chmurze. Pytania zabezpieczające i pojęcia, które należy wziąć pod uwagę podczas fazy wdrażania i weryfikacji [cyklu życia programu Microsoft Security Development (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) . Celem jest ułatwienie zdefiniowania działań i usług platformy Azure, których można użyć do tworzenia bezpieczniejszej aplikacji.

Następujące fazy SDL zostały omówione w tym artykule:

- Implementacja
- Weryfikacja

## <a name="implementation"></a>Implementacja
Celem fazy implementacji jest ustanowienie najlepszych rozwiązań w zakresie wczesnego zapobiegania oraz wykrywanie i usuwanie problemów z zabezpieczeniami w kodzie.
Załóżmy, że aplikacja zostanie użyta w sposób, który nie był używany. Pomaga to chronić przed przypadkowym lub zamierzonym nieprawidłowym użyciem aplikacji.

### <a name="perform-code-reviews"></a>Wykonaj przeglądy kodu

Przed zapisaniem kodu Przeprowadź [przeglądy kodu](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) w celu zwiększenia ogólnej jakości kodu i zmniejszenia ryzyka związanego z tworzeniem błędów. Możesz użyć [programu Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs) , aby zarządzać procesem przeglądu kodu.

### <a name="perform-static-code-analysis"></a>Wykonywanie statycznej analizy kodu

[Statyczna analiza kodu](https://owasp.org/www-community/controls/Static_Code_Analysis) (znana również jako *Analiza kodu źródłowego*) jest zwykle wykonywana w ramach przeglądu kodu. Statyczna analiza kodu często dotyczy uruchamiania narzędzi do analizy kodu statycznego, aby znaleźć potencjalne luki w zabezpieczeniach w nieuruchomionym kodzie przy użyciu technik takich jak [Sprawdzanie](https://en.wikipedia.org/wiki/Taint_checking) zmian i [Analiza przepływu danych](https://en.wikipedia.org/wiki/Data-flow_analysis).

Portal Azure Marketplace oferuje [Narzędzia programistyczne](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) , które wykonują analizę kodu statycznego i pomagają w przeglądach kodu.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Weryfikuj i oczyszczaj wszystkie dane wejściowe dla aplikacji

Traktuj wszystkie dane wejściowe jako niezaufane, aby chronić aplikację przed Najczęściej spotykanymi lukami w zabezpieczeniach aplikacji sieci Web. Niezaufane dane to pojazd służący do ataków iniekcji. Dane wejściowe dla aplikacji obejmują parametry w adresie URL, dane wejściowe od użytkownika, danych z bazy danych lub interfejsu API, a także wszystkie elementy, które są przesyłane przez użytkownika, mogą potencjalnie manipulować. Aplikacja powinna [sprawdzać](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) , czy dane są składniowo i semantycznie prawidłowe, zanim aplikacja korzysta z danych w dowolny sposób (w tym wyświetlanie jej z powrotem do użytkownika).

Sprawdź poprawność danych wejściowych w przepływie danych, aby upewnić się, że przepływ pracy będzie miał tylko prawidłowo uformowane dane. Nie ma potrzeby utrwalania źle sformułowanych danych w bazie danych ani wyzwalania wadliwych składników w składniku podrzędnym.

Blocklisting i allowlisting są dwoma ogólnymi podejściami do wykonywania walidacji składni danych wejściowych:

  - Blocklisting próbuje sprawdzić, czy dane dane wejściowe użytkownika nie zawierają zawartości "znana jako złośliwe".

  - Allowlisting próbuje sprawdzić, czy dane wejściowe użytkownika są zgodne z zestawem danych wejściowych "znane dobre". Allowlisting oparty na znakach to forma allowlisting, w której aplikacja sprawdza, czy dane wejściowe użytkownika zawierają tylko znaki "znane dobre", czy dane wejściowe są zgodne ze znanym formatem.
    Na przykład może to oznaczać, że nazwa użytkownika zawiera tylko znaki alfanumeryczne lub że zawiera dokładnie dwie liczby.

Allowlisting jest preferowanym podejściem do tworzenia bezpiecznego oprogramowania.
Blocklisting podatne na błędy, ponieważ nie można myśleć o kompletnej liście potencjalnie nieprawidłowych danych wejściowych.

Wykonaj tę czynność na serwerze, a nie po stronie klienta (lub na serwerze i po stronie klienta).

### <a name="verify-your-applications-outputs"></a>Weryfikowanie danych wyjściowych aplikacji

Wszystkie dane wyjściowe, które można przedstawić wizualizację lub w dokumencie, powinny być zawsze kodowane i wyprowadzane. [Ucieczki](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), znane także jako *kodowanie danych wyjściowych*, służy do zapewnienia, że niezaufane dane nie są pojazdem do ataku polegającego na iniekcji. Ucieczki, w połączeniu z walidacją danych, zapewnia ochronę warstwową, aby zwiększyć bezpieczeństwo systemu jako całości.

Ucieczki upewnia się, że wszystkie elementy są wyświetlane jako *dane wyjściowe.* Ucieczki pozwala również interpreterowi wiedzieć, że dane nie są przeznaczone do wykonania i uniemożliwiają one ataki. Jest to inna typowa technika ataków nazywana *skryptami między lokacjami* (XSS).

Jeśli używasz platformy sieci Web od innej firmy, możesz sprawdzić opcje kodowania danych wyjściowych w witrynach sieci Web za pomocą [arkusza OWASP XSS zapobiegania Ściągawka](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Używanie sparametryzowanych zapytań podczas kontaktowania się z bazą danych

Nigdy nie twórz wbudowanej kwerendy bazy danych "na bieżąco" w kodzie i wysyłaj ją bezpośrednio do bazy danych. Złośliwy kod wstawiony do aplikacji może potencjalnie spowodować kradzież, wymazania lub zmodyfikowania bazy danych. Aplikacja może również służyć do uruchamiania złośliwych poleceń systemu operacyjnego w systemie operacyjnym, który obsługuje bazę danych.

Zamiast tego należy użyć sparametryzowanych zapytań lub procedur składowanych. W przypadku korzystania z zapytań parametrycznych można bezpiecznie wywołać procedurę z kodu i przekazać ją do ciągu bez obaw, że będzie on traktowany jako część instrukcji zapytania.

### <a name="remove-standard-server-headers"></a>Usuń nagłówki serwera standardowego

Nagłówki, takie jak Server, X-by i X-AspNet-Version, ujawniają informacje o serwerze i podstawowych technologiach. Zaleca się, aby pominąć te nagłówki, aby uniknąć używania odcisku palca aplikacji.
Zobacz [usuwanie nagłówków standardowego serwera w usłudze Azure Websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segregowanie danych produkcyjnych

Dane produkcyjne lub dane realowe nie powinny być używane do programowania, testowania ani żadnych innych celów niż to, czego dotyczy firma. Do wszystkich wdrożeń i testowania należy używać zestawu danych z maską ([anonimowe](https://en.wikipedia.org/wiki/Data_anonymization)).

Oznacza to, że mniejsza liczba osób ma dostęp do Twoich prawdziwych danych, co zmniejsza obszar narażony na ataki. Oznacza to również, że mniejsza liczba pracowników widzi dane osobowe, co eliminuje potencjalne naruszenie poufności.

### <a name="implement-a-strong-password-policy"></a>Zaimplementuj zasady silnego hasła

Aby chronić przed rozłożeniem i zgadywaniem na podstawie słownika, należy zaimplementować zasady silnego hasła, aby upewnić się, że użytkownicy tworzą złożone hasło (na przykład 12 znaków i wymagające znaków alfanumerycznych i specjalnych).

Możesz użyć struktury tożsamości do tworzenia i wymuszania zasad haseł. Azure AD B2C ułatwia zarządzanie hasłami, zapewniając [wbudowane zasady](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), samoobsługowe [resetowanie haseł](../../active-directory-b2c/user-flow-self-service-password-reset.md)i wiele innych.

Aby chronić przed atakami na kontach domyślnych, należy sprawdzić, czy wszystkie klucze i hasła są wymienne i czy są one generowane lub zastępowane po zainstalowaniu zasobów.

Jeśli aplikacja musi automatycznie generować hasła, należy się upewnić, że wygenerowane hasła są losowe i mają wysoką entropię.

### <a name="validate-file-uploads"></a>Weryfikuj operacje przekazywania plików

Jeśli aplikacja zezwala na [przekazywanie plików](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), należy wziąć pod uwagę środki ostrożności, które można podjąć w celu zapewnienia ryzykownej aktywności. Pierwszym krokiem w wielu atakach jest uzyskanie złośliwego kodu w systemie, który jest w trakcie ataku. Zastosowanie przekazywania plików pomaga atakującemu. OWASP oferuje rozwiązania do walidacji pliku, aby upewnić się, że przekazywany plik jest bezpieczny.

Ochrona przed złośliwym oprogramowaniem pomaga identyfikować i usuwać wirusy, programy szpiegujące oraz inne złośliwe oprogramowanie. Można zainstalować [oprogramowanie Microsoft chroniące przed złośliwym kodem](../fundamentals/antimalware.md) lub rozwiązanie Endpoint Protection partnera firmy Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)i [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

[Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft](../fundamentals/antimalware.md) oferuje takie funkcje jak ochrona w czasie rzeczywistym, zaplanowane skanowanie, korygowanie złośliwego oprogramowania, aktualizacje sygnatur, aktualizacje aparatu, raportowanie przykładów i zbieranie zdarzeń wykluczania. Możesz zintegrować rozwiązania firmy Microsoft chroniące przed złośliwym oprogramowaniem i partnerzy [Azure Security Center](../../security-center/security-center-partner-integration.md) , aby ułatwić wdrażanie i wbudowane wykrywanie (alerty i zdarzenia).

### <a name="dont-cache-sensitive-content"></a>Nie Buforuj zawartości poufnej

Nie Buforuj poufnej zawartości w przeglądarce. Przeglądarki mogą przechowywać informacje o pamięci podręcznej i historii. Buforowane pliki są przechowywane w folderze, takim jak folder Temporary Internet Files, w przypadku programu Internet Explorer. Gdy te strony są ponownie określane, przeglądarka wyświetla strony z jej pamięci podręcznej. Jeśli użytkownik wyświetli informacje poufne (adres, szczegóły karty kredytowej, numer ubezpieczenia społecznego, nazwa użytkownika), informacje mogą być przechowywane w pamięci podręcznej przeglądarki i można je pobrać, przeglądając pamięć podręczną przeglądarki lub po prostu naciskając przycisk **Wstecz** w przeglądarce.

## <a name="verification"></a>Weryfikacja
Faza weryfikacji obejmuje kompleksowe wysiłki w celu upewnienia się, że kod spełnia wymagania w zakresie bezpieczeństwa i ochrony prywatności, które zostały określone w poprzednich fazach.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Znajdowanie i rozwiązywanie luk w zabezpieczeniach zależności aplikacji

Przeskanujesz aplikację i jej biblioteki zależne, aby zidentyfikować wszystkie znane składniki zagrożone. Produkty, które są dostępne do wykonania tego skanowania, obejmują [OWASPą kontrolę zależności](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/)i [czarną kaczkę](https://www.blackducksoftware.com/).

Skanowanie luk w [zabezpieczeniach obsługiwane przez usługa TINFOIL Security](https://www.tinfoilsecurity.com/) jest dostępne dla Web Apps Azure App Service. [Usługa TINFOIL skanowania zabezpieczeń dzięki App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferuje deweloperom i administratorom szybki, zintegrowany i ekonomiczny sposób odnajdywania i rozwiązywania luk w zabezpieczeniach, zanim złośliwy aktor nie będzie mógł korzystać z nich.

> [!NOTE]
> Możesz także [zintegrować zabezpieczenia usługa TINFOIL z usługą Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Integracja zabezpieczeń usługa TINFOIL z usługą Azure AD zapewnia następujące korzyści:
>  - W usłudze Azure AD można kontrolować, kto ma dostęp do zabezpieczeń usługa TINFOIL.
>  - Użytkownicy mogą być automatycznie zarejestrowani w usłudze Usługa TINFOIL Security (Logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
>  - Kontami można zarządzać w jednej, centralnej lokalizacji, Azure Portal.

### <a name="test-your-application-in-an-operating-state"></a>Testowanie aplikacji w stanie operacyjnym

Dynamiczne testowanie zabezpieczeń aplikacji (DAST) to proces testowania aplikacji w stanie operacyjnym w celu znalezienia luk w zabezpieczeniach. Narzędzia DAST analizują programy podczas wykonywania, aby znaleźć luki w zabezpieczeniach, takie jak uszkodzenie pamięci, niezabezpieczona konfiguracja serwera, skrypty między lokacjami, problemy z uprawnieniami użytkowników, iniekcja kodu SQL i inne krytyczne problemy z bezpieczeństwem.

DAST różni się od statycznego testowania zabezpieczeń aplikacji (SAST). Narzędzia SAST analizują kod źródłowy lub skompilowane wersje kodu, gdy kod nie jest wykonywany, aby znaleźć wady zabezpieczeń.

Realizuj DAST, najlepiej z pomocą specjalisty ds. zabezpieczeń ( [testera penetracji](../fundamentals/pen-testing.md) lub oceniania luk w zabezpieczeniach). Jeśli specjalista ds. zabezpieczeń nie jest dostępny, możesz samodzielnie wykonać DAST z użyciem skanera internetowego serwera proxy i niektórych szkoleń. Przede wszystkim Podłącz skaner DAST, aby upewnić się, że w kodzie nie wprowadzono oczywistych problemów z zabezpieczeniami. Zobacz witrynę [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) , aby uzyskać listę skanerów luk w zabezpieczeniach aplikacji sieci Web.

### <a name="perform-fuzz-testing"></a>Wykonaj testy rozmyte

W przypadku [testowania rozmytego](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)wywoływanie awarii programu polega na zamierzonym wprowadzeniu nieprawidłowo utworzonych lub losowo danych do aplikacji. Niepowodzenie programu ułatwia ujawnienie potencjalnych problemów z zabezpieczeniami przed wydaniem aplikacji.

[Wykrywanie zagrożeń bezpieczeństwa](https://www.microsoft.com/en-us/security-risk-detection/) to wyjątkowa usługa testowania rozmytego firmy Microsoft do znajdowania usterek krytycznych dla zabezpieczeń w oprogramowaniu.

### <a name="conduct-attack-surface-review"></a>Przeprowadź przegląd obszaru ataków

Przeglądanie obszaru ataków po uzupełnianiu kodu pomaga upewnić się, że wszystkie zmiany projektu lub implementacji zostały uwzględnione w aplikacji lub systemie. Pomaga zapewnić, że wszystkie nowe wektory ataków, które zostały utworzone w wyniku zmian, w tym modele zagrożeń, zostały sprawdzone i skorygowane.

Możesz utworzyć zdjęcie obszaru ataków, skanując aplikację. Firma Microsoft oferuje narzędzie do analizy podatnej na ataki o nazwie [Analizator obszaru ataków](https://www.microsoft.com/download/details.aspx?id=58105). Możesz wybrać spośród wielu komercyjnych testów dynamicznych i narzędzi do skanowania, w tym [OWASP Zed, serwer proxy ataku](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)i [w3af](http://w3af.sourceforge.net/). Te narzędzia do skanowania przeszukują aplikację i mapują elementy aplikacji, które są dostępne w sieci Web. Możesz również wyszukać podobne [Narzędzia deweloperskie](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)w witrynie Azure Marketplace.

### <a name="perform-security-penetration-testing"></a>Przeprowadź testowanie penetracji zabezpieczeń

Upewnienie się, że aplikacja jest zabezpieczona, jest tak ważne jak testowanie wszelkich innych funkcji. [Przetestowanie penetracji](../fundamentals/pen-testing.md) standardowej części procesu kompilowania i wdrażania. Zaplanuj regularne testy zabezpieczeń i skanowania w poszukiwaniu wdrożonych aplikacji oraz monitoruj otwarte porty, punkty końcowe i ataki.

### <a name="run-security-verification-tests"></a>Uruchom testy weryfikacyjne zabezpieczeń

Pakiet [Secure DevOps Kit dla platformy Azure](https://azsk.azurewebsites.net/index.html) (AzSK) zawiera SVTs dla wielu usług platformy Azure. Te SVTs są uruchamiane okresowo, aby mieć pewność, że Twoja subskrypcja platformy Azure i różne zasoby wchodzące w skład Twojej aplikacji są w stanie bezpiecznym. Możesz również zautomatyzować te testy przy użyciu funkcji rozszerzeń ciągłej integracji/ciągłego wdrażania (CI/CD) AzSK, która sprawia, że SVTs jest dostępna jako rozszerzenie programu Visual Studio.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zalecamy mechanizmy kontroli zabezpieczeń i działania, które mogą pomóc w projektowaniu i wdrażaniu bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Wdrażanie bezpiecznych aplikacji](secure-deploy.md)