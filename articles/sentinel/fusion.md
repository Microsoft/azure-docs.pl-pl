---
title: Zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure — wskaźnik
description: Skorzystaj z technologii Fusion na platformie Azure wskaźnikowej, aby zmniejszyć zmęczenie alertu i utworzyć zdarzenia umożliwiające podejmowanie działań, które opierają się na zaawansowaniu wykrywania ataków potokach wieloetapowych.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724289"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure — wskaźnik

> [!IMPORTANT]
> Niektóre wykrywania syntezy (Zobacz te, które zostały wskazane poniżej) są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Korzystając z technologii Fusion na podstawie uczenia maszynowego, wskaźnik platformy Azure może automatycznie wykrywać ataki potokach wieloetapowych, identyfikując kombinacje nietypowych zachowań i podejrzanych działań, które są obserwowane na różnych etapach łańcucha kasowania. W oparciu o te odnajdywania wskaźnik produktów platformy Azure będzie generował zdarzenia, które w przeciwnym razie byłyby trudne do przechwycenia. Zdarzenia te obejmują dwa lub więcej alertów lub działań. Zgodnie z projektem te zdarzenia to niskie ilości, wysokiej jakości i wysokiej ważności.

Ta technologia wykrywania jest dostosowywana do środowiska, ale nie tylko skracają fałszywe dodatnie stawki, ale mogą również wykrywać ataki z ograniczoną lub brakującymi informacjami.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguracja zaawansowanego wieloetapowego wykrywania ataków

To wykrywanie jest domyślnie włączone w wskaźniku na platformie Azure. Aby sprawdzić stan lub wyłączyć go w przypadku użycia alternatywnego rozwiązania do tworzenia zdarzeń na podstawie wielu alertów, należy wykonać następujące instrukcje:

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do **usługi Azure wskaźnik**  >  **konfiguracji**  >  **analizy**

1. Wybierz pozycję **aktywne reguły**, a następnie Znajdź **Zaawansowane wykrywanie ataków potokach wieloetapowych** w kolumnie **Nazwa** , filtrując listę dla typu reguły **Fusion** . Sprawdź kolumnę **stan** , aby sprawdzić, czy to wykrywanie jest włączone czy wyłączone.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Aby zmienić stan, wybierz ten wpis i w bloku **Zaawansowane wykrywanie ataków potokach wieloetapowych** wybierz pozycję **Edytuj**.

1. W bloku **Kreator tworzenia reguł** zmiana stanu jest automatycznie wybierana, więc wybierz kolejno pozycje **Dalej: przegląd** i **Zapisz**. 

 Ponieważ typ reguły **Fusion** zawiera tylko jedną regułę, której nie można modyfikować, szablony reguł nie mają zastosowania do tego typu reguły.

> [!NOTE]
> Dane historyczne platformy Azure są obecnie wykorzystywane do uczenia systemów uczenia maszynowego w ciągu 30 dni od danych historycznych. Te dane są zawsze szyfrowane przy użyciu kluczy firmy Microsoft, które są przekazywane przez potok uczenia maszynowego. Jednak dane szkoleniowe nie są szyfrowane przy użyciu [kluczy zarządzanych przez klienta (CMK)](customer-managed-keys.md) , jeśli włączono CMK w obszarze roboczym wskaźnikowego platformy Azure. Aby zrezygnować z fuzji, przejdź do obszaru **Azure wskaźnik** \> **konfiguracji** \> **analizy \> \>** , a następnie w kolumnie **stan** kliknij pozycję **Wyłącz.**

## <a name="attack-detection-scenarios"></a>Scenariusze wykrywania ataków

W poniższej sekcji przedstawiono typy scenariuszy korelacji pogrupowanych według klasyfikacji zagrożeń, które wskaźnik platformy Azure szuka przy użyciu technologii Fusion.

Jak wspomniano powyżej, ponieważ fuzja skorelować wiele alertów zabezpieczeń z różnych produktów, aby wykrywać zaawansowane ataki potokach wieloetapowych, pomyślne wykrywanie sieci są prezentowane jako **zdarzenia syntezy** na stronie **incydentów** platformy Azure, a nie jako **alerty** w tabeli **alertów zabezpieczeń** w **dziennikach**.

Aby umożliwić te scenariusze wykrywania ataków opartych na fuzji, wszystkie źródła danych wymienione na liście muszą być pozyskiwane przy użyciu skojarzonych łączników danych wskaźnikowych platformy Azure.

> [!NOTE]
> Niektóre z tych scenariuszy są w **wersji zapoznawczej**. Zostanie to wskazane.

