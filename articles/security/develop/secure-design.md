---
title: Projektowanie bezpiecznych aplikacji na Microsoft Azure
description: W tym artykule omówiono najlepsze rozwiązania, które należy wziąć pod uwagę w fazie projektowania aplikacji sieci Web.
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
ms.openlocfilehash: d0cffbd1fa09abef9853e0ef853696c3c8ed353c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246813"
---
# <a name="design-secure-applications-on-azure"></a>Projektowanie bezpiecznych aplikacji na platformie Azure
W tym artykule opisano działania związane z bezpieczeństwem i kontrolki, które należy wziąć pod uwagę podczas projektowania aplikacji w chmurze. Zasoby szkoleniowe wraz z pytaniami i pojęciami związanymi z bezpieczeństwem, które należy wziąć pod uwagę podczas wymagań i fazy projektowania [cyklu życia Microsoft Security Development (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) . Celem jest ułatwienie zdefiniowania działań i usług platformy Azure, których można użyć do zaprojektowania bezpieczniejszej aplikacji.

Następujące fazy SDL zostały omówione w tym artykule:

- Szkolenie
- Wymagania
- Projekt

## <a name="training"></a>Szkolenie
Przed rozpoczęciem opracowywania aplikacji w chmurze należy zapoznać się z tematem bezpieczeństwo i prywatność na platformie Azure. Wykonując ten krok, można zmniejszyć liczbę i ważność luk w zabezpieczeniach w aplikacji. Zostanie przygotowana do odpowiedniej reakcji na stale zmieniający się poziom zagrożenia.

Skorzystaj z następujących zasobów na etapie uczenia, aby zaznajomić się z usługami platformy Azure, które są dostępne dla deweloperów i z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń na platformie Azure:

  - [Przewodnik dewelopera na platformie Azure](https://azure.microsoft.com/campaigns/developer-guide/) pokazuje, jak rozpocząć pracę z platformą Azure. W tym przewodniku pokazano, które usługi służą do uruchamiania aplikacji, przechowywania danych, włączania analizy, tworzenia aplikacji IoT i wdrażania rozwiązań w bardziej wydajny i bezpieczny sposób.

  - [Przewodnik wprowadzenie dla deweloperów platformy Azure](../../guides/developer/azure-developer-guide.md) zawiera podstawowe informacje dla deweloperów, którzy chcą rozpocząć korzystanie z platformy Azure na potrzeby ich tworzenia.

  - [Zestawy SDK i narzędzia](https://docs.microsoft.com/azure/index?pivot=sdkstools) zawiera opis narzędzi dostępnych na platformie Azure.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) udostępnia narzędzia do współpracy deweloperskiej. Narzędzia te obejmują potoki o wysokiej wydajności, bezpłatne repozytoria Git, konfigurowalne tablice Kanban i rozbudowane zautomatyzowane testowanie obciążenia oparte na chmurze.
    [Centrum zasobów DevOps](https://docs.microsoft.com/azure/devops/learn/) łączy nasze zasoby na potrzeby uczenia DevOps, kontroli wersji Git, metod agile, sposobu pracy z DevOps w firmie Microsoft oraz jak można ocenić własne postępy DevOps.

  - [5 najważniejszych elementów zabezpieczeń, które należy wziąć pod uwagę przed rozpoczęciem wypychania do środowiska produkcyjnego](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) , pokazuje, jak zabezpieczyć aplikacje sieci Web na platformie Azure i chronić aplikacje przed najbardziej typowymi i niebezpiecznymi atakami aplikacji sieci Web.

  - [Secure DevOps Kit dla platformy Azure](https://azsk.azurewebsites.net/index.html) to zbiór skryptów, narzędzi, rozszerzeń i automatyzacji, które są związane z kompleksową subskrypcją platformy Azure i wymaganiami dotyczącymi zabezpieczeń zasobów DevOps zespołów, które używają rozległej automatyzacji. Pakiet Secure DevOps Kit dla platformy Azure może przedstawiać sposób bezproblemowego integrowania zabezpieczeń z natywnymi przepływami pracy DevOps. Zestaw adresów zawiera narzędzia, takie jak testy weryfikacyjne zabezpieczeń (SVTs), które mogą pomóc deweloperom pisać bezpieczny kod i przetestować bezpieczną konfigurację swoich aplikacji w chmurze na etapach tworzenia kodu i wczesnego rozwoju.

  - [Najlepsze rozwiązania w zakresie zabezpieczeń dla rozwiązań platformy Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) zawierają zbiór najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

## <a name="requirements"></a>Wymagania
Faza definicji wymagań jest kluczowym krokiem do definiowania działania aplikacji i działania wykonywanego po jego udostępnieniu. Fazy wymagań to również czas, aby myśleć o kontrolkach zabezpieczeń, które zostaną skompilowane w aplikacji. W tej fazie przedstawiono również kroki, które należy wykonać w całym SDL, aby upewnić się, że można wydać i wdrożyć bezpieczną aplikację.

### <a name="consider-security-and-privacy-issues"></a>Weź pod uwagę kwestie dotyczące zabezpieczeń i prywatności
Jest to najlepszy czas, aby rozważyć podstawowe kwestie dotyczące zabezpieczeń i prywatności. Definiowanie akceptowalnych poziomów zabezpieczeń i prywatności na początku projektu ułatwia zespołowi:

- Informacje o zagrożeniach związanych z problemami z zabezpieczeniami.
- Identyfikowanie i rozwiązywanie usterek zabezpieczeń podczas opracowywania.
- Stosuj ustalone poziomy zabezpieczeń i ochrony prywatności w całym projekcie.

Podczas pisania wymagań aplikacji należy wziąć pod uwagę kontrolę zabezpieczeń, która może pomóc zapewnić bezpieczeństwo aplikacji i danych.

### <a name="ask-security-questions"></a>Zadawaj pytania zabezpieczające
Zadawaj pytania zabezpieczające, takie jak:

  - Czy moja aplikacja zawiera poufne dane?

  - Czy moja aplikacja gromadzi lub przechowuje dane, które wymagają, aby były zgodne ze standardami branżowymi i programami zgodności, takimi jak [Federalna instytucja badawcza (FFIEC)](../blueprints/ffiec-analytics-overview.md) lub [branżowe standardy dotyczące bezpieczeństwa danych (PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - Czy moja aplikacja gromadzi lub zawiera poufne dane osobiste lub klienta, które mogą być używane samodzielnie lub z innymi informacjami, aby identyfikować, kontaktować się z lub lokalizować pojedyncze osoby?

  - Czy moja aplikacja zbiera lub zawiera dane, które mogą być używane w celu uzyskania dostępu do informacji medycznych, edukacyjnych, finansowych lub dotyczących zatrudnienia? Określenie czułości danych podczas fazy wymagań pozwala na klasyfikowanie danych i zidentyfikowanie metody ochrony danych, która będzie używana dla aplikacji.

  - Gdzie i jak są przechowywane moje dane? Zastanów się nad sposobem monitorowania usług magazynu używanych przez aplikację w przypadku wszelkich nieoczekiwanych zmian (na przykład wolniejszych czasów odpowiedzi). Czy będzie można mieć wpływ na rejestrowanie w celu zebrania bardziej szczegółowych danych i przeanalizować problem?

  - Czy moja aplikacja będzie dostępna publicznie (w Internecie) czy tylko wewnętrznie? Jeśli aplikacja jest dostępna publicznie, jak chronić dane, które mogą być zbierane z użycia w niewłaściwy sposób? Jeśli aplikacja jest dostępna tylko wewnętrznie, należy wziąć pod uwagę, kto w organizacji powinien mieć dostęp do aplikacji i jak długo powinien mieć dostęp.

  - Czy rozumiesz model tożsamości przed rozpoczęciem projektowania aplikacji? Jak można określić, czy użytkownicy mówią i do czego użytkownik ma uprawnienia?

  - Czy moja aplikacja wykonuje poufne lub ważne zadania (takie jak transfer pieniędzy, odblokowywanie lub dostarczanie medycyny)?
    Zastanów się, jak należy sprawdzić, czy użytkownik wykonujący poufne zadanie jest upoważniony do wykonania zadania i w jaki sposób uwierzytelniasz się, że jest to osoba, której mówią. Autoryzacja (autoryzacja) to czynność udzielenia uwierzytelnionego podmiotu zabezpieczeń uprawnienia do wykonywania czegoś. Uwierzytelnianie (AuthN) jest aktem zaskarżenia strony dla uprawnionych poświadczeń.

  - Czy moja aplikacja wykonuje jakiekolwiek ryzykowne działania dotyczące oprogramowania, takie jak umożliwienie użytkownikom przekazywania lub pobierania plików lub innych danych? Jeśli aplikacja wykonuje ryzykowne działania, należy rozważyć sposób, w jaki aplikacja będzie chronić użytkowników przed obsługą złośliwych plików lub danych.

### <a name="review-owasp-top-10"></a>Przejrzyj OWASP 10 pierwszych
Rozważ zapoznanie się z [<span class="underline">najważniejszymi zagrożeniami bezpieczeństwa aplikacji OWASP 10</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP 10 najpopularniejszych adresów stanowi krytyczne zagrożenie bezpieczeństwa aplikacji sieci Web.
Świadomość tych zagrożeń bezpieczeństwa może pomóc w podejmowaniu wymagań i decyzji projektowych, które minimalizują te zagrożenia w aplikacji.

Zapamiętaj o kontrolach zabezpieczeń, aby zapobiec naruszeniu naruszeń.
Jednak warto również założyć, że nastąpi [naruszenie](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) . Przy założeniu, że nastąpiło naruszenie, należy odpowiedzieć na kilka ważnych pytań dotyczących zabezpieczeń, aby nie musiały one być w stanie awaryjnym:

  - Jak wykrywa się atak?

  - Co należy zrobić w przypadku ataku lub naruszenia?

  - Jak odzyskiwać dane z ataku, takiego jak przecieki lub manipulowanie danymi?

## <a name="design"></a>Projekt

Faza projektowania ma kluczowe znaczenie dla ustanowienia najlepszych rozwiązań dotyczących projektowania i specyfikacji funkcjonalnych. Ma ona również kluczowe znaczenie dla przeprowadzania analizy ryzyka, która pomaga w ograniczeniu bezpieczeństwa i ochrony prywatności w całym projekcie.

Gdy są spełnione wymagania dotyczące zabezpieczeń i są używane bezpieczne koncepcje projektowe, można uniknąć lub zminimalizować szanse na wady zabezpieczeń. Luka w zabezpieczeniach jest nadzorem w projekcie aplikacji, która może pozwolić użytkownikowi na wykonywanie złośliwych lub nieoczekiwanych akcji po wydaniu aplikacji.

W fazie projektowania warto również zastanowić się, jak można zastosować zabezpieczenia w warstwach. jeden poziom obrony nie musi być wystarczająco duży. Co się stanie, jeśli osoba atakująca uzyska poprzednią zaporę aplikacji sieci Web (WAF)? Potrzebna jest inna kontrola zabezpieczeń w celu obrony przed atakami.

Z tego względu omawiamy następujące kwestie dotyczące bezpiecznego projektowania i kontroli zabezpieczeń, które należy rozwiązać podczas projektowania bezpiecznych aplikacji:

- Korzystaj z bezpiecznej biblioteki kodowania i struktury oprogramowania.
- Skanuj w poszukiwaniu zagrożonych składników.
- Korzystaj z modelowania zagrożeń podczas projektowania aplikacji.
- Zmniejsz obszar narażony na ataki.
- Należy przyjąć zasady tożsamości jako podstawowy obwód zabezpieczeń.
- Wymagane jest ponowne uwierzytelnienie dla ważnych transakcji.
- Użyj rozwiązania do zarządzania kluczami, aby zabezpieczyć klucze, poświadczenia i inne wpisy tajne.
- Ochrona poufnych danych.
- Zaimplementuj środki z bezpiecznymi uszkodzeniami.
- Skorzystaj z zalet obsługi błędów i wyjątków.
- Korzystanie z dzienników i alertów.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Korzystanie z bezpiecznej biblioteki kodowania i struktury oprogramowania

W przypadku programowania należy użyć bezpiecznej biblioteki kodowania i struktury oprogramowania, która ma wbudowane zabezpieczenia. Deweloperzy mogą korzystać z istniejących, sprawdzonych funkcji (szyfrowanie, wprowadzanie danych wyjściowych, Kodowanie wyjściowe, klucze lub parametry połączenia), a także wszystkie inne, które byłyby traktowane jako kontrola zabezpieczeń, zamiast tworzyć od podstaw kontrolę zabezpieczeń. Pomaga to chronić przed zagrożeniami dotyczącymi projektu i implementacji związanych z zabezpieczeniami.

Upewnij się, że korzystasz z najnowszej wersji platformy i wszystkich funkcji zabezpieczeń, które są dostępne w środowisku. Firma Microsoft oferuje kompleksowy [zestaw narzędzi programistycznych](https://azure.microsoft.com/product-categories/developer-tools/) dla wszystkich deweloperów, którzy pracują nad dowolną platformą lub językiem w celu dostarczania aplikacji w chmurze. Możesz dokonać kodu przy użyciu wybranego języka, wybierając spośród różnych [zestawów SDK](https://azure.microsoft.com/downloads/).
Możesz korzystać z w pełni funkcjonalnych, zintegrowanych środowisk programistycznych (środowisk IDE) i edytorów, które mają zaawansowane możliwości debugowania i wbudowaną pomoc techniczną platformy Azure.

Firma Microsoft oferuje wiele [języków, struktur i narzędzi](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) , których można użyć do tworzenia aplikacji na platformie Azure. Przykładem jest [platforma Azure dla deweloperów .NET i .NET Core](https://docs.microsoft.com/dotnet/azure/). Dla każdego oferowanego języka i platformy znajdziesz Przewodniki Szybki Start, samouczki i odwołania do interfejsów API, które ułatwiają szybkie rozpoczęcie pracy.

Platforma Azure oferuje różne usługi, których można używać do hostowania witryn i aplikacji sieci Web. Te usługi umożliwiają programowanie w ulubionym języku, niezależnie od tego, czy są to platformy .NET, .NET Core, Java, Ruby, Node.js, PHP czy Python.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) to jedna z tych usług.

Web Apps dodaje możliwości Microsoft Azure do aplikacji. Obejmuje to zabezpieczenia, równoważenie obciążenia, Skalowanie automatyczne i zautomatyzowane zarządzanie. Możesz również skorzystać z możliwości DevOps w Web Apps, takich jak zarządzanie pakietami, środowiska przejściowe, domeny niestandardowe, certyfikaty SSL/TLS i ciągłe wdrażanie z usługi Azure DevOps, GitHub, Docker Hub i innych źródeł.

Platforma Azure oferuje inne usługi, których można używać do hostowania witryn i aplikacji sieci Web. W przypadku większości scenariuszy najlepszym wyborem jest usługa Web Apps. W przypadku architektury mikrousług Rozważmy [Service Fabric platformy Azure](https://azure.microsoft.com/documentation/services/service-fabric).
W razie konieczności sprawowania większej kontroli nad maszynami wirtualnymi, na których działa kod, należy wziąć pod uwagę usługę [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Aby uzyskać więcej informacji na temat wybierania między tymi usługami platformy Azure, zobacz [porównanie Azure App Service, Virtual Machines, Service Fabric i Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Zastosuj aktualizacje do składników

Aby zapobiec występowaniu luk w zabezpieczeniach, należy ciągle prześledzić zarówno składniki po stronie klienta, jak i serwera (na przykład struktury i biblioteki) oraz ich zależności dla aktualizacji. Nowe luki w zabezpieczeniach i zaktualizowane wersje oprogramowania są udostępniane w sposób ciągły. Upewnij się, że masz ciągły plan monitorowania, klasyfikacja i stosowania aktualizacji lub zmian konfiguracji w używanych bibliotekach i składnikach.

Zobacz stronę [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) na stronie [Używanie składników z znanymi lukami](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) w poszukiwaniu sugestii dotyczących narzędzi. Możesz również subskrybować alerty e-mail dotyczące luk w zabezpieczeniach, które są związane ze składnikami, których używasz.

### <a name="use-threat-modeling-during-application-design"></a>Korzystanie z modelowania zagrożeń podczas projektowania aplikacji

Modelowanie zagrożeń polega na zidentyfikowaniu potencjalnych zagrożeń bezpieczeństwa dla Twojej firmy i aplikacji, a następnie zapewnieniu, że są odpowiednie środki zaradcze. SDL określa, że zespoły powinny należeć do modelowania zagrożeń podczas fazy projektowania, a rozwiązanie potencjalnych problemów jest stosunkowo proste i ekonomiczne. Korzystanie z modelowania zagrożeń w fazie projektowania może znacznie zmniejszyć całkowity koszt rozwoju.

Aby ułatwić proces modelowania zagrożeń, zaprojektowano [Threat Modeling Tool SDL](threat-modeling-tool.md) z innymi ekspertami. To narzędzie ułatwia modelowanie zagrożeń dla wszystkich deweloperów, oferując jasne wskazówki dotyczące tworzenia i analizowania modeli zagrożeń.

Modelowanie projektu aplikacji i wyliczanie [zagrożeń dotyczących](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) kroków — fałszowanie, manipulowanie, wyparcie, ujawnienie informacji, odmowa usługi i podniesienie uprawnień — w ramach wszystkich granic zaufania sprawdzono skuteczny sposób na wczesne przechwytywanie błędów projektu. W poniższej tabeli przedstawiono listę zagrożeń i przedstawiono przykładowe środki zaradcze korzystające z funkcji oferowanych przez platformę Azure. Te środki zaradcze nie będą działały w każdej sytuacji.

| Zagrożenie | Właściwość zabezpieczeń | Potencjalne ograniczenia dotyczące platformy Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Fałszowanie               | Uwierzytelnianie        | [Wymagaj połączeń HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manipulowanie              | Integralność             | Sprawdź poprawność certyfikatów SSL/TLS. Aplikacje korzystające z protokołu SSL/TLS muszą w pełni weryfikować certyfikaty X. 509 jednostek, z którymi się łączą. Użyj Azure Key Vault certyfikatów do [zarządzania certyfikatami x509](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates). |
| Wypieranie się            | Weryfikacja tożsamości       | Włącz [monitorowanie i diagnostykę](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)platformy Azure.|
| Ujawnienie informacji | Poufność       | Szyfruj [poufne dane przechowywane](../fundamentals/encryption-atrest.md) [i przesyłane](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Odmowa usługi      | Dostępność          | Monitoruj metryki wydajności pod kątem potencjalnych odmowy warunków usługi. Implementuj filtry połączeń. [Usługa Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md#next-steps), w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, zapewnia ochronę przed atakami na DDoS.|
| Podnoszenie uprawnień | Autoryzacja         | Użyj <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)Azure Active Directory.|

### <a name="reduce-your-attack-surface"></a>Zmniejszenie podatności na ataki

Obszar ataków to łączna suma, w której mogą wystąpić potencjalne luki w zabezpieczeniach. Ten dokument koncentruje się na podatności na ataki aplikacji.
Celem jest ochrona aplikacji przed atakami. Prostym i szybkim sposobem na zminimalizowanie obszaru ataków jest usunięcie nieużywanych zasobów i kodu z aplikacji. Im mniejsza jest Twoja aplikacja, tym mniejsza powierzchnia narażona na ataki. Na przykład Usuń:

- Kod dla funkcji, które nie zostały jeszcze wydane.
- Debugowanie kodu obsługi.
- Interfejsy sieciowe i protokoły, które nie są używane lub które zostały wycofane.
- Maszyny wirtualne i inne zasoby, które nie są używane.

Regularne czyszczenie zasobów i upewnienie się, że usunięcie nieużywanego kodu jest świetnym sposobem na zagwarantowanie, że istnieje mniej możliwości ataków złośliwych podmiotów.

Dokładniejszy i szczegółowy sposób zmniejszenia obszaru ataków polega na zakończeniu analizy obszaru ataków. Analiza obszaru ataków ułatwia zamapowanie części systemu, które muszą zostać sprawdzone i przetestowane pod kątem luk w zabezpieczeniach.

Celem analizy obszaru ataków jest zrozumienie obszarów ryzyka w aplikacji, dzięki czemu deweloperzy i specjaliści ds. zabezpieczeń wiedzą, jakie części aplikacji są otwarte w celu ataku. Następnie można znaleźć sposoby minimalizowania tego potencjału, śledzić czas i sposób zmiany obszaru ataków oraz to, co oznacza z perspektywy ryzyka.

Analiza obszaru ataków pomaga identyfikować:

- Funkcje i części systemu, które należy przejrzeć i przetestować pod kątem luk w zabezpieczeniach.
- Obszary wysokiego ryzyka dotyczące kodu, które wymagają ochrony przed szczegółami (części systemu, które należy chronić).
- W przypadku zmiany obszaru ataków i konieczności odświeżenia oceny zagrożeń.

Zmniejszenie możliwości ataków, które atakują wykorzystać potencjalne słabe miejsce lub luki w zabezpieczeniach, wymagają dokładnej analizy ogólnej obszaru ataków aplikacji. Obejmuje to również wyłączanie lub ograniczanie dostępu do usług systemowych, stosowanie zasady najniższych uprawnień oraz ochronę warstwową, wszędzie tam, gdzie to możliwe.

Omawiamy [przeprowadzenie przeglądu obszaru ataków](secure-develop.md#conduct-attack-surface-review) w trakcie fazy weryfikacji SDL.

> [!NOTE]
> **Jaka jest różnica między modelami zagrożeń i analizą podatności na ataki?**
Modelowanie zagrożeń to proces identyfikowania potencjalnych zagrożeń bezpieczeństwa aplikacji i zapewniania odpowiednich środków zaradczych dotyczących zagrożeń. Analiza obszaru ataków służy do identyfikowania obszarów o wysokim ryzyku, które są otwarte dla ataku. Obejmuje to znalezienie sposobów obrony obszarów wysokiego ryzyka aplikacji oraz przeglądanie i testowanie tych obszarów kodu przed wdrożeniem aplikacji.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Przyjmowanie zasad tożsamości jako podstawowego obwodu zabezpieczeń

Podczas projektowania aplikacji w chmurze ważne jest, aby zwiększyć fokus obwodu zabezpieczeń z podejścia skoncentrowanego na sieci do podejścia skoncentrowanego na tożsamości. W przeszłości podstawowy lokalny obwód zabezpieczeń był siecią organizacji. Większość lokalnych projektów zabezpieczeń używa sieci jako podstawowego przestawcy zabezpieczeń. W przypadku aplikacji w chmurze lepszym rozwiązaniem jest rozważanie tożsamości jako podstawowego obwodu zabezpieczeń.

Czynności, które można wykonać, aby opracować podejście zorientowane na tożsamość do tworzenia aplikacji sieci Web:

- Wymuś uwierzytelnianie wieloskładnikowe dla użytkowników.
- Używaj mocnych platform uwierzytelniania i autoryzacji.
- Stosuj zasadę najniższych uprawnień.
- Zaimplementuj dostęp just in Time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Wymuszanie uwierzytelniania wieloskładnikowego dla użytkowników

Użyj uwierzytelniania dwuskładnikowego. Uwierzytelnianie dwuskładnikowe jest bieżącym standardem uwierzytelniania i autoryzacji, ponieważ pozwala to uniknąć słabych zabezpieczeń, które są związane z typami nazw użytkowników i haseł uwierzytelniania. Dostęp do interfejsów zarządzania platformy Azure (Azure Portal/Remote PowerShell) i usług przeznaczonych dla klientów powinien zostać zaprojektowany i skonfigurowany do korzystania z usługi [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Korzystanie z mocnych platform uwierzytelniania i autoryzacji

Używanie mechanizmów uwierzytelniania i autoryzacji dostarczonych przez platformę zamiast kodu niestandardowego. Wynika to z faktu, że tworzenie niestandardowego kodu uwierzytelniania może być podatne na błędy. Kod komercyjny (na przykład od firmy Microsoft) często jest szeroko przeglądany pod kątem bezpieczeństwa. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) to rozwiązanie platformy Azure służące do zarządzania tożsamościami i dostępem. Te narzędzia i usługi Azure AD ułatwiają bezpieczne programowanie:

- [Microsoft Identity platform](/azure/active-directory/develop/) to zestaw składników, których deweloperzy używają do kompilowania aplikacji, które bezpiecznie logują użytkowników. Platforma wspiera deweloperów, którzy tworzą aplikacje korzystające z jednej dzierżawy i deweloperów, którzy chcą opracowywać aplikacje z wieloma dzierżawcami. Poza podstawową rejestracją aplikacje utworzone przy użyciu platformy tożsamości firmy Microsoft mogą wywoływać interfejsy API firmy Microsoft i niestandardowe interfejsy API. Platforma tożsamości firmy Microsoft obsługuje protokoły standardowe branżowe, takie jak OAuth 2,0 i OpenID Connect Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) to usługa zarządzania tożsamościami, za pomocą której można dostosowywać i kontrolować sposób tworzenia konta, logowania i zarządzania swoimi profilami przy użyciu aplikacji. Obejmuje to aplikacje, które są opracowywane dla systemów iOS, Android i .NET, między innymi. Azure AD B2C włącza te akcje przy ochronie tożsamości klientów.

#### <a name="apply-the-principle-of-least-privilege"></a>Stosuj zasadę najniższych uprawnień

Koncepcja [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) oznacza umożliwienie użytkownikom dokładnego poziomu dostępu i kontroli potrzebnych do wykonywania zadań i nic więcej.

Czy deweloper oprogramowania musi mieć prawa administratora domeny? Czy asystent administracyjny musi mieć dostęp do kontrolek administracyjnych na komputerze osobistym? Ocenianie dostępu do oprogramowania nie jest inne. Jeśli używasz [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md) w celu zapewnienia użytkownikom różnych możliwości i uprawnień w aplikacji, nie będziesz mieć dostępu do wszystkich elementów. Ograniczając dostęp do co jest wymagane dla każdej roli, należy ograniczyć ryzyko wystąpienia problemu z zabezpieczeniami.

Upewnij się, że aplikacja wymusza [najmniejsze uprawnienia](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) w całym wzorcu dostępu.

> [!NOTE]
> Reguły najniższych uprawnień muszą mieć zastosowanie do oprogramowania i osób tworzących oprogramowanie. Deweloperzy oprogramowania mogą być ogromnymi zagrożeniami związanymi z bezpieczeństwem IT, jeśli mają zbyt dużo dostępu. Konsekwencje mogą być poważne, jeśli deweloper ma złośliwe intencje lub ma za dużo dostępu. Zalecamy stosowanie reguł najniższych uprawnień do deweloperów w całym cyklu życia.

#### <a name="implement-just-in-time-access"></a>Implementowanie dostępu just in Time

Zaimplementuj dostęp *just-in-Time* (JIT), aby jeszcze bardziej obniżyć czas ekspozycji na uprawnienia. Użyj [Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-take-control-of-admin-activity) , aby:

- Nadaj użytkownikom uprawnienia, które potrzebują tylko JIT.
- Przypisywanie ról w skrócie czasu trwania z zachowaniem pewności, że uprawnienia są automatycznie odwoływane.

### <a name="require-re-authentication-for-important-transactions"></a>Wymagaj ponownego uwierzytelniania dla ważnych transakcji

[Sfałszowanie żądań między lokacjami](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (znane również jako *XSRF* lub *CSRF*) jest atakiem na aplikacje hostowane w sieci Web, w których złośliwa aplikacja internetowa ma wpływ na interakcję między przeglądarką klienta a aplikacją sieci Web, która ufa tej przeglądarce. Możliwe jest ataki między lokacjami, ponieważ przeglądarki sieci Web wysyłają automatyczne typy tokenów uwierzytelniania przy użyciu każdego żądania do witryny sieci Web.
Ta forma wykorzystywania jest również znana jako *atak dwukrotnego kliknięcia* lub *sesji* , ponieważ atakujący wykorzystuje wcześniej uwierzytelnioną sesję użytkownika.

Najlepszym sposobem obrony przed tym rodzajem ataku jest poproszenie użytkownika o coś, że tylko użytkownik może podać przed każdą ważną transakcją, taką jak zakup, Dezaktywacja konta lub zmiana hasła. Użytkownik może poproszony o ponowne wprowadzenie hasła, wykonanie CAPTCHA lub przesłanie klucza tajnego, który będzie miał tylko użytkownik. Najbardziej typowym podejściem jest token tajny.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Korzystanie z rozwiązania do zarządzania kluczami w celu zabezpieczania kluczy, poświadczeń i innych wpisów tajnych

Utrata kluczy i poświadczeń jest typowym problemem. Jedyną czynnością, która nie jest utrata kluczy i poświadczeń, jest posiadanie nieautoryzowanej strony dostępu do nich. Osoby atakujące mogą korzystać z zautomatyzowanych i ręcznych technik znajdowania kluczy i wpisów tajnych, które są przechowywane w repozytoriach kodu, takich jak GitHub. Nie należy umieszczać kluczy i wpisów tajnych w tych publicznych repozytoriach kodu ani na żadnym innym serwerze.

Zawsze umieszczaj klucze, certyfikaty, wpisy tajne i parametry połączenia w rozwiązaniu do zarządzania kluczami. Możesz użyć scentralizowanego rozwiązania, w którym klucze i wpisy tajne są przechowywane w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń). Platforma Azure udostępnia moduł HSM w chmurze z [Azure Key Vault](../../key-vault/general/overview.md).

Key Vault jest *magazynem wpisów tajnych*: jest to scentralizowana usługa w chmurze służąca do przechowywania wpisów tajnych aplikacji. Key Vault zabezpiecza poufne dane przez przechowywanie wpisów tajnych aplikacji w jednej, centralnej lokalizacji i zapewnianie bezpiecznego dostępu, kontroli uprawnień i rejestrowania dostępu.

Wpisy tajne są przechowywane w poszczególnych *magazynach*. Każdy magazyn ma własne zasady konfiguracji i zabezpieczeń umożliwiające kontrolę dostępu. Dostęp do danych można uzyskać za pomocą interfejsu API REST lub zestawu SDK klienta, który jest dostępny dla większości języków programowania.

> [!IMPORTANT]
> Azure Key Vault jest przeznaczona do przechowywania wpisów tajnych konfiguracji dla aplikacji serwerowych. Nie jest ona przeznaczona do przechowywania danych, które należą do użytkowników aplikacji. Znajduje to odzwierciedlenie w charakterystyce jej wydajności, interfejsie API i modelu kosztów.
>
> Dane użytkownika powinny być przechowywane w innym miejscu, podobnie jak w przypadku wystąpienia Azure SQL Database, które ma Transparent Data Encryption (TDE) lub na koncie magazynu korzystającym z usługi Azure szyfrowanie usługi Storage. Wpisy tajne, które są używane przez aplikację do uzyskiwania dostępu do tych magazynów danych, mogą być przechowywane w Azure Key Vault.

### <a name="protect-sensitive-data"></a>Ochrona poufnych danych

Ochrona danych jest istotną częścią strategii zabezpieczeń.
Klasyfikowanie danych i identyfikowanie potrzeb związanych z ochroną danych ułatwia projektowanie aplikacji z uwzględnieniem bezpieczeństwa danych. Klasyfikowanie (kategoryzowanie) przechowywanych danych według czułości i wpływu na działalność biznesową ułatwia deweloperom określenie ryzyka związanego z danymi.

Oznacz wszystkie odpowiednie dane jako poufne podczas projektowania formatów danych. Upewnij się, że aplikacja traktuje odpowiednie dane jako poufne. Te praktyki mogą pomóc w ochronie poufnych danych:

- Użyj szyfrowania.
- Unikaj twardych wpisów tajnych, takich jak klucze i hasła.
- Upewnij się, że są stosowane kontrole dostępu i inspekcje.

#### <a name="use-encryption"></a>Użyj szyfrowania

Ochrona danych powinna być istotną częścią strategii zabezpieczeń.
Jeśli dane są przechowywane w bazie danych lub przenoszone z powrotem między lokalizacjami, użyj szyfrowania danych przechowywanych w [spoczynku](../fundamentals/encryption-atrest.md) (w bazie danych) i szyfrowania [danych w trakcie przesyłania](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (w drodze do i od użytkownika, bazy danych, interfejsu API lub punktu końcowego usługi). Zaleca się, aby do wymiany danych zawsze używać protokołów SSL/TLS. Upewnij się, że używasz najnowszej wersji protokołu TLS do szyfrowania (obecnie jest to wersja 1,2).

#### <a name="avoid-hard-coding"></a>Unikaj kodowania twardych

Niektóre elementy nie powinny być trwale kodowane w oprogramowaniu. Przykładami są nazwy hostów lub adresy IP, adresy URL, adresy e-mail, nazwy użytkowników, hasła, klucze konta magazynu i inne klucze kryptograficzne. Rozważ zaimplementowanie wymagań dotyczących elementów, które mogą lub nie mogą być zakodowane w kodzie, włącznie z sekcjami komentarza w kodzie.

Po umieszczeniu komentarzy w kodzie upewnij się, że nie zapisano żadnych poufnych informacji. Obejmuje to Twój adres e-mail, hasła, parametry połączenia, informacje o aplikacji, które będą znane tylko przez kogoś w organizacji, i wszelkie inne osoby, które mogą dać atakującemu korzyść w odniesieniu do ataku aplikacji lub organizacji.

W zasadzie założono, że wszystko w projekcie deweloperskim będzie publiczną wiedzą podczas jej wdrażania. Należy unikać uwzględniania poufnych danych dowolnego rodzaju w projekcie.

Wcześniej omówione [Azure Key Vault](../../key-vault/general/overview.md). Za pomocą Key Vault można przechowywać wpisy tajne, takie jak klucze i hasła, zamiast ich kodowania. Jeśli używasz Key Vault w połączeniu z tożsamościami zarządzanymi dla zasobów platformy Azure, Twoja aplikacja internetowa platformy Azure może łatwo i bezpiecznie uzyskiwać dostęp do wartości konfiguracji wpisów tajnych bez przechowywania wpisów tajnych w kontroli źródła lub konfiguracji. Aby dowiedzieć się więcej, zobacz Zarządzanie wpisami [tajnymi w aplikacjach serwerowych za pomocą Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementowanie bezpiecznych miar zakończonych niepowodzeniem

Aplikacja musi być w stanie obsługiwać [Błędy](https://docs.microsoft.com/dotnet/standard/exceptions/) występujące podczas wykonywania w spójny sposób. Aplikacja powinna wychwycić wszystkie błędy i spowodować, że są one bezpieczne lub zamknięte.

Należy również upewnić się, że błędy są rejestrowane przy użyciu wystarczającego kontekstu użytkownika w celu zidentyfikowania podejrzanych lub złośliwych działań. Dzienniki powinny być przechowywane przez wystarczająco dużo czasu, aby umożliwić opóźnioną analizę śledczej. Dzienniki powinny być w formacie, który może być łatwo wykorzystany przez rozwiązanie do zarządzania dziennikami. Upewnij się, że wyzwalane są alerty dotyczące błędów, które są związane z zabezpieczeniami. Niewystarczające rejestrowanie i monitorowanie pozwala osobom atakującym na dalsze ataki i utrzymywanie trwałości.

### <a name="take-advantage-of-error-and-exception-handling"></a>Korzystanie z zalet błędów i obsługi wyjątków

Implementacja poprawnego błędu i [obsługi wyjątków](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) jest ważną częścią kodowania obronnego. Obsługa błędów i wyjątków ma kluczowe znaczenie dla zapewnienia niezawodnego i bezpiecznego działania systemu. Błędy w obsłudze błędów mogą prowadzić do różnych rodzajów luk w zabezpieczeniach, takich jak ujawnienie informacji osobom atakującym i pomaganie atakującym dowiedzieć się więcej na temat platformy i projektu.

Upewnij się, że:

- Wyjątki są obsługiwane w sposób scentralizowany, aby uniknąć duplikowania [bloków try/catch](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) w kodzie.

- Wszystkie nieoczekiwane zachowania są obsługiwane wewnątrz aplikacji.

- Komunikaty, które są wyświetlane użytkownikom, nie mogą wyciekać najważniejszych danych, ale zawierają wystarczające informacje, aby wyjaśnić problem.

- Wyjątki są rejestrowane i zapewniają wystarczające informacje dla zespołów dowodowych lub odpowiedzi na zdarzenia do zbadania.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) zapewnia środowisko pierwszej klasy do [obsługi błędów i wyjątków](../../logic-apps/logic-apps-exception-handling.md) , które są spowodowane przez systemy zależne. Za pomocą Logic Apps można tworzyć przepływy pracy służące do automatyzowania zadań i procesów, które integrują aplikacje, dane, systemy i usługi między przedsiębiorstwami i organizacjami.

### <a name="use-logging-and-alerting"></a>Korzystanie z rejestrowania i alertów

[Rejestruj](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) problemy dotyczące zabezpieczeń i Wyzwalaj alerty dotyczące problemów, aby upewnić się, że ludzie wiedzą o problemach w odpowiednim czasie. Włącz inspekcję i rejestrowanie na wszystkich składnikach. Dzienniki inspekcji powinny przechwytywać kontekst użytkownika i identyfikować wszystkie ważne zdarzenia.

Sprawdź, czy nie są rejestrowane żadne poufne dane przesyłane przez użytkownika do witryny. Przykładowe dane poufne to:

- Poświadczenia użytkownika
- Numery ubezpieczenia społecznego lub inne informacje identyfikujące
- Numery kart kredytowych lub inne informacje finansowe
- Informacje o kondycji
- Klucze prywatne lub inne dane, których można użyć do odszyfrowania zaszyfrowanych informacji
- Informacje o systemie lub aplikacji, których można użyć do bardziej efektywnego ataku na aplikację

Upewnij się, że aplikacja monitoruje zdarzenia zarządzania użytkownikami, takie jak pomyślne i nieudane logowania użytkowników, resetowanie haseł, zmiana hasła, blokada konta i Rejestracja użytkownika. Rejestrowanie tych zdarzeń ułatwia wykrywanie potencjalnie podejrzanych zachowań i reagowanie na nie. Pozwala również zbierać dane operacyjne, takie jak osoba, która uzyskuje dostęp do aplikacji.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zalecamy mechanizmy kontroli zabezpieczeń i działania, które mogą pomóc w tworzeniu i wdrażaniu bezpiecznych aplikacji.

- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
- [Wdrażanie bezpiecznych aplikacji](secure-deploy.md)
