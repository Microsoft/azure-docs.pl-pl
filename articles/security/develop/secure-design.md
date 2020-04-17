---
title: Projektowanie bezpiecznych aplikacji na platformie Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, które należy wziąć pod uwagę podczas fazy wymagań i projektowania projektu aplikacji sieci web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c9e3cfa689f2e528f4d20e796017ae9d91c29fe2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461722"
---
# <a name="design-secure-applications-on-azure"></a>Projektowanie bezpiecznych aplikacji na platformie Azure
W tym artykule przedstawiamy działania zabezpieczeń i kontrolki, które należy wziąć pod uwagę podczas projektowania aplikacji dla chmury. Zasoby szkoleniowe wraz z pytaniami dotyczącymi zabezpieczeń i pojęciami, które należy wziąć pod uwagę podczas wymagań i etapów projektowania cyklu życia programu Microsoft [Security Development (SDL).](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) Celem jest pomoc w definiowaniu działań i usług platformy Azure, których można użyć do projektowania bezpieczniejszej aplikacji.

W tym artykule uwzględnione są następujące fazy SDL:

- Szkolenia
- Wymagania
- Projekt

## <a name="training"></a>Szkolenia
Zanim zaczniesz tworzyć aplikację w chmurze, poświęć trochę czasu na zrozumienie zabezpieczeń i prywatności na platformie Azure. Podejmując ten krok, można zmniejszyć liczbę i ważność luk w zabezpieczeniach, które można wykorzystać w aplikacji. Będziesz bardziej przygotowany do odpowiedniego reagowania na stale zmieniający się krajobraz zagrożeń.