## <a name="compute-resource-abuse"></a>Użycie zasobów obliczeniowych

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Działania tworzenia wielu maszyn wirtualnych następujące po podejrzanym zalogowaniu Azure Active Directory
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, wpływ 

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), przejmowanie zasobów (T1496)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że anomalia liczba maszyn wirtualnych została utworzona w ramach jednej sesji po podejrzanej rejestracji na koncie usługi Azure AD. Ten typ alertu wskazuje, że z wysokim poziomem pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i użyte do utworzenia nowych maszyn wirtualnych dla nieautoryzowanych celów, takich jak uruchamianie operacji wyszukiwania kryptograficznego. Permutacje podejrzanych alertów logowania do usługi Azure AD z alertami dotyczącymi tworzenia wielu maszyn wirtualnych są następujące:

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do działania tworzenia wielu maszyn wirtualnych**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do działania tworzenia wielu maszyn wirtualnych**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do działania tworzenia wielu maszyn wirtualnych**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do działania tworzenia wielu maszyn wirtualnych**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do działania tworzenia wielu maszyn wirtualnych**

## <a name="credential-harvesting-new-threat-classification"></a>Zbieranie poświadczeń (Nowa Klasyfikacja zagrożeń)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Wykonywanie złośliwego narzędzia kradzieży poświadczeń po podejrzanym logowaniu

**Mitre ATT&taktykę:** Dostęp wstępny, dostęp do poświadczeń

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), dumping poświadczeń systemu operacyjnego (T1003)

**Źródła łącznika danych:** Azure Active Directory Identity Protection, Microsoft Defender dla punktu końcowego

**Opis:** Zdarzenie Fusion tego typu wskazuje, że zostało wykonane znane narzędzie kradzieży poświadczeń po podejrzanym zalogowaniu do usługi Azure AD. Zapewnia to wysoki poziom pewności, że konto użytkownika zanotowane w opisie alertu zostało naruszone i może pomyślnie użyć narzędzia, takiego jak **program mimikatz** , aby zebrać poświadczenia, takie jak klucze, hasła w postaci zwykłego tekstu i/lub skróty haseł z systemu. Zebrane poświadczenia mogą pozwolić atakującemu na dostęp do poufnych danych, podwyższyć poziom uprawnień i/lub przenieść je w sieci. W przypadku alertów o podejrzanych alertach dotyczących logowania do usługi Azure AD z bezpiecznym narzędziem kradzieży poświadczeń są następujące:

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do wykonania złośliwego narzędzia kradzieży poświadczeń**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do wykonania złośliwego narzędzia kradzieży poświadczeń**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do wykonania złośliwego narzędzia kradzieży poświadczeń**

- **Zdarzenie logowania z anonimowego adresu IP prowadzące do wykonania złośliwego narzędzia kradzieży poświadczeń**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do wykonania złośliwego narzędzia kradzieży poświadczeń**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Podejrzane działanie kradzieży poświadczeń po podejrzanym logowaniu

**Mitre ATT&taktykę:** Dostęp wstępny, dostęp do poświadczeń

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), poświadczenia z magazynów haseł (T1555), zatapianie poświadczeń systemu operacyjnego (T1003)

**Źródła łącznika danych:** Azure Active Directory Identity Protection, Microsoft Defender dla punktu końcowego

**Opis:** Zdarzenia Fusion tego typu wskazują, że działanie skojarzone ze wzorcem kradzieży poświadczeń nastąpiło po podejrzanym zalogowaniu do usługi Azure AD. Zapewnia to wysoki poziom pewności, że konto użytkownika zanotowane w opisie alertu zostało naruszone i użyte do kradzieży poświadczeń, takich jak klucze, hasła w postaci zwykłego tekstu, skróty haseł i tak dalej. Skradzione poświadczenia mogą umożliwić osobie atakującej dostęp do poufnych danych, podwyższyć poziom uprawnień i/lub przenieść je w sieci. Permutacja podejrzanych alertów dotyczących logowania do usługi Azure AD za pomocą alertu dotyczącego kradzieży poświadczeń są następujące:

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do podejrzanego działania kradzieży poświadczeń**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanego działania kradzieży poświadczeń**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanego działania kradzieży poświadczeń**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanego działania kradzieży poświadczeń**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanego działania kradzieży poświadczeń**

## <a name="crypto-mining-new-threat-classification"></a>Wydobycie kryptograficzne (Nowa Klasyfikacja zagrożeń)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Działanie wyszukiwania kryptograficznego po podejrzanym logowaniu

**Mitre ATT&taktykę:** Dostęp wstępny, dostęp do poświadczeń

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), przejmowanie zasobów (T1496)

**Źródła łącznika danych:** Azure Active Directory Identity Protection, Azure Defender (Azure Security Center)

