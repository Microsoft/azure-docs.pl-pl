---
title: Azure Active Directory aktywności logowania — wersja zapoznawcza | Microsoft Docs
description: Wprowadzenie do raportów aktywności logowania w portalu Azure Active Directory portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc9aa77b3fdc3cda94670545f847bb9de31e1160
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718957"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory aktywności logowania — wersja zapoznawcza

Jako administrator IT chcesz wiedzieć, jak działa Twoje środowisko IT. Informacje o kondycji systemu umożliwiają ocenę, czy i jak należy reagować na potencjalne problemy. 

Aby obsługiwać ten cel, portal Azure Active Directory zapewnia dostęp do trzech dzienników aktywności:

- **Logowania — informacje** na temat logowania i sposobu, w jaki zasoby są używane przez użytkowników.
- **[Inspekcja](concept-audit-logs.md)** — informacje o zmianach zastosowanych do dzierżawy, takich jak zarządzanie użytkownikami i grupą lub aktualizacje zastosowane do zasobów dzierżawy.
- **[Provisioning](concept-provisioning-logs.md)** — działania wykonywane przez usługę aprowizowania, takie jak tworzenie grupy w usłudze ServiceNow lub użytkownik zaimportowany z usługi Workday.


Klasyczny raport logowania w aplikacji Azure Active Directory zawiera omówienie interakcyjnych logować użytkowników. Ponadto masz teraz dostęp do trzech dodatkowych raportów logowania, które są teraz dostępne w wersji zapoznawczej:

- Logowania użytkowników nieinterakcyjnych

- Logowania jednostki usługi

- Tożsamości zarządzane do logowania się do zasobów platformy Azure

Ten artykuł zawiera omówienie raportu aktywności logowania z podglądem nieinterakcyjnych tożsamości aplikacji i tożsamości zarządzanych dla logowania zasobów platformy Azure. Aby uzyskać informacje o raporcie logowania bez funkcji w wersji zapoznawczej, zobacz Raporty aktywności logowania w [portalu Azure Active Directory portal.](concept-sign-ins.md)



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z tej funkcji należy znać odpowiedzi na:

- Kto może uzyskać dostęp do danych?

- Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

### <a name="who-can-access-the-data"></a>Kto może uzyskać dostęp do danych?

- Użytkownicy z rolami Administrator zabezpieczeń, Czytelnik zabezpieczeń i Czytelnik raportów

- Administratorzy globalni

- Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?

Dzierżawa musi mieć skojarzoną Azure AD — wersja Premium, aby wyświetlić działania związane z logowaniem. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Po uaktualnieniu do licencji Premium dane będą wyświetlane w raportach dopiero po kilku dniach bez działań dotyczących danych.



## <a name="sign-ins-report"></a>Raport dotyczący logowań

Raport logowania zawiera odpowiedzi na następujące pytania:

- Jaki jest wzorzec logowania użytkownika, aplikacji lub usługi?
- Ilu użytkowników, aplikacji lub usług zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?


W bloku raportu logowania można przełączać się między:

- **Logowanie interakcyjne** użytkownika — logowania, w których użytkownik udostępnia współczynnik uwierzytelniania, taki jak hasło, odpowiedź za pośrednictwem aplikacji MFA, współczynnik biometryczny lub kod QR.

- **Logowania użytkowników nieinterakcyjnych** — logowania wykonywane przez klienta w imieniu użytkownika. Te logowania nie wymagają interakcji ani żadnego czynnika uwierzytelniania ze strony użytkownika. Na przykład uwierzytelnianie i autoryzacja przy użyciu tokenów odświeżania i dostępu, które nie wymagają od użytkownika wprowadzania poświadczeń.

- **Logowania jednostki usługi** — logowania według aplikacji i jednostki usługi, które nie obejmują żadnego użytkownika. W tych logowaniach aplikacja lub usługa udostępnia poświadczenia we własnym imieniu, aby uwierzytelnić zasoby lub uzyskać do nich dostęp.

- **Tożsamości zarządzane dla logowania zasobów platformy Azure** — logowania według zasobów platformy Azure, które mają wpisy tajne zarządzane przez platformę Azure. Aby uzyskać więcej informacji, [zobacz Czym są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md) 