Użyj następujących zasobów na etapie szkolenia, aby zapoznać się z usługami platformy Azure, które są dostępne dla deweloperów i z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń na platformie Azure:

  - [Przewodnik dla deweloperów platformy Azure](https://azure.microsoft.com/campaigns/developer-guide/) pokazuje, jak rozpocząć korzystanie z platformy Azure. Przewodnik pokazuje, których usług można używać do uruchamiania aplikacji, przechowywania danych, dołączania danych, tworzenia aplikacji IoT i wdrażania rozwiązań w bardziej wydajny i bezpieczny sposób.

  - [Przewodnik wprowadzenie dla deweloperów platformy Azure](../../guides/developer/azure-developer-guide.md) zawiera podstawowe informacje dla deweloperów, którzy chcą rozpocząć korzystanie z platformy Azure dla ich potrzeb programistycznych.

  - [ZestawY SDK i narzędzia](https://docs.microsoft.com/azure/index?pivot=sdkstools) opisują narzędzia, które są dostępne na platformie Azure.

  - [Usługa Azure DevOps Services](https://docs.microsoft.com/azure/devops/) udostępnia narzędzia do współpracy deweloperów. Narzędzia obejmują potoki o wysokiej wydajności, bezpłatne repozytoria Git, konfigurowalne karty Kanban oraz szeroko zakrojone zautomatyzowane i oparte na chmurze testy obciążenia.
    [Centrum zasobów DevOps](https://docs.microsoft.com/azure/devops/learn/) łączy nasze zasoby do nauki praktyk DevOps, kontroli wersji Git, metod agile, jak współpracujemy z DevOps w firmie Microsoft i jak można ocenić swój własny postęp DevOps.

  - [5 najważniejszych elementów zabezpieczeń, które należy wziąć pod uwagę przed wypchnięciem do produkcji,](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) pokazuje, jak zabezpieczyć aplikacje sieci web na platformie Azure i chronić aplikacje przed najczęstszymi i najbardziej niebezpiecznymi atakami aplikacji sieci Web.

  - [Zestaw Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) to zbiór skryptów, narzędzi, rozszerzeń i automatyzacji, który zaspokaja kompleksowe potrzeby w zakresie subskrypcji platformy Azure i zabezpieczeń zasobów zespołów DevOps korzystających z rozbudowanej automatyzacji. Zestaw Secure DevOps dla platformy Azure może pokazać, jak płynnie zintegrować zabezpieczenia z natywnymi przepływami pracy DevOps. Zestaw dotyczy narzędzi, takich jak testy weryfikacji zabezpieczeń (SVTs), które mogą pomóc deweloperom napisać bezpieczny kod i przetestować bezpieczną konfigurację swoich aplikacji w chmurze na etapie kodowania i wczesnego rozwoju.

  - [Najlepsze rozwiązania dotyczące zabezpieczeń dotyczące rozwiązań platformy Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) zawiera zbiór najlepszych rozwiązań w zakresie zabezpieczeń do użycia podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

## <a name="requirements"></a>Wymagania
Faza definiowania wymagań jest kluczowym krokiem w definiowaniu, czym jest aplikacja i co zrobi po jej wydaniu. Faza wymagań jest również czas, aby myśleć o kontroli zabezpieczeń, które zostaną wbudowane w aplikacji. W tej fazie można również rozpocząć kroki, które można wykonać w całej SDL, aby upewnić się, że zwalniasz i wdrażasz bezpieczną aplikację.

### <a name="consider-security-and-privacy-issues"></a>Zastanów się nad kwestiami bezpieczeństwa i prywatności
Ten etap to najlepszy czas na rozważenie fundamentalnych kwestii związanych z bezpieczeństwem i prywatnością. Określenie dopuszczalnych poziomów bezpieczeństwa i prywatności na początku projektu pomaga zespołowi:

- Opis zagrożeń związanych z problemami z zabezpieczeniami.
- Identyfikowanie i naprawianie błędów zabezpieczeń podczas opracowywania.
- Zastosuj ustalone poziomy bezpieczeństwa i prywatności w całym projekcie.

Podczas pisania wymagań dla aplikacji, należy wziąć pod uwagę środki kontroli zabezpieczeń, które mogą pomóc zachować bezpieczne aplikacji i danych.

### <a name="ask-security-questions"></a>Zadawaj pytania zabezpieczające
Zadawaj pytania zabezpieczające, takie jak:

  - Czy moja aplikacja zawiera poufne dane?

  - Czy moja aplikacja zbiera lub przechowuje dane, które wymagają ode mnie przestrzegania standardów branżowych i programów zgodności, takich jak [Federalna Rada Egzaminacyjna Instytucji Finansowych (FFIEC)](../blueprints/ffiec-analytics-overview.md) lub [Standardy Bezpieczeństwa Danych Przemysłu Kart Płatniczych (PCI DSS)?](../blueprints/pcidss-analytics-overview.md)

  - Czy moja aplikacja gromadzi lub zawiera poufne dane osobowe lub dane klienta, które mogą być używane, samodzielnie lub z innymi informacjami, do identyfikacji, kontaktu lub zlokalizowania jednej osoby?

  - Czy moja aplikacja gromadzi lub zawiera dane, które mogą być wykorzystane do uzyskania dostępu do informacji medycznych, edukacyjnych, finansowych lub o zatrudnieniu danej osoby? Identyfikowanie czułości danych podczas fazy wymagań pomaga klasyfikować dane i identyfikować metodę ochrony danych, która będzie używana w aplikacji.

  - Gdzie i w jaki sposób przechowywane są moje dane? Należy wziąć pod uwagę sposób monitorowania usług magazynu, które aplikacja używa dla wszelkich nieoczekiwanych zmian (takich jak wolniejsze czasy odpowiedzi). Czy będziesz w stanie wpłynąć na rejestrowanie, aby zebrać bardziej szczegółowe dane i dogłębnie przeanalizować problem?

  - Czy moja aplikacja będzie dostępna publicznie (w Internecie) lub tylko wewnętrznie? Jeśli aplikacja jest dostępna publicznie, w jaki sposób chronić dane, które mogą być zbierane przed użyciem w niewłaściwy sposób? Jeśli aplikacja jest dostępna tylko wewnętrznie, należy wziąć pod uwagę, kto w organizacji powinien mieć dostęp do aplikacji i jak długo powinny one mieć dostęp.

  - Czy rozumiesz swój model tożsamości przed rozpoczęciem projektowania aplikacji? Jak określisz, że użytkownicy są tym, kim mówią, że są i do czego użytkownik jest upoważniony?

  - Czy moja aplikacja wykonuje wrażliwe lub ważne zadania (takie jak przesyłanie pieniędzy, odblokowywanie drzwi lub dostarczanie leków)?
    Należy wziąć pod uwagę, jak będzie sprawdzać, czy użytkownik wykonujący poufne zadanie jest upoważniony do wykonania zadania i jak będzie uwierzytelnić, że osoba jest tym, kim mówią, że są. Autoryzacja (AuthZ) jest aktem udzielania uwierzytelnionego uprawnienia głównego zabezpieczeń, aby coś zrobić. Uwierzytelnianie (AuthN) jest aktem kwestionowania strony o wiarygodne poświadczenia.

  - Czy moja aplikacja wykonuje ryzykowne działania związane z oprogramowaniem, takie jak zezwalanie użytkownikom na przesyłanie lub pobieranie plików lub innych danych? Jeśli aplikacja wykonuje ryzykowne działania, należy wziąć pod uwagę, jak aplikacja będzie chronić użytkowników przed obsługą złośliwych plików lub danych.

### <a name="review-owasp-top-10"></a>Weryfikacja OWASP top 10
Rozważ zapoznanie się z [<span class="underline">zabezpieczeniami aplikacji OWASP Top 10</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 dotyczy krytycznych zagrożeń bezpieczeństwa dla aplikacji sieci web.
Świadomość tych zagrożeń bezpieczeństwa może pomóc w podejmowaniu decyzji dotyczących wymagań i projektowania, które minimalizują te zagrożenia w aplikacji.

Ważne jest myślenie o kontrolach bezpieczeństwa, aby zapobiec naruszeniom.
Jednak chcesz również [założyć, że](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) nastąpi naruszenie. Zakładając, że naruszenie pomaga odpowiedzieć na kilka ważnych pytań dotyczących bezpieczeństwa z wyprzedzeniem, więc nie trzeba na nie odpowiadać w nagłych wypadkach:

  - Jak wykryć atak?

  - Co zrobię w przypadku ataku lub naruszenia?

  - Jak mam odzyskać od ataku, takich jak wyciek danych lub manipulacji?

## <a name="design"></a>Projekt

Faza projektowania ma kluczowe znaczenie dla ustanowienia najlepszych praktyk w zakresie projektowania i specyfikacji funkcjonalnych. Ma również kluczowe znaczenie dla przeprowadzania analizy ryzyka, która pomaga ograniczyć problemy z bezpieczeństwem i prywatnością w całym projekcie.

Jeśli masz wymagania dotyczące zabezpieczeń i używasz koncepcji bezpiecznego projektowania, możesz uniknąć lub zminimalizować możliwości luki w zabezpieczeniach. Luka w zabezpieczeniach jest niedopatrzeniem w projekcie aplikacji, które może umożliwić użytkownikowi wykonywanie złośliwych lub nieoczekiwanych akcji po zwolnieniu aplikacji.

Na etapie projektowania zastanów się również, jak można zastosować zabezpieczenia w warstwach; jeden poziom obrony niekoniecznie wystarczy. Co się stanie, jeśli osoba atakująca ominie zaporę aplikacji sieci web (WAF)? Chcesz innej kontroli bezpieczeństwa w miejscu, aby bronić się przed tym atakiem.

Mając to na uwadze, omówimy następujące koncepcje bezpiecznego projektowania i mechanizmy kontroli zabezpieczeń, które należy rozwiązać podczas projektowania bezpiecznych aplikacji:

- Użyj bezpiecznej biblioteki kodowania i struktury oprogramowania.
- Skanowanie w poszukiwaniu zagrożonych komponentów.
- Użyj modelowania zagrożeń podczas projektowania aplikacji.
- Zmniejsz powierzchnię ataku.
- Przyjęcie zasady tożsamości jako obwodu zabezpieczeń podstawowych.
- Wymagaj ponownego uwierzytelnienia dla ważnych transakcji.
- Użyj rozwiązania do zarządzania kluczami, aby zabezpieczyć klucze, poświadczenia i inne wpisy tajne.
- Ochrona poufnych danych.
- Wdrażanie środków bezpieczeństwa awaryjnego.
- Skorzystaj z obsługi błędów i wyjątków.
- Użyj rejestrowania i ostrzegania.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Korzystanie z bezpiecznej biblioteki kodowania i struktury oprogramowania

W celu programowania należy użyć bezpiecznej biblioteki kodowania i struktury oprogramowania, która ma wbudowane zabezpieczenia. Deweloperzy mogą używać istniejących, sprawdzonych funkcji (szyfrowanie, kanalizacja wejściowa, kodowanie danych wyjściowych, klucze lub parametry połączenia i wszystko inne, co można by uznać za kontrolę zabezpieczeń) zamiast tworzyć formanty zabezpieczeń od podstaw. Pomaga to chronić przed wadami dotyczącymi projektowania i wdrażania związanych z zabezpieczeniami.

Upewnij się, że używasz najnowszej wersji struktury i wszystkie funkcje zabezpieczeń, które są dostępne w ramach. Firma Microsoft oferuje [kompleksowy zestaw narzędzi programistycznych](https://azure.microsoft.com/product-categories/developer-tools/) dla wszystkich deweloperów, pracujących na dowolnej platformie lub języku, aby dostarczać aplikacje w chmurze. Możesz kodować z wybranym językiem, wybierając z różnych [SDK](https://azure.microsoft.com/downloads/).
Możesz korzystać z w pełni funkcjonalnych zintegrowanych środowisk programistów (IDE) i edytorów, które mają zaawansowane możliwości debugowania i wbudowaną obsługę platformy Azure.

Firma Microsoft oferuje wiele [języków, struktur i narzędzi,](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) których można używać do tworzenia aplikacji na platformie Azure. Przykładem jest [platforma Azure dla deweloperów platformy .NET i .NET Core](https://docs.microsoft.com/dotnet/azure/). Dla każdego języka i struktury, które oferujemy, znajdziesz przewodniki Szybki start, samouczki i odwołania do interfejsu API, które pomogą Ci szybko rozpocząć pracę.

Platforma Azure oferuje wiele usług, których można używać do hostowania witryn sieci Web i aplikacji sieci Web. Te usługi pozwalają rozwijać się w ulubionym języku, czy to .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python.
[Usługa Azure App Service Web Apps](../../app-service/overview.md) (Aplikacje sieci Web) jest jedną z tych usług.

Aplikacje sieci Web zwiększają moc platformy Microsoft Azure. Obejmuje zabezpieczenia, równoważenie obciążenia, skalowanie automatyczne i zautomatyzowane zarządzanie. Można również korzystać z możliwości DevOps w aplikacjach sieci Web, takich jak zarządzanie pakietami, środowiska przejściowe, domeny niestandardowe, certyfikaty SSL/TLS i ciągłe wdrażanie z usługi Azure DevOps, GitHub, Docker Hub i innych źródeł.

Platforma Azure oferuje inne usługi, których można używać do hostowania witryn sieci Web i aplikacji sieci Web. W przypadku większości scenariuszy najlepszym wyborem jest usługa Web Apps. W przypadku architektury mikrousług należy wziąć pod uwagę [sieci szkieletowej usług Azure.](https://azure.microsoft.com/documentation/services/service-fabric)
W razie konieczności sprawowania większej kontroli nad maszynami wirtualnymi, na których działa kod, należy wziąć pod uwagę usługę [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Aby uzyskać więcej informacji na temat wyboru między tymi usługami platformy Azure, zobacz [porównanie usługi Azure App Service, maszyn wirtualnych, sieci szkieletowej usług i usług w chmurze.](/azure/architecture/guide/technology-choices/compute-decision-tree)

### <a name="apply-updates-to-components"></a>Stosowanie aktualizacji do składników

Aby zapobiec lukom, należy stale inwentaryzować składniki po stronie klienta i po stronie serwera (na przykład struktury i biblioteki) oraz ich zależności pod kątem aktualizacji. Nowe luki w zabezpieczeniach i zaktualizowane wersje oprogramowania są wydawane w sposób ciągły. Upewnij się, że masz stały plan monitorowania, klasyfikowania i stosowania aktualizacji lub zmian konfiguracji do używanych bibliotek i składników.

Informacje na temat [używania składników ze znanymi lukami](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) w zabezpieczeniach sugestii dotyczących narzędzi można znaleźć na stronie [Open Web Application Security Project (OWASP).](https://www.owasp.org/index.php/Main_Page) Można również subskrybować alerty e-mail dotyczące luk w zabezpieczeniach związanych z używanymi składnikami.

### <a name="use-threat-modeling-during-application-design"></a>Używanie modelowania zagrożeń podczas projektowania aplikacji

Modelowanie zagrożeń to proces identyfikowania potencjalnych zagrożeń bezpieczeństwa dla firmy i aplikacji, a następnie zapewnienia odpowiednich środków zaradczych. SDL określa, że zespoły powinny angażować się w modelowanie zagrożeń w fazie projektowania, gdy rozwiązywanie potencjalnych problemów jest stosunkowo łatwe i opłacalne. Korzystanie z modelowania zagrożeń w fazie projektowania może znacznie zmniejszyć całkowity koszt rozwoju.

Aby ułatwić proces modelowania zagrożeń, zaprojektowaliśmy [narzędzie do modelowania zagrożeń SDL](threat-modeling-tool.md) z myślą o ekspertach innych niż kwestie bezpieczeństwa. To narzędzie ułatwia modelowanie zagrożeń wszystkim deweloperom, zapewniając jasne wskazówki dotyczące tworzenia i analizowania modeli zagrożeń.

Modelowanie projektu aplikacji i wyliczanie zagrożeń [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) — fałszowanie, manipulowanie, odpieranie, ujawnianie informacji, odmowa usługi i podniesienie uprawnień — we wszystkich granicach zaufania okazało się skutecznym sposobem na wczesne wychwytywanie błędów projektowych. W poniższej tabeli wymieniono zagrożenia STRIDE i podano kilka przykładowych środków zaradczych, które używają funkcji dostarczonych przez platformę Azure. Te środki zaradcze nie będą działać w każdej sytuacji.

| Zagrożenie | Właściwość zabezpieczeń | Potencjalne ograniczenie platformy Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Fałszowanie zawartości               | Authentication        | [Wymagaj połączeń HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manipulowanie              | Integralność             | Sprawdzanie poprawności certyfikatów SSL/TLS. Aplikacje korzystające z protokołu SSL/TLS muszą w pełni zweryfikować certyfikaty X.509 jednostek, z którymi się łączą. Użyj certyfikatów usługi Azure Key Vault do [zarządzania certyfikatami x509](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates). |
| Odrzucenie            | Brak odrzucenia       | Włącz [monitorowanie i diagnostykę](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)platformy Azure .|
| Ujawnianie informacji | Poufność       | Szyfruj poufne dane [w spoczynku](../fundamentals/encryption-atrest.md) i [podczas przesyłania](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Odmowa usługi      | Dostępność          | Monitoruj metryki wydajności pod kątem potencjalnych warunków typu "odmowa usługi". Implementowanie filtrów połączeń. [Ochrona przed atakami DDoS platformy Azure](../../virtual-network/ddos-protection-overview.md#next-steps)w połączeniu z najlepszymi rozwiązaniami w zakresie projektowania aplikacji zapewnia ochronę przed atakami DDoS.|
| Podniesienie uprawnień | Autoryzacja         | Korzystanie z <span class="underline"> </span> [zarządzania tożsamościami uprzywilejowanymi](../../active-directory/privileged-identity-management/pim-configure.md)usługi Azure Active Directory .|

### <a name="reduce-your-attack-surface"></a>Zmniejsz powierzchnię ataku

Obszar ataku to całkowita suma potencjalnych luk w zabezpieczeniach. W tym artykule skupiamy się na powierzchni ataku aplikacji.
Nacisk kładzie się na ochronę aplikacji przed atakiem. Prostym i szybkim sposobem zminimalizowania powierzchni ataku jest usunięcie nieużywanych zasobów i kodu z aplikacji. Im mniejsza aplikacja, tym mniejsza powierzchnia ataku. Na przykład usuń:

- Kod funkcji, które nie zostały jeszcze wydane.
- Debugowanie kod pomocy technicznej.
- Interfejsy sieciowe i protokoły, które nie są używane lub które zostały przestarzałe.
- Maszyny wirtualne i inne zasoby, których nie używasz.

Regularne oczyszczanie zasobów i zapewnienie usunięcia nieużytego kodu to świetne sposoby na zapewnienie, że istnieje mniej możliwości atakowania złośliwych podmiotów.

Bardziej szczegółowym i dogłębnym sposobem zmniejszenia powierzchni ataku jest ukończenie analizy powierzchni ataku. Analiza powierzchni ataku ułatwia mapowanie części systemu, które muszą zostać sprawdzone i przetestowane pod kątem luk w zabezpieczeniach.

Celem analizy powierzchni ataku jest zrozumienie obszarów ryzyka w aplikacji, dzięki czemu deweloperzy i specjaliści od zabezpieczeń są świadomi, jakie części aplikacji są otwarte do ataku. Następnie można znaleźć sposoby, aby zminimalizować ten potencjał, śledzić, kiedy i jak zmienia się powierzchnia ataku i co to oznacza z punktu widzenia ryzyka.

Analiza powierzchni ataku pomaga zidentyfikować:

- Funkcje i części systemu, które należy przejrzeć i przetestować pod kątem luk w zabezpieczeniach.
- Obszary wysokiego ryzyka kodu, które wymagają ochrony w głębi (części systemu, które należy bronić).
- Po zmianie powierzchni ataku i trzeba odświeżyć ocenę zagrożenia.

Ograniczenie możliwości wykorzystania przez osoby atakujące potencjalnego słabego punktu lub luki w zabezpieczeniach wymaga dokładnej analizy ogólnej powierzchni ataku aplikacji. Obejmuje ona również wyłączenie lub ograniczenie dostępu do usług systemowych, stosowanie zasady najmniejszych uprawnień i stosowanie warstwowych mechanizmów obronnych w miarę możliwości.

Omawiamy [przeprowadzenie przeglądu powierzchni ataku](secure-develop.md#conduct-attack-surface-review) podczas fazy weryfikacji SDL.

> [!NOTE]
> **Jaka jest różnica między modelowania zagrożeń i analizy powierzchni ataku?**
Modelowanie zagrożeń to proces identyfikowania potencjalnych zagrożeń bezpieczeństwa dla aplikacji i zapewnienia, że istnieją odpowiednie środki zaradcze przed zagrożeniami. Analiza powierzchni ataku identyfikuje obszary wysokiego ryzyka kodu, które są otwarte do ataku. Obejmuje znalezienie sposobów obrony obszarów wysokiego ryzyka aplikacji i przeglądania i testowania tych obszarów kodu przed wdrożeniem aplikacji.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Przyjęcie zasady tożsamości jako obwodu zabezpieczeń podstawowych

Podczas projektowania aplikacji w chmurze, ważne jest, aby rozszerzyć zakres zabezpieczeń skupić się z sieci zorientowanych podejście do podejścia zorientowanego na tożsamość. Historycznie podstawowym obwodem zabezpieczeń lokalnych była sieć organizacji. Większość lokalnych projektów zabezpieczeń używa sieci jako podstawowej wersji obrotu zabezpieczeń. W przypadku aplikacji w chmurze lepiej jest obsługiwać, biorąc pod uwagę tożsamość jako podstawowy obwód zabezpieczeń.

Czynności, które można wykonać, aby opracować podejście zorientowane na tożsamość w celu tworzenia aplikacji sieci web:

- Wymuszanie uwierzytelniania wieloskładnikowego dla użytkowników.
- Użyj platform silnego uwierzytelniania i autoryzacji.
- Zastosuj zasadę najmniejszego przywileju.
- Zaimplementuj dostęp just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Wymuszanie uwierzytelniania wieloskładnikowego dla użytkowników

Użyj uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest bieżącym standardem uwierzytelniania i autoryzacji, ponieważ pozwala uniknąć zabezpieczeń, które są związane z nazwami użytkowników i hasła typów uwierzytelniania. Dostęp do interfejsów zarządzania platformy Azure (portal Azure/zdalny program PowerShell) oraz do usług skierowanych do klienta powinien być zaprojektowany i skonfigurowany do korzystania z [uwierzytelniania wieloskładnikowego platformy Azure.](../../active-directory/authentication/concept-mfa-howitworks.md)

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Korzystanie z platform silnego uwierzytelniania i autoryzacji

Zamiast kodu niestandardowego należy używać mechanizmów uwierzytelniania i autoryzacji dostarczonych przez platformę. Dzieje się tak, ponieważ tworzenie niestandardowego kodu uwierzytelniania może być podatne na błędy. Kod komercyjny (na przykład od firmy Microsoft) często jest szczegółowo sprawdzany pod kątem bezpieczeństwa. [Usługa Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) to rozwiązanie platformy Azure do zarządzania tożsamościami i dostępem. Te narzędzia i usługi Azure AD pomagają w bezpiecznym opracowywaniu:

- [Platforma tożsamości usługi Azure AD (Usługa Azure AD dla deweloperów)](../../active-directory/develop/about-microsoft-identity-platform.md) to usługa tożsamości w chmurze używana przez deweloperów do tworzenia aplikacji, które bezpiecznie logują się do użytkowników. Usługa Azure AD pomaga deweloperom, którzy budują aplikacje z jedną dzierżawą, linią biznesową (LOB) i deweloperom, którzy chcą tworzyć aplikacje z wieloma dzierżawcami. Oprócz podstawowego logowania aplikacje, które są tworzone przy użyciu usługi Azure AD można wywołać interfejsy API firmy Microsoft i niestandardowych interfejsów API, które są oparte na platformie Azure AD. Platforma tożsamości usługi Azure AD obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

- [Usługa Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) to usługa zarządzania tożsamościami, której można używać do dostosowywania i kontrolowania sposobu rejestrowania się, logowania się i zarządzania swoimi profilami podczas korzystania z aplikacji. Obejmuje to aplikacje, które są opracowane dla systemów iOS, Android i .NET, między innymi. Usługa Azure AD B2C umożliwia te akcje przy jednoczesnej ochronie tożsamości klientów.

#### <a name="apply-the-principle-of-least-privilege"></a>Stosowanie zasady najmniejszych uprawnień

Pojęcie [najmniejszych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) oznacza zapewnienie użytkownikom dokładnego poziomu dostępu i kontroli, którego potrzebują do wykonywania swoich zadań i nic więcej.

Czy programista potrzebuje praw administratora domeny? Czy asystent administracyjny potrzebowałby dostępu do kontroli administracyjnej na swoim komputerze osobistym? Ocena dostępu do oprogramowania nie jest inna. Jeśli używasz [kontroli dostępu opartej na rolach (RBAC),](../../role-based-access-control/overview.md) aby dać użytkownikom różne możliwości i uprawnienia w aplikacji, nie zapewnisz wszystkim dostępu do wszystkiego. Ograniczając dostęp do tego, co jest wymagane dla każdej roli, można ograniczyć ryzyko wystąpienia problemu z zabezpieczeniami.

Upewnij się, że aplikacja wymusza [najmniejsze uprawnienia](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) w swoich wzorcach dostępu.

> [!NOTE]
>  Zasady o najmniejszych uprawnieniach muszą mieć zastosowanie do oprogramowania i osób tworzących oprogramowanie. Deweloperzy oprogramowania mogą stanowić ogromne zagrożenie dla bezpieczeństwa IT, jeśli mają zbyt duży dostęp. Konsekwencje mogą być poważne, jeśli deweloper ma złośliwe intencje lub ma zbyt duży dostęp. Zaleca się, że reguły o najmniejszych uprawnieniach mają zastosowanie do deweloperów w całym cyklu życia programu dewelopera.

#### <a name="implement-just-in-time-access"></a>Wdrażanie dostępu just-in-time

Zaimplementuj dostęp *just-in-time* (JIT), aby jeszcze bardziej obniżyć czas narażenia uprawnień. Użyj [uprzywilejowanego zarządzania tożsamościami usługi Azure AD,](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) aby:

- Daj użytkownikom uprawnienia, których potrzebują tylko JIT.
- Przypisz role na skrócony czas trwania zufnością, że uprawnienia są automatycznie odwoływane.

### <a name="require-re-authentication-for-important-transactions"></a>Wymagaj ponownego uwierzytelnienia dla ważnych transakcji

[Fałszerstwo żądań między witrynami](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (znane również jako *XSRF* lub *CSRF)* to atak na aplikacje hostowane w sieci Web, w którym złośliwa aplikacja internetowa wpływa na interakcję między przeglądarką klienta a aplikacją sieci Web, która ufa tej przeglądarce. Ataki fałszerstwa żądań między witrynami są możliwe, ponieważ przeglądarki internetowe wysyłają niektóre typy tokenów uwierzytelniania automatycznie przy każdym żądaniu do witryny sieci Web.
Ta forma wykorzystania jest również znany jako *atak jednym kliknięciem* lub *sesji jazdy,* ponieważ atak wykorzystuje wcześniej uwierzytelnionej sesji użytkownika.

Najlepszym sposobem obrony przed tego rodzaju atakiem jest zwrócenie się do użytkownika o coś, co tylko użytkownik może podać przed każdą ważną transakcją, taką jak zakup, dezaktywacja konta lub zmiana hasła. Możesz poprosić użytkownika o ponowne wniesienie hasła, wypełnienie captcha lub przesłanie tajnego tokenu, który będzie miał tylko użytkownik. Najczęstszym podejściem jest tajny token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Używanie rozwiązania do zarządzania kluczami w celu zabezpieczenia kluczy, poświadczeń i innych wpisów tajnych

Utrata kluczy i poświadczeń jest częstym problemem. Jedyną rzeczą gorszą niż utrata kluczy i poświadczeń jest posiadanie nieautoryzowanej strony uzyskać dostęp do nich. Osoby atakujące mogą korzystać z technik automatycznych i ręcznych, aby znaleźć klucze i wpisy tajne, które są przechowywane w repozytoriach kodu, takich jak GitHub. Nie umieszczaj kluczy i wpisów tajnych w tych publicznych repozytoriach kodu ani na żadnym innym serwerze.

Zawsze umieszczaj klucze, certyfikaty, wpisy tajne i parametry połączenia w rozwiązaniu do zarządzania kluczami. Można użyć scentralizowanego rozwiązania, w którym klucze i wpisy tajne są przechowywane w sprzętowych modułach zabezpieczeń (HSM). Platforma Azure zapewnia moduł HSM w chmurze dzięki [usłudze Azure Key Vault.](../../key-vault/general/overview.md)

Key Vault to *tajny magazyn:* jest to scentralizowana usługa w chmurze do przechowywania wpisów tajnych aplikacji. Usługa Key Vault chroni poufne dane, przechowując wpisy tajne aplikacji w jednej, centralnej lokalizacji i zapewniając bezpieczny dostęp, kontrolę uprawnień i rejestrowanie dostępu.

Wpisy tajne są przechowywane w poszczególnych *magazynach*. Każdy magazyn ma własną konfigurację i zasady zabezpieczeń, aby kontrolować dostęp. Dostęp do danych za pośrednictwem interfejsu API REST lub za pośrednictwem sdk klienta, który jest dostępny dla większości języków programowania.

> [!IMPORTANT]
> Usługa Azure Key Vault jest przeznaczona do przechowywania wpisów tajnych konfiguracji dla aplikacji serwera. Nie jest przeznaczony do przechowywania danych należących do użytkowników aplikacji. Znajduje to odzwierciedlenie w charakterystyce jej wydajności, interfejsie API i modelu kosztów.
>
> Dane użytkownika powinny być przechowywane w innym miejscu, na przykład w wystąpieniu usługi Azure SQL Database, które ma przezroczyste szyfrowanie danych (TDE) lub na koncie magazynu, który używa szyfrowania usługi Azure Storage Service. Wpisy tajne, które są używane przez aplikację do uzyskiwania dostępu do tych magazynów danych mogą być przechowywane w usłudze Azure Key Vault.

### <a name="protect-sensitive-data"></a>Ochrona poufnych danych

Ochrona danych jest istotną częścią strategii bezpieczeństwa.
Klasyfikacja danych i identyfikowanie potrzeb w zakresie ochrony danych pomaga zaprojektować aplikację z myślą o bezpieczeństwie danych. Klasyfikacja (kategoryzowanie) przechowywanych danych według wrażliwości i wpływu biznesowego pomaga deweloperom określić ryzyko związane z danymi.

Oznacz wszystkie odpowiednie dane jako poufne podczas projektowania formatów danych. Upewnij się, że aplikacja traktuje odpowiednie dane jako poufne. Te praktyki mogą pomóc w ochronie poufnych danych:

- Użyj szyfrowania.
- Unikaj twardych kodowania wpisów tajnych, takich jak klucze i hasła.
- Upewnij się, że kontrole dostępu i inspekcje są na miejscu.

#### <a name="use-encryption"></a>Korzystanie z szyfrowania

Ochrona danych powinna być istotną częścią strategii bezpieczeństwa.
Jeśli dane są przechowywane w bazie danych lub jeśli porusza się tam iz powrotem między lokalizacjami, użyj szyfrowania danych w [spoczynku](../fundamentals/encryption-atrest.md) (podczas gdy w bazie danych) i szyfrowania [danych w tranzycie (w](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) drodze do i od użytkownika, bazy danych, interfejsu API lub punktu końcowego usługi). Zaleca się, aby do wymiany danych zawsze używać protokołów SSL/TLS. Upewnij się, że używasz najnowszej wersji protokołu TLS do szyfrowania (obecnie jest to wersja 1.2).

#### <a name="avoid-hard-coding"></a>Unikaj twardego kodowania

Niektóre rzeczy nigdy nie powinny być zakodowane na komputerze w oprogramowaniu. Niektóre przykłady to nazwy hostów lub adresy IP, adresy URL, adresy e-mail, nazwy użytkowników, hasła, klucze kont magazynu i inne klucze kryptograficzne. Należy wziąć pod uwagę implementowanie wymagań dotyczących tego, co może lub nie może być zakodowane na komputerze w kodzie, w tym w sekcjach komentarzy kodu.

Po umieszczeniu komentarzy w kodzie upewnij się, że nie zapisujesz żadnych poufnych informacji. Obejmuje to adres e-mail, hasła, parametry połączenia, informacje o aplikacji, które będą znane tylko przez kogoś w organizacji, oraz wszelkie inne, które mogą dać osobie atakującej przewagę w atakowaniu aplikacji lub organizacji.

Zasadniczo załóżmy, że wszystko w projekcie dewelopera będzie wiadomo publiczną po jego wdrożeniu. Unikaj umieszczania w projekcie poufnych danych dowolnego rodzaju.

Wcześniej omówiliśmy [usługę Azure Key Vault](../../key-vault/general/overview.md). Za pomocą usługi Key Vault można przechowywać wpisy tajne, takie jak klucze i hasła, zamiast ich kodowania na twardo. Korzystając z usługi Key Vault w połączeniu z tożsamościami zarządzanymi dla zasobów platformy Azure, twoja aplikacja sieci Web platformy Azure może łatwo i bezpiecznie uzyskiwać dostęp do tajnych wartości konfiguracji bez przechowywania żadnych wpisów tajnych w formancie źródłowym lub konfiguracji. Aby dowiedzieć się więcej, zobacz [Zarządzanie wpisami tajnymi w aplikacjach serwera za pomocą usługi Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Wdrażanie środków bezpieczeństwa awaryjnego

Aplikacja musi być w stanie obsługiwać [błędy,](https://docs.microsoft.com/dotnet/standard/exceptions/) które występują podczas wykonywania w spójny sposób. Aplikacja powinna przechwytyć wszystkie błędy i albo nie bezpieczne lub zamknięte.

Należy również upewnić się, że błędy są rejestrowane z wystarczającym kontekstem użytkownika do identyfikowania podejrzanych lub złośliwych działań. Dzienniki powinny być przechowywane przez wystarczająco dużo czasu, aby umożliwić opóźnioną analizę kryminalistyczną. Dzienniki powinny być w formacie, który może być łatwo używane przez rozwiązanie do zarządzania dziennikiem. Upewnij się, że wyzwalane są alerty dotyczące błędów związanych z zabezpieczeniami. Niewystarczające rejestrowanie i monitorowanie pozwala atakującym na dalsze systemy ataku i utrzymanie trwałości.

### <a name="take-advantage-of-error-and-exception-handling"></a>Korzystaj z obsługi błędów i wyjątków

Implementowanie poprawnej [obsługi błędów](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) i wyjątków jest ważną częścią kodowania obronnego. Obsługa błędów i wyjątków ma kluczowe znaczenie dla zapewnienia niezawodności i bezpieczeństwa systemu. Błędy w obsłudze błędów mogą prowadzić do różnego rodzaju luk w zabezpieczeniach, takich jak wyciek informacji do osób atakujących i pomoc osobom atakującym w do zrozumienia informacji o platformie i projekcie.

Upewnij się, że:

- Można obsługiwać wyjątki w sposób scentralizowany, aby uniknąć zduplikowanych [bloków try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) w kodzie.

- Wszystkie nieoczekiwane zachowania są obsługiwane wewnątrz aplikacji.

- Wiadomości, które są wyświetlane użytkownikom nie wyciekają krytyczne dane, ale zawierają wystarczające informacje, aby wyjaśnić problem.

- Wyjątki są rejestrowane i zapewniają wystarczającą ilość informacji dla zespołów kryminalistycznych lub reagowania na incydenty do zbadania.

[Usługa Azure Logic Apps](../../logic-apps/logic-apps-overview.md) zapewnia pierwszorzędne środowisko [obsługi błędów i wyjątków,](../../logic-apps/logic-apps-exception-handling.md) które są spowodowane przez systemy zależne. Aplikacja logiki służy do tworzenia przepływów pracy w celu automatyzacji zadań i procesów integrujonych aplikacje, dane, systemy i usługi w przedsiębiorstwach i organizacjach.

### <a name="use-logging-and-alerting"></a>Korzystanie z rejestrowania i ostrzegania

[Rejestruj](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) swoje problemy z zabezpieczeniami w celu przeprowadzenia dochodzeń w sprawie zabezpieczeń i wyzwalaj alerty o problemach, aby upewnić się, że użytkownicy wiedzą o problemach w odpowiednim czasie. Włącz inspekcję i rejestrowanie wszystkich składników. Dzienniki inspekcji należy przechwytywać kontekst użytkownika i zidentyfikować wszystkie ważne zdarzenia.

Sprawdź, czy nie rejestrujesz żadnych poufnych danych przesłanych przez użytkownika do witryny. Przykłady poufnych danych obejmują:

- Poświadczenia użytkownika
- Numery ubezpieczenia społecznego lub inne informacje identyfikujące
- Numery kart kredytowych lub inne informacje finansowe
- Informacje o stanie zdrowia
- Klucze prywatne lub inne dane, które mogą być używane do odszyfrowywania zaszyfrowanych informacji
- Informacje o systemie lub aplikacji, które mogą być wykorzystane do skuteczniejszego ataku na aplikację

Upewnij się, że aplikacja monitoruje zdarzenia zarządzania użytkownikami, takie jak pomyślne i nieudane logowania użytkowników, resetowanie hasła, zmiany hasła, blokada konta i rejestracja użytkownika. Rejestrowanie tych zdarzeń pomaga wykryć i reagować na potencjalnie podejrzane zachowanie. Umożliwia również zbieranie danych operacyjnych, takich jak kto uzyskuje dostęp do aplikacji.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zaleca się kontrolki zabezpieczeń i działania, które mogą pomóc w opracowywaniu i wdrażaniu bezpiecznych aplikacji.

- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
- [Wdrażanie bezpiecznych aplikacji](secure-deploy.md)