**Opis:** Zdarzenia Fusion tego typu wskazują działanie wyszukiwania kryptograficznego powiązane z podejrzanym logowaniem do konta usługi Azure AD. Zapewnia to wysoki poziom pewności, że konto użytkownika zanotowane w opisie alertu zostało naruszone i zostało użyte do przejęcia kontroli nad zasobami w danym środowisku. Może to spowodować zablokować dostęp zasobów obliczeniowych i/lub znacznie wyższe niż oczekiwane rachunki użycia w chmurze. Permutacje podejrzanych alertów logowania do usługi Azure AD za pomocą alertu dotyczącego działań związanych z wyszukiwaniem kryptografii są następujące:  

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do działania wyszukiwania kryptograficznego**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do działania wyszukiwania kryptograficznego**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do działania wyszukiwania kryptograficznego**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do działania wyszukiwania kryptograficznego**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do działania wyszukiwania kryptograficznego**

## <a name="data-exfiltration"></a>Eksfiltracja danych

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Skrzynka pocztowa pakietu Office 365 eksfiltracji po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, Eksfiltracji, kolekcja

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), kolekcja poczty E-mail (T1114), zautomatyzowane Eksfiltracji (T1020)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że w skrzynce odbiorczej użytkownika została ustawiona podejrzana reguła przekazywania skrzynki odbiorczej po podejrzanym logowaniu na koncie usługi Azure AD. To wskazanie zapewnia wysoki poziom pewności, że konto użytkownika (zanotowane w opisie zdarzenia Fusion) zostało naruszone i zostało użyte do wyprowadzać danych z sieci organizacji przez włączenie reguły przekazywania skrzynek pocztowych bez znajomości prawdziwej wiedzy użytkownika. Permutacje alertów dotyczących logowania do usługi Azure AD za pomocą alertu eksfiltracji skrzynki pocztowej pakietu Office 365 są następujące:

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do eksfiltracji skrzynek pocztowych pakietu Office 365**

- **Zdarzenie logowania z nieznanej lokalizacji wiodącej w skrzynce pocztowej usługi Office 365 eksfiltracji**

- **Zdarzenie logowania z zainfekowanego urządzenia wiodącego w skrzynce pocztowej usługi Office 365 eksfiltracji**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do skrzynki pocztowej pakietu Office 365 eksfiltracji**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do eksfiltracji skrzynek pocztowych pakietu Office 365**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Pobieranie pliku masowego po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, Eksfiltracji

**Techniki MITRE&ATT:** Prawidłowe konto (T1078)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia syntezy tego typu wskazują, że nietypowa liczba plików została pobrana przez użytkownika po podejrzanej rejestracji na koncie usługi Azure AD. To oznaczenie zapewnia wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i zostało użyte do wyprowadzać danych z sieci organizacji. Permutacje alertów dotyczących logowania do usługi Azure AD z alertem pobierania pliku masowego są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do pobrania pliku masowego**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do pobrania pliku masowego**

- **Zdarzenie logowania z zainfekowanego urządzenia, które prowadzi do pobrania pliku masowego**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do pobierania pliku masowego**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do pobierania pliku masowego**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Masowe udostępnianie plików po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, Eksfiltracji

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), Eksfiltracji za pośrednictwem usługi sieci Web (T1567)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że liczba plików powyżej określonego progu została udostępniona innym osobom po podejrzanym logowaniu się do konta usługi Azure AD. To oznaczenie zapewnia wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i użyte do wyprowadzać danych z sieci organizacji przez udostępnienie plików, takich jak dokumenty, arkusze kalkulacyjne itp., nieautoryzowanym użytkownikom do złośliwych celów. Permutacje podejrzanych alertów logowania do usługi Azure AD z alertem dotyczącym udostępniania plików masowych są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do masowego udostępniania plików**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do masowego udostępniania plików**

- **Zdarzenie logowania z zainfekowanego urządzenia, które prowadzi do masowego udostępniania plików**

- **Zdarzenie logowania z anonimowego adresu IP, które prowadzi do masowego udostępniania plików**