![Typy raportów logowania](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Logowania użytkowników

Każda karta w bloku logowania zawiera kolumny domyślne poniżej. Niektóre karty mają dodatkowe kolumny:

- Data logowania

- Identyfikator żądania

- Nazwa użytkownika lub identyfikator użytkownika

- Nazwa aplikacji lub identyfikator aplikacji

- Stan logowania

- Adres IP urządzenia używanego do logowania



### <a name=&quot;interactive-user-sign-ins&quot;></a>Interakcyjne logowania użytkowników


Interakcyjne logowania użytkowników to logowania, w których użytkownik zapewnia współczynnik uwierzytelniania w usłudze Azure AD lub wchodzi w bezpośrednią interakcję z usługą Azure AD lub aplikacją pomocnika, taką jak Microsoft Authenticator aplikacji. Czynniki, które użytkownicy podają, obejmują hasła, odpowiedzi na wyzwania uwierzytelniania wieloskładnikowego, czynniki biometryczne lub kody QR, które użytkownik udostępnia usłudze Azure AD lub aplikacji pomocnika.

> [!NOTE]
> Ten raport zawiera również federowane logowania od dostawców tożsamości, którzy są federowani z usługą Azure AD.  



> [!NOTE] 
> Interakcyjny raport logowania użytkownika zawierał niektóre logowania nieinterakcyjne z klientów programu Microsoft Exchange. Chociaż te logowania były nieinterakcyjne, zostały uwzględnione w raporcie logowania użytkownika interakcyjnego w celu dodatkowego wglądu. Gdy raport logowania użytkowników nieinterakcyjnych zostanie wprowadzony w publicznej wersji zapoznawczej w listopadzie 2020 r., dzienniki zdarzeń logowania nieinterakcyjnego zostały przeniesione do raportu logowania użytkownika nieinterakcyjnego w celu zwiększenia dokładności. 


**Rozmiar raportu:** mały <br> 
**Przykłady:**

- Użytkownik podaje nazwę użytkownika i hasło na ekranie logowania usługi Azure AD.

- Użytkownik przechodzi wyzwanie uwierzytelniania WIELOSKŁADNIKowego SMS.

- Użytkownik udostępnia gest biometryczny do odblokowywania komputera z systemem Windows za pomocą Windows Hello dla firm.

- Użytkownik jest federowany z usługą Azure AD przy użyciu AD FS SAML.


Oprócz pól domyślnych raport logowania interakcyjnego zawiera również: 

- Lokalizacja logowania

- Czy zastosowano dostęp warunkowy



Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Kolumny interakcyjnego logowania użytkownika](./media/concept-all-sign-ins/columns-interactive.png &quot;Kolumny interakcyjnego logowania użytkownika")





Dostosowanie widoku umożliwia wyświetlenie dodatkowych pól lub usunięcie pól, które są już wyświetlane.

![Wszystkie kolumny interaktywne](./media/concept-all-sign-ins/all-interactive-columns.png)


Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje na temat powiązanego logowania.

![Działania logowania](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interakcyjne logowania użytkowników")



### <a name="non-interactive-user-sign-ins"></a>Logowania użytkowników nieinterakcyjnych

Logowania użytkowników nieinterakcyjnych to logowania, które zostały wykonane przez aplikację kliencyjną lub składniki systemu operacyjnego w imieniu użytkownika. Podobnie jak logowania użytkowników interakcyjnych, te logowania są wykonywane w imieniu użytkownika. W przeciwieństwie do logowania interakcyjnego użytkownika te logowania nie wymagają od użytkownika podania współczynnika uwierzytelniania. Zamiast tego urządzenie lub aplikacja kliency używa tokenu lub kodu do uwierzytelniania lub uzyskiwania dostępu do zasobu w imieniu użytkownika. Ogólnie rzecz biorąc, użytkownik będzie postrzegać te logowania jako takie, które mają miejsce w tle działania użytkownika.


**Rozmiar raportu:** Dużych <br>
**Przykłady:** 

- Aplikacja kliency używa tokenu odświeżania protokołu OAuth 2.0 w celu uzyskania tokenu dostępu.

- Klient używa kodu autoryzacji OAuth 2.0 w celu uzyskania tokenu dostępu i tokenu odświeżania.

- Użytkownik wykonuje logowanie jednokrotne do aplikacji internetowej lub aplikacji systemu Windows na komputerze przyłączonym do usługi Azure AD.

- Użytkownik korzysta z drugiej aplikacji Microsoft Office podczas sesji na urządzeniu przenośnym przy użyciu interfejsu FOCI (Family of Client ID).




Oprócz pól domyślnych raport logowania nieinterakcyjnego zawiera również: 

- Identyfikator zasobu

- Liczba pogrupowanych logie




Nie można dostosować pól wyświetlanych w tym raporcie.


![Wyłączone kolumny](./media/concept-all-sign-ins/disabled-columns.png "Wyłączone kolumny")

Aby ułatwić podsumowanie danych, zdarzenia logowania nieinterakcyjnego są grupowane. Klienci często tworzą wiele nieinterakcyjnych logów w imieniu tego samego użytkownika w krótkim czasie, które mają te same właściwości z wyjątkiem czasu próby logowania. Na przykład klient może uzyskać token dostępu raz na godzinę w imieniu użytkownika. Jeśli użytkownik lub klient nie zmieni stanu, adres IP, zasób i wszystkie inne informacje są takie same dla każdego żądania tokenu dostępu. Gdy usługa Azure AD rejestruje wiele logów, które są identyczne poza godziną i datą, te logowania będą pochodzić z tej samej jednostki, są agregowane w jeden wiersz. Wiersz z wieloma identycznymi logowaniami (z wyjątkiem daty i godziny wydania) będzie miał wartość większą niż 1 w kolumnie # logowania. Możesz rozwinąć wiersz, aby wyświetlić wszystkie różne logowania i ich różne sygnatury czasowe. Logowania są agregowane w użytkownikach nieinterakcyjnych, gdy są takie jak następujące dane:


- Aplikacja

- Użytkownik

- Adres IP

- Stan

- Identyfikator zasobu


Oto co możesz zrobić:

- Rozwiń węzeł, aby wyświetlić poszczególne elementy grupy.  

- Kliknij pojedynczy element, aby wyświetlić wszystkie szczegóły 


![Szczegóły logowania użytkownika nieinterakcyjnego](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Logowania jednostki usługi

W przeciwieństwie do logowania użytkownika interakcyjnego i nieinterakcyjnego, logowania jednostki usługi nie obejmują użytkownika. Zamiast tego są to logowania za pomocą dowolnego konta użytkownika innego niż konto użytkownika, takiego jak aplikacje lub jednostki usługi (z wyjątkiem logowania tożsamości zarządzanej, które są uwzględnione tylko w raporcie logowania tożsamości zarządzanej). W tych logowaniach aplikacja lub usługa udostępnia własne poświadczenia, takie jak certyfikat lub klucz tajny aplikacji w celu uwierzytelnienia lub uzyskania dostępu do zasobów.


**Rozmiar raportu:** Dużych <br>
**Przykłady:**

- Jednostki usługi używa certyfikatu do uwierzytelniania i uzyskiwania dostępu do Microsoft Graph. 

- Aplikacja używa tajnego klienta do uwierzytelniania w przepływie poświadczeń klienta OAuth. 


Ten raport ma domyślny widok listy, który pokazuje:

- Data logowania

- Identyfikator żądania

- Główna nazwa lub identyfikator usługi

- Stan

- Adres IP

- Nazwa zasobu

- Identyfikator zasobu

- Liczba logów

Nie można dostosować pól wyświetlanych w tym raporcie.

![Wyłączone kolumny](./media/concept-all-sign-ins/disabled-columns.png "Wyłączone kolumny")

Aby ułatwić podsumowanie danych w dziennikach logowania jednostki usługi, zdarzenia logowania jednostki usługi są grupowane. Logowania z tej samej jednostki w tych samych warunkach są agregowane w jednym wierszu. Możesz rozwinąć wiersz, aby wyświetlić wszystkie różne logowania i ich różne sygnatury czasowe. Logowania są agregowane w raporcie jednostki usługi w przypadku dopasowania następujących danych:

- Główna nazwa lub identyfikator usługi

- Stan

- Adres IP

- Nazwa lub identyfikator zasobu

Oto co możesz zrobić:

- Rozwiń węzeł, aby wyświetlić poszczególne elementy grupy.  

- Kliknij pojedynczy element, aby wyświetlić wszystkie szczegóły 


![Szczegóły kolumny](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Szczegóły kolumny")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Tożsamość zarządzana dla logowania zasobów platformy Azure 

Tożsamość zarządzana dla logowania zasobów platformy Azure to logowania, które zostały wykonane przez zasoby, które mają wpisy tajne zarządzane przez platformę Azure w celu uproszczenia zarządzania poświadczeniami.

**Rozmiar raportu:** Małe <br> 
**Przykłady:**

Maszyna wirtualna z poświadczeniami zarządzanymi używa usługi Azure AD do uzyskania tokenu dostępu.   


Ten raport ma domyślny widok listy, który pokazuje:


- Identyfikator tożsamości zarządzanej

- Nazwa tożsamości zarządzanej

- Zasób

- Identyfikator zasobu

- Liczba pogrupowanych logie

Nie można dostosować pól wyświetlanych w tym raporcie.

Aby ułatwić podsumowanie danych, w dziennikach logowania tożsamości zarządzanych dla zasobów platformy Azure są grupowane zdarzenia logowania nieinterakcyjnego. Logowania z tej samej jednostki są agregowane w jeden wiersz. Możesz rozwinąć wiersz, aby wyświetlić wszystkie różne logowania i ich różne sygnatury czasowe. Logowania są agregowane w raporcie tożsamości zarządzanych, gdy wszystkie następujące dane są takie jak:

- Nazwa lub identyfikator tożsamości zarządzanej

- Stan

- Adres IP

- Nazwa lub identyfikator zasobu

Wybierz element w widoku listy, aby wyświetlić wszystkie logowania pogrupowane w węźle.

Wybierz pogrupowany element, aby wyświetlić wszystkie szczegóły logowania. 


## <a name="sign-in-error-code"></a>Kod błędu logowania

Jeśli logowanie nie powiodło się, możesz uzyskać więcej informacji na temat przyczyny w sekcji Podstawowe **informacje** powiązanego elementu dziennika. 

![Zrzut ekranu przedstawia widok szczegółowych informacji.](./media/concept-all-sign-ins/error-code.png)
 
Chociaż element dziennika zawiera przyczynę niepowodzenia, istnieją przypadki, w których można uzyskać więcej informacji za pomocą narzędzia wyszukiwania błędów [logowania](https://login.microsoftonline.com/error). Na przykład to narzędzie zawiera kroki korygowania, jeśli jest dostępne.  

![Narzędzie wyszukiwania kodu błędu](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Ustawiając filtr, można zawęzić zakres zwracanych danych logowania. Usługa Azure AD udostępnia szeroką gamę dodatkowych filtrów, które można ustawić. Podczas ustawiania filtru należy zawsze zwrócić szczególną uwagę na skonfigurowany **filtr** Zakres dat. Odpowiedni filtr zakresu dat gwarantuje, że usługa Azure AD zwraca tylko te dane, które naprawdę Cię umkną.     

Filtr **Zakres** dat umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.
Możliwe wartości:

- Jeden miesiąc

- Siedem dni

- 24 godziny

- Niestandardowy

![Filtr zakresu dat](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Filtrowanie logów użytkowników

Filtr logowania interakcyjnego i nieinterakcyjnego jest taki sam. W związku z tym filtr skonfigurowany dla logowania interakcyjnego jest utrwalany dla logowania nieinterakcyjnego i na odwrót. 






## <a name="access-the-new-sign-in-activity-reports"></a>Uzyskiwanie dostępu do nowych raportów aktywności logowania 

Raport działań logowania w oknie Azure Portal udostępnia prostą metodę przełączania i wyłączanie raportu podglądu. Jeśli masz włączone raporty w wersji zapoznawczej, otrzymasz nowe menu, które zapewnia dostęp do wszystkich typów raportów działań logowania.     


Aby uzyskać dostęp do nowych raportów logowania za pomocą logowania nieinterakcyjnego i logowania aplikacji: 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory**.

    ![Wybieranie usługi Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. W sekcji **Monitorowanie** kliknij **pozycję Logowania.**

    ![Wybieranie opcji logowania](./media/concept-all-sign-ins/sign-ins.png)

3. Kliknij pasek **Podgląd.**

    ![Włączanie nowego widoku](./media/concept-all-sign-ins/enable-new-preview.png)

4. Aby wrócić do widoku domyślnego, kliknij ponownie pasek **Podgląd.** 

    ![Przywracanie widoku klasycznego](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Pobieranie raportów dotyczących działań logowania

Po pobraniu raportu aktywności logowania spełnione są następujące kwestie:

- Raport logowania można pobrać w formacie CSV lub JSON.

- Można pobrać maksymalnie 100 000 rekordów. Jeśli chcesz pobrać więcej danych, użyj interfejsu API raportowania.

- Pobieranie zależy od wybranego filtru.

- Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania Azure Active Directory raportu.](reference-reports-data-retention.md) 


![Pobieraj raporty](./media/concept-all-sign-ins/download-reports.png "Pobieraj raporty")


Każdy plik CSV do pobrania składa się z sześciu różnych plików:

- Logowania interakcyjne

- Szczegóły uwierzytelniania logowania interakcyjnego

- Logowania nieinterakcyjne

- Szczegóły uwierzytelniania logowania nieinterakcyjnego

- Logowania jednostki usługi

- Tożsamość zarządzana dla logowania zasobów platformy Azure

Każdy plik JSON do pobrania składa się z czterech różnych plików:

- Logowania interakcyjne (ze szczegółami uwierzytelniania)

- Logowania nieinterakcyjne (ze szczegółami uwierzytelniania)

- Logowania jednostki usługi

- Tożsamość zarządzana dla logowania zasobów platformy Azure

![Pobieranie plików](./media/concept-all-sign-ins/download-files.png "Pobieranie plików")




## <a name="next-steps"></a>Następne kroki

* [Kody błędów raportu aktywności logowania](reference-sign-ins-error-codes.md)
* [Zasady przechowywania danych usługi Azure AD](reference-reports-data-retention.md)
* [Opóźnienia raportów usługi Azure AD](reference-reports-latencies.md)
