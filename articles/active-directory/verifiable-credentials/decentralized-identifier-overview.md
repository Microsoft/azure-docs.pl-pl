---
title: Wprowadzenie do Azure Active Directory poświadczenia do zweryfikowania (wersja zapoznawcza)
description: Przegląd poświadczeń do zweryfikowania platformy Azure.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 04b36b9b32e78016f693e61d40246776492be0e3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222864"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Wprowadzenie do Azure Active Directory poświadczenia do zweryfikowania (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nasze cyfrowe i fizyczne zasoby są coraz bardziej połączone z aplikacjami, usługami i urządzeniami używanymi do uzyskiwania dostępu do bogatego zestawu środowisk. Ta transformacja cyfrowa pozwala nam na współdziałanie z setkami firm i tysiącami innych użytkowników w sposób, który był wcześniej nieurojony.

Jednak dane tożsamości zostały zbyt często ujawnione w przypadku naruszeń zabezpieczeń. Takie naruszenia mają wpływ na działalność osób mających wpływ na nasze kwestie społeczne, profesjonalne i finansowe. Firma Microsoft uważa, że jest to lepszy sposób. Każda osoba ma prawo do tożsamości, która posiada, i kontroluje, która bezpiecznie przechowuje elementy swojej tożsamości cyfrowej i zachowuje prywatność. W tym przewodniku wyjaśniono, jak jesteśmy zachęcamy do korzystania z różnorodnych społeczności, aby utworzyć otwarte, wiarygodne, współdziałające i oparte na standardach rozwiązanie zdecentralizowane tożsamość (i) dla osób i organizacji.

## <a name="why-we-need-decentralized-identity"></a>Dlaczego potrzebna jest zdecentralizowana tożsamość

Dzisiaj korzystamy z naszej tożsamości cyfrowej w pracy, w domu i w każdej aplikacji, usłudze i używanej przez nas urządzeniu. Składają się z wszystkiego, co mamy, do tego, i doświadczenia w życie — kupowanie biletów na wydarzenie, ewidencjonowanie hotelu lub nawet porządkowanie. Obecnie nasza tożsamość i wszystkie nasze interakcje cyfrowe są własnością i są kontrolowane przez inne osoby. niektórzy z nich jeszcze nie wiedzą.

Ogólnie rzecz biorąc, użytkownicy wyrażają zgodę na kilka aplikacji i urządzeń. Takie podejście wymaga wysokiego stopnia czujki na części użytkownika w celu śledzenia, kto ma dostęp do informacji. Na frontonie przedsiębiorstwa współpraca z klientami i partnerami wymaga aranżacji o wysokiej dotyku, aby bezpiecznie wymieniać dane w taki sposób, aby zachować prywatność i bezpieczeństwo wszystkich uczestników.

Uważamy, że system tożsamości zdecentralizowanej oparty na standardach może odblokować nowy zestaw środowisk, dzięki którym użytkownicy i organizacje mają większą kontrolę nad swoimi danymi — i zapewniają wyższy poziom zaufania i zabezpieczeń dla aplikacji, urządzeń i dostawców usług.

## <a name="lead-with-open-standards"></a>Potencjalni klienci dzięki otwartym standardom

Firma Microsoft zobowiązała się do współpracy z klientami, partnerami i społecznością w celu odblokowania nowej generacji zdecentralizowanych środowisk opartych na tożsamościach, a firma Microsoft przyjemnością się z partnerami i organizacjami, które wprowadzają niezawodne udziały w tym miejscu. Jeśli ekosystem ma zostać powiększony, standardy, składniki techniczne i elementy dostarczane przez kod muszą być otwarte i dostępne dla wszystkich.

Firma Microsoft aktywnie współpracuje z członkami zdecentralizowanego programu Identity Foundation (DIF), grupą wspólnotową poświadczeń W3C i szerszym społecznością tożsamości. Pracujemy nad tymi grupami, aby identyfikować i opracowywać standardy krytyczne, a w naszych usługach zostały zaimplementowane następujące standardy.