- **Zdarzenie logowania od użytkownika z nieujawnionymi poświadczeniami prowadzącymi do masowego udostępniania plików**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Podejrzane reguły manipulowania skrzynką odbiorczą ustawione po podejrzanym zalogowaniu do usługi Azure AD
Ten scenariusz należy do dwóch klasyfikacji zagrożeń na tej liście: **eksfiltracji danych** i **ruchy poprzeczne**. Dla jasności, pojawia się w obu sekcjach.

Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, ruchy boczne, Eksfiltracji

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), wewnętrzne wyłudzanie informacji Spear (T1534)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenie Fusion tego typu wskazuje, że zasady nietypowej skrzynki odbiorczej zostały ustawione w skrzynce odbiorczej użytkownika po podejrzanej rejestracji na koncie usługi Azure AD. Zapewnia to wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i zostało użyte do manipulowania regułami skrzynki odbiorczej poczty e-mail użytkownika dla złośliwych celów. Może to być spowodowane próbą wyprowadzać danych z sieci organizacji przez osobę atakującą. Alternatywnie osoba atakująca może próbować generować wiadomości e-mail dotyczące wyłudzania informacji z organizacji (pomijając mechanizmy wykrywania wyłudzania informacji przeznaczone do wiadomości e-mail ze źródeł zewnętrznych) na potrzeby późniejszego przechodzenia przez uzyskanie dostępu do dodatkowych kont użytkowników i/lub uprzywilejowanych. W przypadku alertów o podejrzanych alertach dotyczących logowania do usługi Azure AD przy użyciu podejrzanych reguł manipulowania skrzynką odbiorczą są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanej reguły manipulowania skrzynką odbiorczą**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Wiele Power BI działań związanych z udostępnianiem raportów po podejrzanym zalogowaniu do usługi Azure AD 
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, Eksfiltracji 

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), Eksfiltracji za pośrednictwem usługi sieci Web (T1567)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że nietypowej liczby raportów Power BI były udostępniane w ramach jednej sesji po podejrzanej rejestracji na koncie usługi Azure AD. To wskazanie zapewnia wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i zostało użyte do wyprowadzać danych z sieci organizacji przez udostępnienie Power BI raporty nieautoryzowanym użytkownikom w celu złośliwego użytku. Permutacje podejrzanych alertów logowania do usługi Azure AD z wieloma Power BImi działaniami do udostępniania raportów są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do wielu Power BI działań związanych z udostępnianiem raportów**

- **Zdarzenie logowania z nieznanej lokalizacji wiodącej w wielu Power BI działaniach związanych z udostępnianiem raportów**

- **Zdarzenie logowania z zainfekowanego urządzenia wiodące w wielu Power BI działaniach związanych z udostępnianiem raportów**

- **Zdarzenie logowania z anonimowego adresu IP, które prowadzi do wielu Power BI działań udostępniania raportów**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do wielu Power BI działań udostępniania raportów**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Podejrzane Power BI udostępnianie raportów po podejrzanym zalogowaniu do usługi Azure AD
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, Eksfiltracji 

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), Eksfiltracji za pośrednictwem usługi sieci Web (T1567)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że w przypadku podejrzanego logowania do konta usługi Azure AD nastąpiło podejrzane działanie udostępniania raportów Power BI. Działanie udostępniania zostało zidentyfikowane jako podejrzane, ponieważ raport Power BI zawierał informacje poufne identyfikowane przy użyciu przetwarzania języka naturalnego, a ponieważ został udostępniony z zewnętrznym adresem e-mail, opublikowanym w sieci Web lub dostarczony jako migawka na zewnętrzny adres e-mail. Ten alert wskazuje na wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i zostało użyte do wyprowadzać poufnych danych z organizacji, udostępniając Power BI raporty nieautoryzowanym użytkownikom w złośliwych celach. Permutacje alertów dotyczących logowania do usługi Azure AD z podejrzanymi Power BI udostępniania raportów są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do podejrzanego Power BI udostępniania raportów**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanego Power BI udostępniania raportów**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanego Power BI udostępniania raportów**

- **Zdarzenie logowania z anonimowego adresu IP prowadzące do podejrzanego Power BI udostępniania raportów**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanego Power BI udostępniania raportów**

## <a name="data-destruction"></a>Niszczenie danych

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Usuwanie pliku masowego po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, wpływ

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), niszczenie danych (T1485)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że nietypowej liczby unikatowych plików zostało usuniętych po podejrzanej zalogowaniu się do konta usługi Azure AD. Zapewnia to wskazanie, że konto zanotowane w opisie zdarzenia Fusion mogło zostać naruszone i było używane do niszczenia danych do złośliwych celów. Permutacje alertów dotyczących logowania do usługi Azure AD za pomocą alertu dotyczącego usuwania plików masowych są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do usunięcia pliku masowego**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do usunięcia pliku masowego**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do usunięcia pliku masowego**

