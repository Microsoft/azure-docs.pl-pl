---
title: Raporty dotyczące działań związanych z logowaniem Azure Active Directory — wersja zapoznawcza | Microsoft Docs
description: Wprowadzenie do raportów dotyczących działań związanych z logowaniem w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 185638d683699403c304603d968cfe84e32a55b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574564"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Raporty dotyczące działań związanych z logowaniem Azure Active Directory — wersja zapoznawcza

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- **Działanie** 
    - **Logowania** — informacje o tym, kiedy użytkownicy, aplikacje i zarządzane zasoby logują się do usługi Azure AD w celu uzyskania dostępu do zasobów.
    - **Dzienniki inspekcji**  -  [Dzienniki inspekcji](concept-audit-logs.md) zapewniają informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzanych aplikacji i działań związanych z katalogiem.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — [ryzykowne logowanie](../identity-protection/overview-identity-protection.md) jest wskaźnikiem próby logowania przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani do ryzyka** — [ryzykowny użytkownik](../identity-protection/overview-identity-protection.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Raport klasycznych logowań w Azure Active Directory zawiera omówienie logowania użytkowników interakcyjnych. Ponadto masz teraz dostęp do trzech dodatkowych raportów logowania, które są teraz dostępne w wersji zapoznawczej:

- Logowania użytkowników nieinterakcyjnych

- Logowania główne usługi

- Zarządzane tożsamości dla logowania do zasobów platformy Azure

Ten artykuł zawiera omówienie raportu aktywności związanej z logowaniem z podglądem nieinteraktywnych, aplikacji i zarządzanych tożsamości dla zasobów platformy Azure. Aby uzyskać informacje na temat raportu logowania bez funkcji w wersji zapoznawczej, zobacz  [Raporty aktywności logowania w portalu Azure Active Directory](concept-sign-ins.md).



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z tej funkcji należy znać odpowiedzi na następujące pytania:

- Kto może uzyskać dostęp do danych?

- Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

### <a name="who-can-access-the-data"></a>Kto może uzyskać dostęp do danych?

- Użytkownicy w rolach administrator zabezpieczeń, czytelnik zabezpieczeń i czytelnik raportów

- Administratorzy globalni

- Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

Dzierżawca musi mieć skojarzoną licencję Azure AD — wersja Premium, aby wyświetlić działania związane z logowaniem. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Wyświetlenie danych w raportach po przeprowadzeniu uaktualnienia do licencji Premium bez działań związanych z danymi przed uaktualnieniem zajmie kilka dni.



## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania zawiera odpowiedzi na następujące pytania:

- Co to jest wzorzec logowania użytkownika, aplikacji lub usługi?
- Ile użytkowników, aplikacji lub usług zostało zalogowanych w ciągu tygodnia?
- Jaki jest stan tych logowań?


W bloku raport logowania można przełączać się między:

- **Logowania użytkowników interakcyjnych** — logowania, w przypadku których użytkownik udostępnia czynnik uwierzytelniania, taki jak hasło, odpowiedź w ramach aplikacji MFA, czynnik biometryczny lub kod QR.

- **Logowania użytkowników nieinteraktywnych** — logowania wykonywane przez klienta w imieniu użytkownika. Te logowania nie wymagają żadnej interakcji z użytkownikiem. Na przykład uwierzytelnianie i autoryzacja przy użyciu tokenów odświeżania i dostępu, które nie wymagają od użytkownika wprowadzania poświadczeń.

- **Logowania główne usługi** — logowania według aplikacji i podmiotów usługi, które nie obejmują żadnego użytkownika. W tych logowaniach aplikacja lub usługa udostępnia poświadczenia w swoim imieniu w celu uwierzytelniania lub uzyskiwania dostępu do zasobów.

- **Zarządzane tożsamości dla logowania do zasobów platformy Azure** — logowania przez zasoby platformy Azure, które mają wpisy tajne zarządzane przez platformę Azure. Aby uzyskać więcej informacji, zobacz [co to są zarządzane tożsamości dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md) 


![Typy raportów logowania](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Logowania użytkowników

Każda karta w bloku logowania pokazuje domyślne kolumny poniżej. Niektóre karty mają dodatkowe kolumny:

- Data logowania

- Identyfikator żądania

- Nazwa użytkownika lub identyfikator użytkownika

- Nazwa lub identyfikator aplikacji

- Stan logowania

- Adres IP urządzenia używanego do logowania



### <a name=&quot;interactive-user-sign-ins&quot;></a>Logowania użytkowników interakcyjnych


Logowania użytkowników interakcyjnych to logowania, w przypadku których użytkownik udostępnia rolę uwierzytelniania do usługi Azure AD lub współdziała bezpośrednio z usługą Azure AD lub aplikacją pomocnika, taką jak aplikacja Microsoft Authenticator. Czynniki, które użytkownicy zapewniają, obejmują hasła, odpowiedzi na wyzwania usługi MFA, czynniki biometryczne lub kody QR, które użytkownik udostępni w usłudze Azure AD lub do aplikacji pomocnika.

> [!NOTE]
> Ten raport zawiera również federacyjne logowania z dostawców tożsamości federacyjnych do usługi Azure AD.  



Uwaga: Raport logowania użytkowników interaktywnych używany do przechowywania niektórych nieinteraktywnych logowania z klientów programu Microsoft Exchange. Mimo że te logowania nie były interaktywne, zostały uwzględnione w raporcie logowania użytkowników interakcyjnych w celu zapewnienia dodatkowej widoczności. Gdy raport logowania użytkowników nieinteraktywnych wprowadzi publiczną wersję zapoznawczą w listopadzie 2020, te dzienniki zdarzeń nieinterakcyjnego logowania zostały przeniesione do raportu nieinterakcyjnego logowania użytkownika w celu zwiększenia dokładności. 


**Rozmiar raportu:** mały <br> 
**Pokazują**

- Użytkownik podaje nazwę użytkownika i hasło na ekranie logowania usługi Azure AD.

- Użytkownik przekazuje wyzwanie SMS usługi MFA.

- Użytkownik udostępnia gest biometryczny do odblokowania komputera z systemem Windows za pomocą usługi Windows Hello dla firm.

- Użytkownik jest federacyjny do usługi Azure AD z potwierdzeniem SAML AD FS.


Oprócz domyślnych pól raport interakcyjnego logowania zawiera również następujące elementy: 

- Lokalizacja logowania

- Czy zastosowano dostęp warunkowy



Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Interaktywne kolumny logowania użytkowników](./media/concept-all-sign-ins/columns-interactive.png &quot;Interaktywne kolumny logowania użytkowników")





Dostosowanie widoku pozwala wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Wszystkie kolumny interaktywne](./media/concept-all-sign-ins/all-interactive-columns.png)


Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje na temat powiązanego logowania.

![Aktywność logowania](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Logowania użytkowników interakcyjnych")



### <a name="non-interactive-user-sign-ins"></a>Logowania użytkowników nieinterakcyjnych

Logowania użytkowników nieinterakcyjnych to logowania wykonywane przez aplikację kliencką lub składniki systemu operacyjnego w imieniu użytkownika. Takie logowania są wykonywane w imieniu użytkownika interakcyjnego. W przeciwieństwie do logowania użytkowników interakcyjnych te logowania nie wymagają od użytkownika podania współczynnika uwierzytelniania. Zamiast tego aplikacja urządzenia lub klienta używa tokenu lub kodu do uwierzytelniania lub uzyskiwania dostępu do zasobu w imieniu użytkownika. Ogólnie rzecz biorąc, użytkownik będzie postrzegał te logowania w tle działania użytkownika.


**Rozmiar raportu:** Znacznie <br>
**Przykłady:** 

- Aplikacja kliencka używa tokenu odświeżania protokołu OAuth 2,0 w celu uzyskania tokenu dostępu.

- Klient używa kodu autoryzacji OAuth 2,0 w celu uzyskania tokenu dostępu i tokenu odświeżania.

- Użytkownik wykonuje Logowanie jednokrotne do aplikacji sieci Web lub systemu Windows na komputerze przyłączonym do usługi Azure AD.

- Użytkownik loguje się do drugiej aplikacji Microsoft Office, gdy ma sesję na urządzeniu przenośnym przy użyciu usługi FOCI (rodzina identyfikatorów klienta).




Oprócz pól domyślnych raport nieinteraktywnych logowań zawiera również następujące elementy: 

- Identyfikator zasobu

- Liczba zgrupowanych logowań




Nie można dostosować pól wyświetlanych w tym raporcie.


![Kolumny wyłączone](./media/concept-all-sign-ins/disabled-columns.png "Kolumny wyłączone")

W celu ułatwienia tworzenia podsumowania danych są grupowane zdarzenia nieinterakcyjnego logowania. Klienci często tworzą wiele nieinteraktywnych logowań w imieniu tego samego użytkownika w krótkim czasie, który udostępnia wszystkie te same cechy, z wyjątkiem czasu, w którym podjęto próbę logowania. Na przykład klient może uzyskać token dostępu raz na godzinę w imieniu użytkownika. Jeśli użytkownik lub klient nie zmieni stanu, adres IP, zasób i wszystkie inne informacje są takie same dla każdego żądania tokenu dostępu. Gdy usługa Azure AD rejestruje wielokrotne logowania, które są identyczne od czasu i daty, te operacje logowania będą agregowane w jednym wierszu. Wiersz z wieloma identycznymi logowaniami (z wyjątkiem daty i czasu wystawienia) będzie mieć wartość większą niż 1 w kolumnie # signers. Można rozwinąć wiersz, aby zobaczyć wszystkie różne logowania i różne sygnatury czasowe. Logowania są agregowane w nieinterakcyjnych użytkownikach, gdy są zgodne następujące dane:


- Aplikacja

- Użytkownik

- Adres IP

- Stan

- Identyfikator zasobu


Oto co możesz zrobić:

- Rozwiń węzeł, aby wyświetlić poszczególne elementy grupy.  

- Kliknij pojedynczy element, aby wyświetlić wszystkie szczegóły 


![Szczegóły logowania użytkowników nieinterakcyjnych](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Logowania główne usługi

W przeciwieństwie do logowania interakcyjnego i nieinterakcyjnego logowania do podmiotów głównych usługi nie obejmują użytkownika. Zamiast tego są one logowaniem przez dowolne konto niebędące użytkownikami, takie jak aplikacje lub jednostki usługi (z wyjątkiem logowania tożsamości zarządzanej), które znajdują się tylko w raportach dotyczących logowania tożsamości zarządzanej. W tych logowaniach aplikacja lub usługa udostępnia własne poświadczenia, takie jak certyfikat lub klucz tajny aplikacji w celu uwierzytelniania lub dostępu do zasobów.


**Rozmiar raportu:** Znacznie <br>
**Przykłady:**

- Nazwa główna usługi używa certyfikatu do uwierzytelniania i uzyskiwania dostępu do Microsoft Graph. 

- Aplikacja używa klucza tajnego klienta do uwierzytelniania w przepływie poświadczeń klienta OAuth. 


Ten raport zawiera domyślny widok listy, który pokazuje:

- Data logowania

- Identyfikator żądania

- Nazwa główna usługi lub identyfikator

- Stan

- Adres IP

- Nazwa zasobu

- Identyfikator zasobu

- Liczba logowań

Nie można dostosować pól wyświetlanych w tym raporcie.

![Kolumny wyłączone](./media/concept-all-sign-ins/disabled-columns.png "Kolumny wyłączone")

W celu ułatwienia tworzenia podsumowania danych w dziennikach logowania nazwy głównej usługi są grupowane zdarzenia głównej nazwy usługi. Logowania z tej samej jednostki w tych samych warunkach są agregowane w jednym wierszu. Można rozwinąć wiersz, aby zobaczyć wszystkie różne logowania i różne sygnatury czasowe. Logowania są agregowane w raporcie głównym usługi, gdy następujące dane pasują do:

- Nazwa główna usługi lub identyfikator

- Stan

- Adres IP

- Nazwa lub identyfikator zasobu

Oto co możesz zrobić:

- Rozwiń węzeł, aby wyświetlić poszczególne elementy grupy.  

- Kliknij pojedynczy element, aby wyświetlić wszystkie szczegóły 


![Szczegóły kolumny](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Szczegóły kolumny")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Zarządzana tożsamość dla logowania do zasobów platformy Azure 

Zarządzana tożsamość dla logowania do zasobów platformy Azure to logowania wykonywane przez zasoby, których klucze tajne są zarządzane przez platformę Azure, aby uprościć zarządzanie poświadczeniami.

**Rozmiar raportu:** Małych <br> 
**Pokazują**

Maszyna wirtualna z poświadczeniami zarządzanymi używa usługi Azure AD w celu uzyskania tokenu dostępu.   


Ten raport zawiera domyślny widok listy, który pokazuje:


- Identyfikator tożsamości zarządzanej

- Nazwa tożsamości zarządzanej

- Zasób

- Identyfikator zasobu

- Liczba zgrupowanych logowań

Nie można dostosować pól wyświetlanych w tym raporcie.

W celu ułatwienia tworzenia podsumowania danych, zarządzane tożsamości dla zasobów platformy Azure logowanie do dzienników, zdarzenia logowania nieinterakcyjnego są pogrupowane. Logowania z tej samej jednostki są agregowane w jednym wierszu. Można rozwinąć wiersz, aby zobaczyć wszystkie różne logowania i różne sygnatury czasowe. Logowania są agregowane w raporcie tożsamości zarządzanych, gdy wszystkie poniższe dane pasują do:

- Nazwa lub identyfikator tożsamości zarządzanej

- Stan

- Adres IP

- Nazwa lub identyfikator zasobu

Wybierz element w widoku listy, aby wyświetlić wszystkie logowania, które są zgrupowane w węźle.

Wybierz zgrupowany element, aby wyświetlić wszystkie szczegóły logowania. 


## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Ustawiając filtr, można zawęzić zakres zwracanych danych logowania. Usługa Azure AD udostępnia szeroką gamę dodatkowych filtrów, które można ustawić. Podczas ustawiania filtru zawsze należy zwrócić szczególną uwagę na skonfigurowany filtr zakresu **dat** . Odpowiedni filtr zakresu dat zapewnia, że usługa Azure AD zwraca tylko te dane, które naprawdę zainteresują.     

Filtr zakresu **dat** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.
Możliwe wartości:

- Jeden miesiąc

- Siedem dni

- Dwadzieścia cztery godziny

- Niestandardowy

![Filtr zakresu dat](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtruj logowania użytkowników

Filtr dla logowania interaktywnego i nieinterakcyjnego jest taki sam. W związku z tym filtr skonfigurowany do logowania interakcyjnego jest zachowywany dla nieinteraktywnych logowania i na odwrót. 






## <a name="access-the-new-sign-in-activity-reports"></a>Dostęp do nowych raportów działań związanych z logowaniem 

Raport aktywność logowania w Azure Portal zapewnia prostą metodę przełączania i wyłączania raportu z wersji zapoznawczej. Jeśli masz włączone raporty z wersji zapoznawczej, uzyskasz nowe menu, które zapewnia dostęp do wszystkich typów raportów działań związanych z logowaniem.     


Aby uzyskać dostęp do nowych raportów logowania za pomocą logowania nieinterakcyjnego i aplikacji: 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**.

    ![Wybieranie usługi Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. W sekcji **monitorowanie** kliknij pozycję **logowania**.

    ![Wybieranie opcji logowania](./media/concept-all-sign-ins/sign-ins.png)

3. Kliknij pasek **podglądu** .

    ![Włącz nowy widok](./media/concept-all-sign-ins/enable-new-preview.png)

4. Aby przełączyć się z powrotem do widoku domyślnego, kliknij pasek **podglądu** ponownie. 

    ![Przywróć Widok klasyczny](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Pobierz raporty dotyczące działań związanych z logowaniem

Po pobraniu raportu działania związanego z logowaniem spełnione są następujące kwestie:

- Raport logowania można pobrać jako plik CSV lub JSON.

- Możesz pobrać do 100-K rekordów. Jeśli chcesz pobrać więcej danych, użyj interfejsu API raportowania.

- Pobieranie zależy od dokonanego wyboru filtru.

- Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów Azure Active Directory](reference-reports-data-retention.md). 


![Pobieraj raporty](./media/concept-all-sign-ins/download-reports.png "Pobieraj raporty")


Każdy pobrany plik CSV składa się z sześciu różnych plików:

- Logowanie interakcyjne

- Szczegóły uwierzytelniania interakcyjnych logowań

- Logowania nieinterakcyjne

- Szczegóły uwierzytelniania w przypadku logowania nieinterakcyjnego

- Logowania główne usługi

- Zarządzana tożsamość dla logowania do zasobów platformy Azure

Każdy pobrany plik JSON składa się z czterech różnych plików:

- Logowanie interakcyjne (w tym szczegóły uwierzytelniania)

- Logowania nieinterakcyjne (w tym szczegóły uwierzytelniania)

- Logowania główne usługi

- Zarządzana tożsamość dla logowania do zasobów platformy Azure

![Pobieranie plików](./media/concept-all-sign-ins/download-files.png "Pobieranie plików")




## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportów działań związanych z logowaniem](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)