* [Identyfikatory z decentralizacji W3C](https://www.w3.org/TR/did-core/)
* [Poświadczenia podlegające weryfikacji W3C](https://www.w3.org/TR/vc-data-model/)
* [Sidetree DIF](https://identity.foundation/sidetree/spec/)
* [Znana konfiguracja dobrze znanego DIF](https://identity.foundation/specs/did-configuration/)
* [SIOP](https://identity.foundation/did-siop/)
* [Program wymiany prezentacji DIF](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Co to są DIDs?

Zanim będziemy mogli zrozumieć DIDs, ułatwiają one porównywanie ich z bieżącymi systemami tożsamości. Adresy e-mail i identyfikatory sieci społecznościowych to przyjazne dla człowieka aliasy do współpracy, ale teraz są przeciążone, aby zapewnić, że punkty kontroli dostępu do danych w wielu scenariuszach wykraczają poza współpracę. Powoduje to utworzenie potencjalnego problemu, ponieważ dostęp do tych identyfikatorów może zostać usunięty w dowolnym momencie przez strony zewnętrzne.

Identyfikatory zdecentralizowane (DIDs) są różne. DIDs są wygenerowanymi przez użytkownika, unikatowymi identyfikatorami globalnymi, które zostały odblokowane w systemach zdecentralizowanych, takich jak jonu. Mają one unikatowe cechy, takie jak większa gwarancja niezmienności, odporność Censorship i manipulowanie evasiveness. Te atrybuty mają krytyczne znaczenie dla dowolnego systemu identyfikatora, który jest przeznaczony do zapewnienia własności i kontroli użytkownika. 

Rozwiązanie do weryfikowania poświadczeń firmy Microsoft używa zdecentralizowanych poświadczeń (DIDs) w celu kryptograficznego podpisywania jako dowodu, że jednostka uzależniona (weryfikatorer) jest zaświadczana do informacji potwierdzających, że są właścicielami poświadczenia możliwe do zweryfikowania. W związku z tym, zaleca się, aby każda osoba tworząca zweryfikowane rozwiązanie w oparciu o ofertę firmy Microsoft zaleciła sobie podstawową wiedzę na temat zdecentralizowanych identyfikatorów.
## <a name="what-are-verifiable-credentials"></a>Co to są poświadczenia do zweryfikowania?

 Identyfikatory są używane w codziennych okresach. Mamy licencje na sterowniki, które są używane jako dowód naszej zdolności do obsługi samochodu. Uniwersytety wydające dyplomy, które udowodnią osiągnięcie poziomu edukacji. Korzystamy z paszportów, aby udowodnić, kto jest odpowiedzialny za przybycie do innych krajów. Model danych opisuje, jak możemy obsłużyć te typy scenariuszy podczas pracy z Internetem, ale w bezpieczny sposób, który szanuje prywatność użytkownika. Dodatkowe informacje można uzyskać w [modelu danych z zweryfikowanymi poświadczeniami 1,0](https://www.w3.org/TR/vc-data-model/)

W skrócie, zweryfikowane poświadczenia to obiekty danych składające się z oświadczeń podjętych przez wystawcę zaświadczania informacji o temacie. Te oświadczenia są identyfikowane przez schemat i zawierają wystawcy oraz podmiot. Wystawcy tworzy podpis cyfrowy jako dowód potwierdzający zaświadczenie tych informacji.


## <a name="how-does-decentralized-identity-work"></a>Jak działa zdecentralizowana tożsamość?

Potrzebujemy nowej formy tożsamości. Potrzebujemy tożsamości, która łączy technologie i standardy, aby dostarczyć kluczowe atrybuty tożsamości, takie jak własność samowłasności i odporność Censorship. Te możliwości są trudne do osiągnięcia przy użyciu istniejących systemów.

Aby dostarczać te niesie obietnice zwiększenia, musimy mieć podstawę techniczną składającą się z siedmiu innowacji. Jedną z kluczowych innowacji są identyfikatory, które należą do użytkownika, agent użytkownika do zarządzania kluczami skojarzonymi z takimi identyfikatorami oraz zaszyfrowane, kontrolowane przez użytkownika magazyny danych.

![Omówienie zweryfikowanego środowiska poświadczeń firmy Microsoft](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. identyfikatory zdecentralizowane W3C (DIDs)** , które użytkownicy tworzą, są właścicielami i kontrolują niezależnie od organizacji lub instytucji rządowych. DIDs są globalnie unikatowymi identyfikatorami połączonymi z zdecentralizowanymi metadanymi infrastruktury kluczy publicznych (DPKI), które składają się z dokumentów JSON zawierających materiał klucza publicznego, deskryptory uwierzytelniania i punkty końcowe usługi.

**2. Niescentralizowany system: jonu (sieć nakładki tożsamości)** jest otwartą warstwą 2, bez uprawnień na podstawie czystego Sidetree protokołu, który nie wymaga żadnych specjalnych tokenów, zaufanych modułów sprawdzania poprawności ani innych konsensusych mechanizmów; liniowy postęp łańcucha czasu Bitcoin jest wymagany do jego działania. Firma Microsoft [otworzyła pakiet npm](https://www.npmjs.com/package/@decentralized-identity/ion-tools) , aby pracować z siecią jonu łatwą w integracji z aplikacjami i usługami. Biblioteki obejmują tworzenie nowych, generowanie kluczy i zakotwiczenie w Bitcoin łańcucha bloków. 

**3. Agent użytkownika/portfel: aplikacja Microsoft Authenticator** umożliwia prawdziwym osobom korzystanie z zdecentralizowanych tożsamości i zweryfikowanych poświadczeń. Wystawca tworzy DIDs, ułatwia żądania wystawiania i prezentacji w celu zweryfikowania poświadczeń i zarządza kopią zapasową Twojego inicjatora przez zaszyfrowany plik portfela.

**4. program Microsoft rozpoznawania nazw** API, który łączy się z naszym WĘZŁem jonu, aby wyszukiwać i rozwiązywać DIDs przy użyciu ```did:ion``` metody i zwracać obiekt DDO. DDO zawiera metadane DPKI skojarzone z, takie jak klucze publiczne i punkty końcowe usługi. 

**5. Azure Active Directory zweryfikowane usługi poświadczeń** — interfejs API wystawiania i weryfikacji oraz zestaw SDK open source dla poświadczeń z weryfikacją [W3C](https://www.w3.org/TR/vc-data-model/) , które są podpisane za pomocą ```did:ion``` metody. Umożliwiają one właścicielom tożsamości generowanie, prezentowanie i weryfikowanie oświadczeń. Stanowi to podstawę zaufania między użytkownikami systemów.

## <a name="a-sample-scenario"></a>Przykładowy scenariusz

Scenariusz, którego używamy, aby wyjaśnić, jak działa system VCs:

- Woodgrove Inc. firma.
- Proseware, firma, która oferuje rabaty dla pracowników Woodgrove.
- Alicja, pracownik na Woodgrove, Inc., który chce uzyskać rabat z proseware



Obecnie Alicja udostępnia nazwę użytkownika i hasło, aby zalogować się do środowiska sieci Woodgrove. Woodgrove wdraża rozwiązanie VC, aby zapewnić bardziej łatwą w zarządzaniu metodę dla Alicja, która udowodni, że jest pracownikiem Woodgrove. Proseware używa rozwiązania VC zgodnego z rozwiązaniem VC usługi Woodgrove oraz akceptuje poświadczenia wystawione przez Woodgrove jako dowód zatrudnienia.

Wystawca poświadczenie, Woodgrove Inc., tworzy klucz publiczny i klucz prywatny. Klucz publiczny jest przechowywany w ramach jonu. Gdy klucz zostanie dodany do infrastruktury, wpis jest rejestrowany w zdecentralizowanej księdze opartej na łańcucha bloków. Wystawca zapewnia Alicja klucz prywatny, który jest przechowywany w aplikacji portfela. Za każdym razem, gdy Alicja pomyślnie używa klucza prywatnego, transakcja jest rejestrowana w aplikacji portfela.

![Microsoft — przegląd](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Role w ramach rozwiązania do zweryfikowania poświadczeń 

W rozwiązaniu z poświadczeniami możliwymi do zweryfikowania istnieją trzy główne podmioty. Na poniższym diagramie:

- **Krok 1** **użytkownik** żąda poświadczenia do zweryfikowania od wystawcy.
- **Krok 2**. **wystawca** poświadczenia zaświadcza, że sprawdzana przez użytkownika jest dokładna i tworzy zweryfikowane poświadczenia podpisane przy użyciu swojego konta, a użytkownik jest podmiotem.
- **W kroku 3** użytkownik podpisuje prezentację do zweryfikowania (wiceprezes) i wyśle ją do **weryfikatora.** Weryfikator sprawdza i sprawdza poprawność poświadczeń przez dopasowanie do klucza publicznego umieszczonego w DPKI.

Role w tym scenariuszu są następujące:

![role w zweryfikowanym środowisku poświadczeń](media/decentralized-identifier-overview/issuer-user-verifier.png)

**wystawca** — wystawca to organizacja, która tworzy rozwiązanie do wystawiania żądające informacji od użytkownika. Informacje są używane do weryfikowania tożsamości użytkownika. Na przykład Woodgrove, Inc. ma rozwiązanie do wystawiania, które umożliwia im tworzenie i dystrybuowanie poświadczeń do zweryfikowania (VCs) do wszystkich pracowników. Pracownik używa aplikacji Authenticator, aby zalogować się przy użyciu nazwy użytkownika i hasła, która przekazuje token identyfikatora do usługi wystawiania. Po Woodgrove, Inc. sprawdza poprawność przesłanego tokenu identyfikatora, rozwiązanie wystawiania tworzy obwód wirtualny zawierający oświadczenia dotyczące pracownika i jest podpisany za pomocą Woodgrove, Inc.. Pracownik ma teraz zweryfikowane poświadczenia, które są podpisywane przez pracodawcę, który obejmuje pracowników, którzy przeprowadzili ten podmiot.  

**użytkownik** — użytkownik jest osobą lub jednostką żądającą VC. Na przykład Alicja jest nowym pracownikiem Woodgrove i Inc. wcześniej wystawiła swoją weryfikację poświadczeń do zweryfikowania zatrudnienia. Gdy Alicja musi zapewnić potwierdzenie zatrudnienia w celu uzyskania rabatu w witrynie proseware, może udzielić dostępu do poświadczeń w swojej aplikacji uwierzytelniającej, podpisując prezentację, która udowadnia Alicja, jest właścicielem elementu. Proseware jest w stanie potwierdzić, że poświadczenie zostało wystawione przez Woodgrove, Inc. i Alicja jest właścicielem poświadczenia. 

**weryfikator** — weryfikator jest firmą lub podmiotem, który musi zweryfikować oświadczenia z jednego lub kilku wystawców, którym ufają. Na przykład proseware ufa administratorom Woodgrove, Inc., czy jest to odpowiednie zadanie weryfikacji tożsamości pracowników i wystawiania autentycznych i prawidłowych obwodów. Gdy Alicja podejmie próbę uporządkowania sprzętu potrzebnego do jej działania, proseware będzie używać otwartych standardów, takich jak SIOP i wymiana prezentacji, aby żądać poświadczeń od użytkownika, którzy potwierdzają, że są pracownikami Woodgrove, Inc. Na przykład proseware może dostarczyć Alicja link do witryny sieci Web z kodem QR, który skanuje z jego kamerą telefoniczną. Spowoduje to zainicjowanie żądania dotyczącego określonego wirtualnego VC, który zostanie przeanalizowany przez wystawcę i pozwoli mu na zatwierdzenie żądania potwierdzenia jej zatrudnienia do proseware. Proseware może użyć interfejsu API usługi poświadczeń do weryfikacji lub zestawu SDK, aby sprawdzić autentyczność prezentacji możliwej do zweryfikowania. W oparciu o informacje udostępniane przez program Alicja przyznają one rabat. Jeśli inne firmy i organizacje wiedzą, że firma Woodgrove, Inc. wydaje się ich pracownikom, może również utworzyć rozwiązanie weryfikatora i użyć poświadczenia do weryfikacji Woodgrove, Inc., aby udostępnić specjalne oferty zastrzeżone dla instytucji Woodgrove, Inc. Employees.

## <a name="next-steps"></a>Następne kroki

Teraz, znając informacje o DIDs i zweryfikowaniu poświadczeń, wypróbuj je samodzielnie, postępując zgodnie z artykułem wprowadzenie lub jednym z naszych artykułów, które zawierają więcej szczegółowych informacji na temat zweryfikowanych pojęć związanych z poświadczeniami.

- [Wprowadzenie do zweryfikowania poświadczeń](get-started-verifiable-credentials.md)
- [Jak dostosować swoje poświadczenia](credential-design.md)
- [Zweryfikowane poświadczenia — często zadawane pytania](verifiable-credentials-faq.md)