- **Zdarzenie logowania z anonimowego adresu IP prowadzące do usunięcia pliku masowego**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do usunięcia pliku masowego**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Podejrzane działanie dotyczące usuwania wiadomości e-mail po podejrzanym zalogowaniu do usługi Azure AD
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, wpływ 

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), niszczenie danych (T1485)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że nietypowa liczba wiadomości e-mail została usunięta podczas jednej sesji po podejrzanej rejestracji na koncie usługi Azure AD. Zapewnia to wskazanie, że konto zanotowane w opisie zdarzenia Fusion mogło zostać naruszone i było używane do niszczenia danych do złośliwych celów, takich jak naruszenie organizacji lub ukrywanie działania poczty e-mail związanej z spamem. Permutacje podejrzanych alertów logowania do usługi Azure AD za pomocą alertu dotyczącego usuwania podejrzanych wiadomości e-mail są następujące:   

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do podejrzanych działań związanych z usuwaniem poczty e-mail**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanego działania dotyczącego usuwania wiadomości e-mail**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanego działania związanego z usuwaniem poczty e-mail**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanego działania dotyczącego usuwania wiadomości e-mail**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanego działania dotyczącego usuwania wiadomości e-mail**

## <a name="denial-of-service"></a>Odmowa usługi

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Wiele działań usuwania maszyn wirtualnych po podejrzanym zalogowaniu do usługi Azure AD
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, wpływ

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), odmowa usługi Endpoint Service (T1499)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że nietypowa liczba maszyn wirtualnych została usunięta podczas jednej sesji po podejrzanej rejestracji na koncie usługi Azure AD. To oznaczenie zapewnia wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i było używane do próby zakłócenia lub zniszczenia środowiska chmury w organizacji. Permutacje podejrzanych alertów logowania do usługi Azure AD z alertami dotyczącymi usuwania wielu maszyn wirtualnych są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do wielu działań związanych z usuwaniem maszyn wirtualnych**

- **Zdarzenie logowania z nieznanej lokalizacji wiodącej w wielu działaniach usuwania maszyn wirtualnych**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do wielu działań związanych z usuwaniem maszyn wirtualnych**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do wielu działań usuwania maszyn wirtualnych**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do działania usuwania wielu maszyn wirtualnych**

## <a name="lateral-movement"></a>Ruch boczny

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Personifikacja pakietu Office 365 po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, ruchy boczne

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), wewnętrzne wyłudzanie informacji Spear (T1534)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia Fusion tego typu wskazują, że nietypowej liczby akcji personifikacji nastąpiło po podejrzanym logowaniu z konta usługi Azure AD. W niektórych programach dostępne są opcje umożliwiające użytkownikom personifikację innych użytkowników. Na przykład usługi poczty e-mail zezwalają użytkownikom na autoryzację innych użytkowników w celu wysyłania wiadomości e-mail w ich imieniu. Ten alert wskazuje na wyższy poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i było używane do wykonywania działań personifikacji dla złośliwych celów, takich jak wysyłanie wiadomości e-mail dotyczących złośliwego oprogramowania lub ruchu bocznego. Permutacje alertów dotyczących logowania do usługi Azure AD za pomocą alertu personifikacji pakietu Office 365 są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do personifikacji pakietu Office 365**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do personifikacji pakietu Office 365**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do personifikacji pakietu Office 365**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do personifikacji pakietu Office 365**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do personifikacji pakietu Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Podejrzane reguły manipulowania skrzynką odbiorczą ustawione po podejrzanym zalogowaniu do usługi Azure AD
Ten scenariusz należy do dwóch klasyfikacji zagrożeń na tej liście: **przemieszczanie boczne** i **eksfiltracji danych**. Dla jasności, pojawia się w obu sekcjach.

Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, ruchy boczne, Eksfiltracji

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), wewnętrzne Spear phishing (T1534), zautomatyzowane Eksfiltracji (T1020)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenie Fusion tego typu wskazuje, że zasady nietypowej skrzynki odbiorczej zostały ustawione w skrzynce odbiorczej użytkownika po podejrzanej rejestracji na koncie usługi Azure AD. To oznaczenie zapewnia wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i zostało użyte do manipulowania regułami skrzynki odbiorczej poczty e-mail użytkownika w złośliwych celach. Może to być spowodowane próbą wyprowadzać danych z sieci organizacji przez osobę atakującą. Alternatywnie osoba atakująca może próbować generować wiadomości e-mail dotyczące wyłudzania informacji z organizacji (pomijając mechanizmy wykrywania wyłudzania informacji przeznaczone do wiadomości e-mail ze źródeł zewnętrznych) na potrzeby późniejszego przechodzenia przez uzyskanie dostępu do dodatkowych kont użytkowników i/lub uprzywilejowanych. W przypadku alertów o podejrzanych alertach dotyczących logowania do usługi Azure AD przy użyciu podejrzanych reguł manipulowania skrzynką odbiorczą są następujące:

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanej reguły manipulowania skrzynką odbiorczą**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanej reguły manipulowania skrzynką odbiorczą**

## <a name="malicious-administrative-activity"></a>Złośliwe działanie administracyjne

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Podejrzane działanie administracyjne aplikacji w chmurze po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, trwałość, ochrona przed zagrożeniami, ruchy boczne, zbieranie, Eksfiltracji i wpływ

