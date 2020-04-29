---
title: Konfigurowanie aplikacji w portalu wydawców
description: Instrukcje dotyczące sposobu konfigurowania aplikacji w portalu publikowania w chmurze.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280205"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Konfigurowanie aplikacji w portalu wydawców

Teraz możesz przystąpić do konfigurowania aplikacji w portalu wydawców.

## <a name="login-and-create-a-new-offer"></a>Zaloguj się i Utwórz nową ofertę

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. Na pasku nawigacyjnym po lewej stronie kliknij pozycję "+ Nowa oferta" i wybierz pozycję "Dynamics 365 dla zaangażowania klienta".

![Wybieranie nowej oferty](./media/CRMScreenShot14.png)

1. Widok nowej oferty "Edytor" jest teraz otwarty i wszystko jest gotowe do rozpoczęcia tworzenia.

![Ekran nowej oferty](./media/CRMScreenShot15.png)

1. "Formularze", które muszą zostać wypełnione, są widoczne po lewej stronie w widoku "Edytor". Każdy "formularz" składa się z zestawu pól, które mają zostać wypełnione. Wymagane pola są oznaczone czerwoną gwiazdką (\*).

Istnieją cztery główne formularze do tworzenia oferty Dynamics 365 na potrzeby zaangażowania klienta

* Ustawienia oferty
* Informacje techniczne
* Szczegóły witryny dotyczącej Sklepu
* Kontakty

## <a name="fill-out-the-offer-settings-form"></a>Wypełnij formularz ustawień oferty

Formularz ustawienia oferty jest podstawową formą, aby określić ustawienia oferty. Poniżej opisano różne pola.

### <a name="offer-id"></a>Identyfikator oferty

To jest unikatowy identyfikator oferty w ramach profilu wydawcy. Ten identyfikator będzie widoczny w adresach URL produktów. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się znakiem kreski i może zawierać maksymalnie 50 znaków. To pole jest zablokowane po przeprowadzeniu oferty.

na przykład jeśli wydawcy **"contoso"** tworzą ofertę o identyfikatorze oferty **"Sample-webapp"**, zostanie ona wyświetlona w AppSource jako "https:\//AppSource.Microsoft.com/Marketplace/Apps/contoso.sample-webapp?Tab=Overview"

### <a name="publisher-id"></a>IDENTYFIKATOR wydawcy

Ta lista rozwijana umożliwia wybranie profilu wydawcy, w ramach którego ma zostać opublikowana oferta. To pole jest zablokowane po przeprowadzeniu oferty.

### <a name="name"></a>Nazwa

