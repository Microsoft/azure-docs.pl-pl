---
title: Zarządzanie potencjalnymi klientami w portalu komercyjnym | Azure Marketplace i AppSource
description: Omówienie różnych tematów związanych z publikowaniem ofert i artefaktami technicznymi w witrynie Azure Marketplace i AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/14/2020
ms.openlocfilehash: e3924e609091f4d0975901024f20db74efb6aac5
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032711"
---
# <a name="lead-management-for-the-commercial-marketplace"></a>Zarządzanie potencjalnymi klientami w portalu komercyjnym

Klienci są centrum wszelkich dobrych firm. W przypadku przekształcania nabytych produktów dzisiejszych, rynki muszą skupić się bezpośrednio na łączeniu się z klientami i utworzyć relację. Dlatego generowanie wysokiej jakości potencjalnych klientów jest istotnym narzędziem dla cyklu sprzedaży. Po wystawieniu oferty w [centrum partnerskim](https://partner.microsoft.com/)dostępne są narzędzia umożliwiające programowe otrzymywanie informacji kontaktowych klienta bezpośrednio po wprowadzeniu zainteresowania przez klienta lub wdrożeniu produktu w portalu Marketplace. 

## <a name="what-are-leads-in-the-commercial-marketplace"></a>Co to są potencjalni klienci w komercyjnej witrynie Marketplace?

Potencjalni klienci są pozainteresowani lub wdrażają produkty z komercyjnej witryny Marketplace. Po nawiązaniu połączenia z listą w centrum partnerskim będziesz mieć możliwość otrzymywania potencjalnych klientów z usług Azure Marketplace i AppSource.

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Jak połączyć system CRM z centrum partnerskim

Łącznik zarządzania potencjalnym liderem w centrum partnerskim został zaprojektowany tak, aby można było łatwo podłączyć się z informacjami o programie CRM do listy dostępnych systemów CRM. Teraz można łatwo korzystać z potencjalnych klientów wygenerowanych przez platformę Marketplace bez znacznego wysiłku inżynieryjnego do integracji z systemem zewnętrznym.

Poniżej przedstawiono instrukcje krok po kroku dotyczące sposobu łączenia poszczególnych możliwych miejsc docelowych potencjalnych klientów:

**Dynamics CRM Online** — zobacz [Konfigurowanie zarządzania potencjalnym klientem dla programu Dynamics 365 na potrzeby zaangażowania klienta](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) , aby uzyskać instrukcje dotyczące sposobu konfigurowania usługi Dynamics CRM Online na potrzeby uzyskiwania potencjalnych klientów.

**Marketo** — zobacz [Konfigurowanie zarządzania potencjalnymi klientami w programie Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) , aby uzyskać instrukcje dotyczące konfigurowania konfiguracji lidera programu Marketo w celu uzyskania potencjalnych klientów

**Salesforce** — zobacz [Konfigurowanie zarządzania potencjalnym klientem dla usługi Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) , aby uzyskać instrukcje dotyczące konfigurowania wystąpienia usługi Salesforce w celu uzyskania potencjalnych klientów.

**Tabela platformy Azure** — zobacz [Konfigurowanie zarządzania potencjalnym klientem za pomocą tabeli platformy Azure,](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) Aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure Storage na potrzeby uzyskiwania potencjalnych klientów w tabeli platformy Azure.

**Punkt końcowy https** — zobacz [Konfigurowanie zarządzania potencjalnym liderem za pomocą punktu końcowego HTTPS,](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) Aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego HTTPS w celu uzyskania potencjalnych klientów.

Po poprawnym skonfigurowaniu miejsca docelowego potencjalnego klienta i poprosić o opublikowanie oferty zostanie zweryfikowane połączenie i zostanie wysłany klient testowy. Gdy przeglądasz ofertę przed rozpoczęciem pracy, możesz również przetestować połączenie z liderem, próbując uzyskać swoją ofertę w środowisku wersji zapoznawczej. Ważne jest, aby upewnić się, że Twoje ustawienia lidera są aktualne, dzięki czemu nie utracisz żadnych potencjalnych klientów, więc pamiętaj, aby zaktualizować te połączenia, gdy coś się zmieniło.

### <a name="what-are-the-next-steps"></a>Jakie są następne kroki?

Po zakończeniu instalacji technicznej należy uwzględnić te potencjalną pracę w bieżącej sprzedaży & strategii marketingowej i procesów operacyjnych. Chcemy zrozumieć swój ogólny proces sprzedaży i najlepiej współpracować z nim w celu zapewnienia wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby zapewnić pomyślne działanie. Będziemy nam poznamy Twoją opinię na temat sposobu, w jaki możemy zoptymalizować i zwiększyć liczbę potencjalnych klientów, którzy wyślą Ci dodatkowe dane w celu pomyślnego przeprowadzenia tych klientów. Daj nam znać, Jeśli interesuje Cię przekazywanie opinii i sugestii, aby umożliwić zespołowi sprzedaży lepsze pomyślną pracę z komercyjnymi klientami portalu Marketplace.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a> Typowe błędy konfiguracji potencjalnych klientów podczas publikowania w centrum partnerskim

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Sprawdź ustawienia konta programu Dynamics CRM. LastCRMError: nie można zalogować się do programu Dynamics CRM, LastCRMException:** 

> Jeśli wybrano opcję Uwierzytelnianie Microsoft 365, sprawdź, czy konto użytkownika i hasło są prawidłowe. Jeśli wybrano Azure Active Directory, sprawdź, czy identyfikator dzierżawy, identyfikator aplikacji i klucz tajny aplikacji są zgodne z ustawieniem na Azure Active Directory. Postępuj zgodnie z instrukcjami [tutaj](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Jeśli nazwa użytkownika/hasło konta jest prawidłowe, upewnij się, że ma ona dostęp do usługi Dynamics 365 i ma przypisaną licencję (kroki 11-15 w przypadku używania Azure Active Directory lub ustawień zabezpieczeń, jeśli używasz użytkownika pakietu Office). 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Użytkownik nie ma uprawnień do tworzenia dla atrybutu leadsourcecode w jednostce potencjalnego klienta.** 

> W aplikacji/użytkowniku brakuje roli zabezpieczeń, aby Microsoft Marketplace składnik zapisywania klienta. Wykonaj kroki 11-15 w przypadku używania Azure Active Directory lub ustawienia zabezpieczeń, jeśli używasz [tutaj](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)użytkownika pakietu Office.

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: nie znaleziono dzierżawy. To wystąpienie może wystąpić, jeśli nie ma aktywnych subskrypcji dla dzierżawy.**  

> Identyfikator katalogu podany w sekcji Zarządzanie potencjalnym liderem nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu zgodnie z instrukcjami w kroku 2 (w obszarze Azure Active Directory) [tutaj](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser nie powiodła się — żadne role nie są przypisane do użytkownika.**  

> Rozwiązanie: Przypisz rolę zabezpieczeń do Microsoft Marketplace zapisywania klienta. Postępuj [zgodnie z instrukcjami w obszarze](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) ustawienia zabezpieczeń.

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: aplikacja z identyfikatorem nie została znaleziona w katalogu** 

> Identyfikator aplikacji podany w sekcji zarządzania potencjalnym liderem nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu zgodnie z instrukcjami w kroku 8 (w obszarze Azure Active Directory, w [tym miejscu](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: żądany identyfikator dzierżawy jest nieprawidłowy i nie jest prawidłowym formatem domeny zewnętrznej** 

> Identyfikator katalogu podany w sekcji Zarządzanie potencjalnym liderem nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu zgodnie z instrukcjami w kroku 2 (w obszarze Azure Active Directory, w [tym miejscu](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: błąd podczas walidacji poświadczeń.: podano nieprawidłowy klucz tajny klienta.** 

> Rozwiązanie: Zaloguj się do Azure Portal, sprawdź, czy klucz aplikacji jest zgodny z tym, co znajduje się w centrum partnerskim. Wygeneruj hasło na podstawie instrukcji w kroku 10 (w obszarze Azure Active Directory), w [tym miejscu](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: upłynął limit czasu kanału żądania podczas oczekiwania na odpowiedź po 00:02:00. Zwiększ wartość limitu czasu przekazaną do wywołania żądania lub Zwiększ wartość właściwości SendTimeout dla powiązania. Czas przydzielony na tę operację mógł być częścią dłuższego limitu czasu.**  

> Rozwiązanie: Zaloguj się do Centrum partnerskiego, sprawdź ustawienia oferty >> klienci klienta >> adres URL, sprawdź, czy jest to prawidłowe dynamiczne wystąpienie programu CRM.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Co to są potencjalni klienci i dlaczego są ważne dla mnie jako Wydawca w portalu Marketplace?** 

Potencjalni klienci to klienci, którzy wdrażają produkty z portalu Marketplace. Bez względu na to, czy Twój produkt znajduje się w witrynie [Azure Marketplace](https://azuremarketplace.microsoft.com) , czy na [AppSource](https://appsource.microsoft.com/), będziesz mieć możliwość otrzymywania potencjalnych klientów, którzy interesują Twój produkt, jeśli skonfigurowano miejsce docelowe potencjalnego klienta w ofercie.  

**Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?** 

Dokumentację można znaleźć w witrynie [Uzyskaj potencjalnych](./partner-center-portal/commercial-marketplace-get-customer-leads.md) klientów lub przesłać bilet pomocy technicznej w [pomocy i obsłudze technicznej](https://aka.ms/marketplacepublishersupport). Wybierz pozycję typ oferty i zarządzanie potencjalnymi klientami. 

**Czy muszę skonfigurować miejsce docelowe potencjalnego klienta w celu opublikowania oferty w portalu Marketplace?**

Tak, w przypadku publikowania aplikacji kontakt ze mną SaaS lub usług konsultingowych.  

**Jak upewnić się, że konfiguracja lidera jest poprawna?**

Po skonfigurowaniu oferty i przejściu do potencjalnego klienta Opublikuj swoją ofertę. Na etapie weryfikacji potencjalnego klienta Portal Marketplace wyśle test do miejsca docelowego potencjalnego klienta skonfigurowanego w ofercie. 

**Jak mogę znaleźć potencjalnego klienta testowego?**

Wyszukaj ciąg "MSFT_TEST" w miejscu docelowym potencjalnego klienta, poniżej przedstawiono przykładowe dane potencjalnego klienta testu: 

```text
company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 
```

**Mam ofertę na żywo, ale nie widzę żadnych potencjalnych klientów?**

Każdy potencjalny klient będzie miał dane przekazaną w polach w wybranym miejscu docelowym potencjalnego klienta, a potencjalni klienci będą mieli następujący format: **Źródło-akcja | Oferta**

- *Źródeł*
  - AzureMarketplace
  - AzurePortal
  - TestDrive  
  - SPZA (akronim dla AppSource)

- *Wykonane*
  - "INS" — oznacza instalację. Jest to w witrynie Azure Marketplace lub AppSource za każdym razem, gdy klient trafi na przycisk, aby uzyskać produkt.
  - "PLT" — oznacza próbę przetestowania partnera. Jest to AppSource za każdym razem, gdy klient trafi na przycisk kontakt ze mną.
  - "DNC" — oznacza nie kontakt. Ta wartość jest włączona w witrynie AppSource, gdy Partner, który został krzyżowo wymieniony na stronie Twojej aplikacji, otrzymuje prośbę o kontakt. Udostępniamy, że ten klient został krzyżowo wymieniony w Twojej aplikacji, ale nie trzeba się z nimi skontaktować.
  - "Utwórz" — Ta wartość jest dostępna tylko w Azure Portal i jest zawsze, gdy klient kupi ofertę na swoje konto.
  - "StartTestDrive" — dotyczy tylko dysków testowych i jest za każdym razem, gdy klient uruchamia ich dysk testowy.

- *Otrzymane*
  - "Checkpoint. Check-Point-r77-10sg-BYOL",
  - "Bitnami. openedxcypress",
  - "Docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a"

*Poniżej przedstawiono przykładowe dane dotyczące informacji o kliencie*

```json
{ 
"FirstName":"John",
"LastName":"Smith",
"Email":"jsmith@microsoft.com",
"Phone":"1234567890",
"Country":"US",
"Company":"Microsoft",
"Title":"CTO"
}
```

Więcej informacji znajduje się w obszarze [Informacje o potencjalnym kliencie](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Mam skonfigurowany obiekt BLOB platformy Azure jako miejsce docelowe potencjalnego klienta, dlaczego nie widzę potencjalnego klienta?** 

Potencjalny klient zostanie zapisany tylko w przypadku wybrania usługi Azure BLOB Storage jako miejsca docelowego potencjalnego klienta. Przejdź do tabeli platformy Azure, aby uzyskać rzeczywisty czas realizacji potencjalnego klienta.

**Dlaczego nie mogę znaleźć potencjalnego klienta w portalu Marketplace?**  

Istnieje możliwość, że domena poczty e-mail użytkownika końcowego pochodzi z. edu. Ze względu na prywatność nie przekazujemy danych osobowych z domeny. edu. Prześlij bilet pomocy technicznej w [pomocy i obsłudze technicznej](https://aka.ms/marketplacepublishersupport).

**Mam skonfigurowaną usługę Azure Table/Azure BLOB jako lokalizację docelową mojego potencjalnego klienta, jak mogę wyświetlić potencjalnych klientów?** 

Możesz uzyskać dostęp do obiektu BLOB lub tabeli z Azure Portal lub pobrać i zainstalować [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) bezpłatnie, aby wyświetlić tabele/obiekty blob konta usługi Azure Storage. 

**Mam skonfigurowaną tabelę platformy Azure jako lokalizację docelową mojego potencjalnego klienta, czy mogę otrzymywać powiadomienia za każdym razem, gdy nowy potencjalny klient zostanie wysłany przez portal Marketplace?** 

Tak, postępuj zgodnie z instrukcjami, aby skonfigurować funkcję Azure Table + na [tej dokumentacji.](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) 

**Mam skonfigurowaną usługi Salesforce jako lokalizację docelową mojego potencjalnego klienta, dlaczego nie mogę znaleźć potencjalnych klientów?**

Sprawdź, czy formularz sieci Web do realizacji jest polem obowiązkowym opartym na liście wyboru. Jeśli tak, przełącz się na pole do nieobowiązkowego pola tekstowego.  
 
**Wystąpił problem z miejscem docelowym potencjalnego klienta i pominięto kilka potencjalnych klientów. Czy mogę je wysłać do mnie w wiadomości e-mail?** 

Ze względu na zasady ochrony prywatności nie możemy udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonej poczty e-mail. 

**Mam skonfigurowany magazyn platformy Azure (obiekt BLOB/tabela) jako miejsce docelowe potencjalnego klienta, ile będzie kosztować?** 

Dane generowania potencjalnych klientów są niskie (<1 GB dla niemal wszystkich wydawców). Koszt będzie zależeć od liczby odebranych potencjalnych klientów, jeśli w ciągu miesiąca otrzymasz 1 000 potencjalnych klientów, koszty związane z tym obejmują około 50 centów. 