**Techniki MITRE&ATT:** NIE DOTYCZY

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia syntezy tego typu wskazują, że anomalia liczba działań administracyjnych została wykonana w jednej sesji po podejrzanym zalogowaniu do usługi Azure AD z tego samego konta. Zapewnia to wskazanie, że konto zanotowane w opisie zdarzenia Fusion mogło zostać naruszone i użyte do wykonania dowolnej liczby nieautoryzowanych akcji administracyjnych ze złośliwym intencją. Oznacza to również, że zabezpieczenia konta z uprawnieniami administracyjnymi mogły zostać naruszone. Permutacje podejrzanych alertów logowania do usługi Azure AD za pomocą alertu o podejrzanym działaniu z aplikacjami w chmurze są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do podejrzanych działań administracyjnych aplikacji w chmurze**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanych działań administracyjnych aplikacji w chmurze**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanych działań administracyjnych aplikacji w chmurze**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanych działań administracyjnych aplikacji w chmurze**

- **Zdarzenie logowania od użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanych działań administracyjnych aplikacji w chmurze**

## <a name="malicious-execution-with-legitimate-process"></a>Złośliwe wykonanie przy użyciu legalnego procesu

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Program PowerShell wykonał podejrzane połączenie sieciowe, a po nim nietypowy ruch oflagowany przez zaporę Palo Alto Networks.
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Działania

**Techniki MITRE&ATT:** Interpreter poleceń i skryptów (T1059)

**Źródła łącznika danych:** Microsoft Defender for Endpoint (dawniej usługa Microsoft Defender Advanced Threat Protection lub MDATP), Palo Alto Networks 

**Opis:** Zdarzenia Fusion tego typu wskazują, że żądanie połączenia wychodzącego zostało wykonane za pośrednictwem polecenia programu PowerShell, a po wykryciu nietypowej aktywności przychodzącej przez zaporę Palo Alto Networks. Zapewnia to wskazanie, że osoba atakująca prawdopodobnie uzyska dostęp do sieci i próbuje wykonać złośliwe działania. Próby połączenia wykonywane przez program PowerShell, które są zgodne z tym wzorcem, mogą wskazywać na polecenia złośliwego oprogramowania i sterowanie nim, żądania pobrania dodatkowego złośliwego oprogramowania lub osoby atakujące ustanawiające zdalny dostęp interaktywny. Podobnie jak w przypadku ataków "żyjące", to działanie może być uprawnionym użyciem programu PowerShell. Jednak wykonanie polecenia programu PowerShell po podejrzanej aktywności w zaporze przychodzącej zwiększa pewność, że program PowerShell jest używany w złośliwy sposób i powinien zostać przeanalizowany dalej. W Palo Alto dzienników wskaźnik platformy Azure koncentruje się na [dziennikach zagrożeń](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), a ruch jest uznawany za podejrzany, gdy zagrożenia są dozwolone (podejrzane dane, pliki, zalewania, pakiety, skanowania, programy szpiegujące, adresy URL, wirusy, luki, pożarem-wirusy, Wildfires). Odwołuje się również do dziennika Palo Alto Threat odpowiadającego [typowi zagrożeń/zawartości](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) wymienionej w opisie zdarzenia Fusion, aby uzyskać dodatkowe szczegóły alertu.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Podejrzane zdalne wykonanie usługi WMI następuje przez nietypowy ruch oflagowany przez zaporę Palo Alto Networks
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wykonywanie, odnajdywanie

**Techniki MITRE&ATT:** Instrumentacja zarządzania Windows (T1047)

**Źródła łącznika danych:** Microsoft Defender for Endpoint (dawniej MDATP), Palo Alto Networks 

**Opis:** Zdarzenie Fusion tego typu wskazuje, że polecenia interfejsu zarządzania systemu Windows (WMI) zostały zdalnie wykonane w systemie, a po wykryciu podejrzanej aktywności przychodzącej przez zaporę Palo Alto Networks. Zapewnia to wskazanie, że osoba atakująca mogła uzyskać dostęp do sieci i podejmuje próbę późniejszego przeniesienia, eskalacji uprawnień i/lub wykonywania złośliwych ładunków. Podobnie jak w przypadku ataków "żyjące", to działanie może być uprawnionym użyciem usługi WMI. Jednak zdalne wykonywanie poleceń usługi WMI, po których następuje podejrzana aktywność zapory przychodzącej, zwiększa stopień pewności, że usługa WMI jest używana w złośliwy sposób i należy ją dokładniej zbadać. W Palo Alto dzienników wskaźnik platformy Azure koncentruje się na [dziennikach zagrożeń](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), a ruch jest uznawany za podejrzany, gdy zagrożenia są dozwolone (podejrzane dane, pliki, zalewania, pakiety, skanowania, programy szpiegujące, adresy URL, wirusy, luki, pożarem-wirusy, Wildfires). Odwołuje się również do dziennika Palo Alto Threat odpowiadającego [typowi zagrożeń/zawartości](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) wymienionej w opisie zdarzenia Fusion, aby uzyskać dodatkowe szczegóły alertu.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Podejrzany wiersz polecenia programu PowerShell po podejrzanym logowaniu