To jest nazwa wyświetlana oferty. To jest nazwa, która będzie wyświetlana w [AppSource](https://appsource.microsoft.com/). Może zawierać maksymalnie 50 znaków.

Kliknij przycisk "Zapisz", aby zapisać postęp. Następnym krokiem jest dodanie informacji technicznych do oferty.

## <a name="fill-out-the-technical-info-form"></a>Wypełnij formularz informacji technicznych


Formularz informacje techniczne polega na tym, że zapełnisz informacje specyficzne dla rozwiązania do zaangażowania klienta na usługi Dynamics 365. Umieszczenie wskaźnika myszy na stronie zawiera więcej informacji. Zobacz przykład poniżej.

![Ekran informacji technicznych](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informacje o aplikacji

Większość wydawców pozostawi te pola z wartościami domyślnymi, User, no, no i pustym adresem URL konfiguracji aplikacji, tak jak na poniższym zrzucie ekranu.

### <a name="crm-package"></a>Pakiet CRM

![Informacje o pakiecie CRM](./media/CRMScreenShot17.png)

Oto wyjaśnienie tych pól:

* Nazwa pliku pakietu: nazwa pliku utworzona w powyższym kroku podczas tworzenia pliku zip, który jest pakietem CRM AppSource. W powyższym przykładzie jest to plik "\_Microsoft SamplePackage. zip".
* Adres URL lokalizacji pakietu: jest to adres URL konta usługi Azure Storage, które zawiera nazwę pliku pakietu określoną powyżej. Jest to adres URL utworzony w kroku 9 sekcji powyżej.
* Czy w pliku pakietu znajduje się więcej niż jeden pakiet CRM: wybierz opcję tak **tylko** wtedy, gdy obsługujesz wiele wersji programu CRM z różnymi pakietami. W przypadku większości partnerów nie będzie to miało znaczenia. Jeśli wybierzesz opcję tak, musisz utworzyć pakiety AppSource dla każdej wersji rozwiązania. _Uwaga: nie jest to pytanie, czy masz wiele plików **zip** . Jeśli masz wiele plików. zip rozwiązania, ale tylko jedną wersję, nadal nie należy wybierać opcji "nie". Narzędzie pakowanie spowoduje automatyczne przełączenie tych samych._

### <a name="crm-package-availability"></a>Dostępność pakietu CRM

W tej sekcji Wybierz regiony programu CRM, do których zostanie udostępniony pakiet. Aby uzyskać informacje na temat tego, które regiony programu CRM stanowią kraje/regiony, zobacz link:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Uwaga: wdrożenie do Niemiec "suwerenne i US Gov Cloud" suwerenne wymaga specjalnych uprawnień i weryfikacji podczas certyfikacji

## <a name="storefront-details"></a>Szczegóły witryny dotyczącej Sklepu

### <a name="offer-summary"></a>Podsumowanie oferty

To jest podsumowanie propozycji wartości oferty. Zostanie ona wyświetlona na stronie wyszukiwania Twojej oferty. Powinna zawierać maksymalnie 100 znaków.

### <a name="offer-description"></a>Opis oferty

Jest to opis, który będzie wyświetlany na stronie szczegółów aplikacji. Maksymalna dozwolona liczba znaków to 1300

### <a name="industries"></a>Branże

Wybierz branżę, do której aplikacja jest Najlepsza. Jeśli aplikacja odnosi się do wielu branż, możesz pozostawić to pole puste.

### <a name="categories"></a>Kategorie

Wybierz kategorie, które są odpowiednie dla Twojej aplikacji. Wybierz maksymalnie 3.

### <a name="app-type"></a>Typ aplikacji

Wybierz typ wersji próbnej, która będzie włączana przez aplikację w usłudze AppSource. "Bezpłatnie" oznacza, że Twoja aplikacja jest bezpłatna. "Wersja próbna" oznacza, że klienci mogą wypróbować aplikację przez krótki okres w AppSource. "Żądanie dla wersji próbnej" nie jest obsługiwane dla programu Dynamics 365 dla aplikacji do zaangażowania klientów. Nie wybieraj tej opcji.

### <a name="help-link-for-your-app"></a>Link pomocy dla aplikacji

Wprowadź adres URL strony, która zawiera informacje dotyczące pomocy dotyczącej aplikacji.

### <a name="supported-countriesregions"></a>Obsługiwane kraje/regiony

To pole określa kraje/regiony, w których oferta będzie dostępna do wersji próbnej.

### <a name="supported-languages"></a>Obsługiwane języki

Wybierz języki obsługiwane przez aplikację. Jeśli aplikacja obsługuje dodatkowe języki, które nie znajdują się na tej liście, Kontynuuj publikowanie oferty i Wyślij wiadomość e-mail do [appsource@microsoft.com](mailto:appsource@microsoft.com) nas pod adresem:, aby poinformować nas o tym.

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji aplikacji

### <a name="app-release-date"></a>Data wydania aplikacji

Wprowadź datę wydania aplikacji

### <a name="products-your-app-works-with-max-3"></a>Produkty działające z aplikacją (maksymalnie 3)

Specyficzne dla listy produkty, z którymi działa Twoja aplikacja. Można wyświetlić listę maksymalnie trzech produktów. Aby wyświetlić listę produktów, kliknij znak plus (obok pozycji nowy) i nowe pole tekstowe otwarte zostanie utworzone, aby wprowadzić nazwę produktu, z którym działa aplikacja.

### <a name="search-keywords-max-3"></a>Słowa kluczowe wyszukiwania (maks. 3)

AppSource umożliwia klientowi wyszukiwanie na podstawie słów kluczowych. Można wprowadzić zestaw słów kluczowych, dla których aplikacja będzie pokazywana klientom.

Na przykład jeśli aplikacja to wiadomości e-mail "Moje usługi poczty E-mail", adresowanie, usługa poczty może być słowami kluczowymi. Wybierz wyrazy, które użytkownicy będą prawdopodobnie używać do wyszukiwania aplikacji w polu wyszukiwania AppSource.

### <a name="hide-key"></a>Ukryj klucz

Jest to klucz, który zostanie połączony z adresem URL wersji zapoznawczej oferty, aby ukryć go z widoku publicznego. Nie jest to hasło. W tym miejscu możesz wprowadzić dowolny ciąg.

### <a name="offer-logo-png-format-48x48"></a>Logo oferty (format PNG, 48x48)

Zostanie ona wyświetlona na stronie wyszukiwania Twojej aplikacji. **Dozwolony jest tylko format PNG.** Przekaż obraz PNG z rozdzielczością 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logo oferty (format PNG, 216x216)

Zostanie ona wyświetlona na stronie szczegółów aplikacji. **Dozwolony jest tylko format PNG.** Przekaż obraz PNG z rozdzielczością 216PX\*216PX

### <a name="videos"></a>Filmy wideo

Można przekazać maksymalnie cztery wideo. Dla każdego wideo, które chcesz przekazać, musisz podać nazwę wideo, adres URL (tylko w usłudze YouTube lub Vimeo) i miniaturę, aby skojarzyć z filmem wideo. Miniatura musi być w formacie PNG i musi być\*1280PX 720PX. Aby dodać nowe wideo, kliknij znak plus. Miniatury wideo pojawią się na stronie szczegółów aplikacji.

### <a name="documents"></a>Dokumenty

Można przekazać maksymalnie trzy dokumenty w formacie PDF. Dla każdego dokumentu, który ma zostać przekazany, należy podać nazwę dokumentu i przekazać go. Dokument musi być w formacie PDF.

Aby dodać nowe dokumenty, kliknij znak plusa

### <a name="screenshots"></a>Zrzuty ekranu

Są to zrzuty ekranu, które pojawią się na stronie szczegółów AppSource aplikacji.

### <a name="privacy-policy"></a>Zasady ochrony prywatności

Wprowadź adres URL do zasad zachowania poufności informacji aplikacji

### <a name="terms-of-use"></a>Warunki użytkowania

Wprowadź warunki użytkowania aplikacji. Aby móc wypróbować aplikację, klienci AppSource muszą zaakceptować te warunki

### <a name="support-url"></a>Adres URL pomocy technicznej

Wprowadź adres URL pomocy technicznej dla aplikacji.

### <a name="lead-destination"></a>Miejsce docelowe potencjalnego klienta

Wybierz system CRM, w którym ma zostać zapisany potencjalny klient. Wybierz pozycję "Azure Table" tutaj, jeśli masz jeden z następujących systemów CRM: Salesforce, Marketo, Microsoft Dynamics CRM. System CRM wybierany w tym miejscu to miejsce, w którym zostaną zapisane szczegółowe informacje o użytkownikach końcowych, którzy próbują wykonać swoją aplikację w witrynie AppSource (potencjalni klienci) W zależności od wybranego systemu CRM kliknij odpowiedni adres URL poniżej, aby uzyskać informacje na temat sposobu wykonania następnego zestawu pól.

* [Tabela platformy Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [SalesForce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Szczegóły witryny dotyczącej Sklepu

Szczegóły kontaktu są używane do komunikacji wewnętrznej między partnerem i firmą Microsoft. Uwaga: ważne jest, aby użyć adresu e-mail, który jest monitorowany w tych polach. Będziemy używać tej wiadomości e-mail do komunikowania się z twoją pracą w trakcie publikowania w usłudze AppSource. Tylko adres URL pomocy technicznej będzie widoczny dla klientów.