**Mitre ATT&taktykę:** Wstępny dostęp, wykonywanie

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), interpreter poleceń i skryptów (T1059)

**Źródła łącznika danych:** Azure Active Directory Identity Protection, Microsoft Defender for Endpoint (dawniej MDATP)

**Opis:** Zdarzenie Fusion tego typu wskazuje, że użytkownik wykonał potencjalnie złośliwe polecenia programu PowerShell po podejrzanym logowaniu się do konta usługi Azure AD. Zapewnia to wysoki poziom pewności, że konto zanotowane w opisie alertu zostało naruszone i podjęto dalsze złośliwe działania. Osoby atakujące często wykorzystują program PowerShell do wykonywania złośliwych ładunków w pamięci bez opuszczania artefaktów na dysku, aby uniknąć wykrywania za pomocą mechanizmów zabezpieczeń opartych na dyskach, takich jak skanery antywirusowe. W przypadku alertów podejrzanych o podejrzenie logowania usługi Azure AD za pomocą podejrzanego ostrzeżenia polecenia programu PowerShell są następujące:

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do podejrzanego wiersza polecenia programu PowerShell**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanego wiersza polecenia programu PowerShell**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanego wiersza polecenia programu PowerShell**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanego wiersza polecenia programu PowerShell**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanego wiersza polecenia programu PowerShell**

## <a name="malware-c2-or-download"></a>Złośliwe oprogramowanie C2 lub pobieranie

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Żądanie sieciowe do sieci TOR zachowywanie anonimowości, po którym następuje anomalia ruch oflagowany przez zaporę Palo Alto Networks.
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Polecenie i kontrola

**Techniki MITRE&ATT:** Szyfrowany kanał (T1573), proxy (T1090)

**Źródła łącznika danych:** Microsoft Defender for Endpoint (dawniej MDATP), Palo Alto Networks 

**Opis:** Zdarzenie Fusion tego typu wskazuje, że zostało wysłane żądanie połączenia wychodzącego do usługi TOR zachowywanie anonimowości, a po wykryciu nietypowej aktywności przychodzącej przez zaporę Palo Alto Networks. Zapewnia to wskazanie, że osoba atakująca prawdopodobnie uzyska dostęp do sieci i próbuje ukryć swoje działania i intencje. Połączenia z siecią TOR następujące po tym wzorcu mogą wskazywać na polecenia złośliwego oprogramowania oraz działania kontroli, żądania pobrania dodatkowego złośliwego oprogramowania lub osoby atakującej, która ustanawia zdalny dostęp interaktywny. W Palo Alto dzienników wskaźnik platformy Azure koncentruje się na [dziennikach zagrożeń](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), a ruch jest uznawany za podejrzany, gdy zagrożenia są dozwolone (podejrzane dane, pliki, zalewania, pakiety, skanowania, programy szpiegujące, adresy URL, wirusy, luki, pożarem-wirusy, Wildfires). Odwołuje się również do dziennika Palo Alto Threat odpowiadającego [typowi zagrożeń/zawartości](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) wymienionej w opisie zdarzenia Fusion, aby uzyskać dodatkowe szczegóły alertu.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Połączenie wychodzące z adresem IP z historią prób nieautoryzowanego dostępu, po których następuje nietypowy ruch oflagowany przez zaporę Palo Alto Networks
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Polecenie i kontrola

**Techniki MITRE&ATT:** Nie dotyczy

**Źródła łącznika danych:** Microsoft Defender for Endpoint (dawniej MDATP), Palo Alto Networks 

**Opis:** Zdarzenia Fusion tego typu wskazują, że nastąpiło połączenie wychodzące z adresem IP ze historięm nieautoryzowanych prób dostępu, a w przypadku wykrycia nietypowego działania przez zaporę Palo Alto Networks. Zapewnia to wskazanie, że osoba atakująca może uzyskać dostęp do sieci. Próby połączenia zgodnie z tym wzorcem mogą wskazywać na działanie polecenia i kontroli nad złośliwym oprogramowaniem, żądania pobrania dodatkowego złośliwego oprogramowania lub osoby atakującej, która ustanawia zdalny dostęp interaktywny. W Palo Alto dzienników wskaźnik platformy Azure koncentruje się na [dziennikach zagrożeń](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), a ruch jest uznawany za podejrzany, gdy zagrożenia są dozwolone (podejrzane dane, pliki, zalewania, pakiety, skanowania, programy szpiegujące, adresy URL, wirusy, luki, pożarem-wirusy, Wildfires). Odwołuje się również do dziennika Palo Alto Threat odpowiadającego [typowi zagrożeń/zawartości](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) wymienionej w opisie zdarzenia Fusion, aby uzyskać dodatkowe szczegóły alertu.

## <a name="ransomware"></a>Oprogramowanie wymuszające okup

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Wykonanie oprogramowania wymuszającego okup po podejrzanym zalogowaniu do usługi Azure AD

**Mitre ATT&taktykę:** Wstępny dostęp, wpływ

**Techniki MITRE&ATT:** Prawidłowe konto (T1078), zaszyfrowane dane na potrzeby wpływu (T1486)

**Źródła łącznika danych:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Opis:** Zdarzenia syntezy tego typu wskazują, że nietypowe zachowanie użytkownika wskazujące atak z wykorzystaniem oprogramowania wymuszającego okup zostało wykryte po podejrzanej rejestracji na koncie usługi Azure AD. To wskazanie zapewnia wysoki poziom pewności, że konto zanotowane w opisie zdarzenia Fusion zostało naruszone i zostało użyte do szyfrowania danych na potrzeby extorting właścicielowi danych lub odmowy dostępu do danych przez właściciela danych. Permutacje alertów dotyczących logowania do usługi Azure AD za pomocą alertu uruchamiania oprogramowania wymuszającego okup są następujące:  

- **Niemożliwa podróż do nietypowej lokalizacji prowadzącej do oprogramowania wymuszającego okup w aplikacji w chmurze**

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do oprogramowania wymuszającego okup w aplikacji w chmurze**

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do oprogramowania wymuszającego okup w aplikacji w chmurze**

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do oprogramowania wymuszającego okup w aplikacji w chmurze**

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do oprogramowania wymuszającego okup w aplikacji w chmurze**

## <a name="remote-exploitation"></a>Zdalne wykorzystanie

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Podejrzane użycie struktury ataków następuje przez nietypowy ruch oflagowany przez zaporę Palo Alto Networks
Ten scenariusz jest obecnie w **wersji zapoznawczej**.

**Mitre ATT&taktykę:** Wstępny dostęp, wykonywanie, przenoszenie boczne, eskalacja uprawnień

**Techniki MITRE&ATT:** Wykorzystanie Public-Facing aplikacji (T1190), wykorzystanie do wykonania klienta (T1203), wykorzystanie usług zdalnych (T1210), wykorzystanie do eskalacji uprawnień (T1068)

**Źródła łącznika danych:** Microsoft Defender for Endpoint (dawniej MDATP), Palo Alto Networks 

**Opis:** Zdarzenia syntezy tego typu wskazują, że wykryto niestandardowe zastosowania protokołów, podobne do użycia struktur ataków, takich jak Metasploit, i po tym, że podejrzana aktywność przychodząca została wykryta przez zaporę Palo Alto Networks. Może to być pierwotne wskazanie, że osoba atakująca korzystała z usługi, aby uzyskać dostęp do zasobów sieciowych lub że osoba atakująca już uzyskała dostęp i próbuje dalej wykorzystać dostępne systemy/usługi w celu późniejszego przechodzenia i/lub eskalacji uprawnień. W Palo Alto dzienników wskaźnik platformy Azure koncentruje się na [dziennikach zagrożeń](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), a ruch jest uznawany za podejrzany, gdy zagrożenia są dozwolone (podejrzane dane, pliki, zalewania, pakiety, skanowania, programy szpiegujące, adresy URL, wirusy, luki, pożarem-wirusy, Wildfires). Odwołuje się również do dziennika Palo Alto Threat odpowiadającego [typowi zagrożeń/zawartości](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) wymienionej w opisie zdarzenia Fusion, aby uzyskać dodatkowe szczegóły alertu.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się więcej na temat zaawansowanego wykrywania ataków potokach wieloetapowych. możesz zainteresować się następującymi przewodnikami Szybki Start, aby dowiedzieć się, jak uzyskać wgląd w dane i potencjalne zagrożenia: Rozpoczynanie [pracy z platformą Azure](quickstart-get-visibility.md).

Jeśli wszystko jest gotowe do zbadania zdarzeń utworzonych dla Ciebie, zobacz następujący samouczek: [Zbadaj zdarzenia za pomocą platformy Azure](tutorial-investigate-cases.md).

