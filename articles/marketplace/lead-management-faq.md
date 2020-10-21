---
title: Pytania dotyczące zarządzania potencjalnymi klientami i rozwiązywanie problemów — centrum partnerskie firmy Microsoft
description: Zapoznaj się z typowymi błędami i pytaniami dotyczącymi konfigurowania komercyjnych liderów portalu Marketplace w centrum partnerskim
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330924"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Często zadawane pytania i rozwiązywanie problemów z konfiguracją klienta

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące zarządzania potencjalnymi klientami w ramach ofert komercyjnych w portalu Marketplace. Dotyczy to także błędów, które mogą wystąpić podczas konfigurowania potencjalnych klientów w systemie zarządzania relacjami klienta (CRM) w centrum partnerskim.

## <a name="common-questions-about-lead-management"></a>Często zadawane pytania dotyczące zarządzania potencjalnymi klientami

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?

Zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md) , aby zapoznać się z omówieniem sposobu łączenia systemu CRM z komercyjnymi ofertami portalu Marketplace. Jeśli wystąpi błąd, zapoznaj się z poniższymi wskazówkami dotyczącymi rozwiązywania problemów. Aby uzyskać pomoc techniczną, Prześlij bilet pomocy technicznej za pomocą [pomocy i obsługi technicznej Centrum partnerskiego](https://aka.ms/marketplacepublishersupport). Następnie wybierz pozycję **Utwórz ofertę**  >  **Typ**oferty  >  **Konfiguracja zarządzania liderem**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Czy muszę skonfigurować miejsce docelowe potencjalnego klienta w celu opublikowania oferty w portalu komercyjnym?

Odpowiedź zależy od typu publikowanej oferty. Oprogramowanie jako usługa (SaaS) i Dynamics 365 Customer Engagement używają do **kontaktowania** się ze wszystkimi usługami Dynamics 365 for Finanse i operacje, wszystkie oferty usługi Dynamics 365 Business Central i wszystkie oferty usług konsultingowych. W związku z tym wymagają połączenia z miejscem docelowym potencjalnego klienta. Jeśli typu oferty nie ma na liście, połączenie z miejscem docelowym potencjalnego klienta nie jest wymagane. Zalecamy skonfigurowanie miejsca docelowego potencjalnego klienta, aby nie przegapić możliwości biznesowych.

#### <a name="how-can-i-find-the-test-lead"></a>Jak mogę znaleźć potencjalnego klienta testowego?

Wyszukaj `"MSFT_TEST"` w miejscu docelowym potencjalnego klienta. Poniżej znajduje się przykładowy test potencjalnego klienta firmy Microsoft. Należy zauważyć, że format ołowiu testowego różni się w zależności od miejsca docelowego potencjalnego klienta.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Mam ofertę na żywo, ale dlaczego nie widzę żadnych potencjalnych klientów?

Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest prawidłowe. Wyślemy Ci lidera testowego po wybraniu opcji **Publikuj** w ofercie w centrum partnerskim. Jeśli widzisz potencjalnego klienta testowego, połączenie jest prawidłowe. Możesz również przetestować połączenie z liderem, próbując uzyskać podgląd oferty w ramach kroku wersji zapoznawczej. Wybierz pozycję **Pobierz teraz**, **skontaktuj się z**nami lub **bezpłatną wersję próbną** na liście w portalu komercyjnym.

Upewnij się również, że szukasz odpowiednich danych. Zapoznaj się z tematem informacje o [potencjalnych klientach](partner-center-portal/commercial-marketplace-get-customer-leads.md) , aby poznać dane potencjalnych klientów, które wysyłamy do miejsca docelowego potencjalnego klienta.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Mam skonfigurowany magazyn obiektów blob platformy Azure jako miejsce docelowe potencjalnego klienta, ale dlaczego nie widzę potencjalnego klienta?

Usługa Azure Blob Storage nie jest już obsługiwana jako miejsce docelowe potencjalnego klienta, dlatego nie masz żadnych potencjalnych klientów wygenerowanych przez ofertę. Przełącz się do dowolnego z opcji innych elementów [docelowych potencjalnego klienta](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Z komercyjnej witryny Marketplace otrzymałem wiadomość e-mail, ale dlaczego nie mogę znaleźć potencjalnego klienta w moim programie CRM?

Istnieje możliwość, że domena poczty e-mail użytkownika końcowego pochodzi z. edu. Ze względu na prywatność nie przekazujemy informacji osobistych z domeny. edu. Prześlij bilet pomocy technicznej, korzystając z [pomocy i pomocy technicznej Centrum partnerskiego](https://aka.ms/marketplacepublishersupport).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Tabela platformy Azure została skonfigurowana jako lokalizacja docelowa mojego potencjalnego klienta. Jak mogę wyświetlić potencjalnych klientów?

Możesz uzyskać dostęp do danych potencjalnych klientów przechowywanych w tabeli platformy Azure z Azure Portal. Możesz również pobrać i zainstalować [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) bezpłatnie, aby wyświetlić dane tabeli dla konta usługi Azure Storage.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Tabela platformy Azure została skonfigurowana jako lokalizacja docelowa mojego potencjalnego klienta. Czy mogę otrzymywać powiadomienia o każdym wysłaniu nowego komercyjnego lidera rynku?

Tak. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie zarządzania potencjalnymi klientami przy użyciu tabeli platformy Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) w celu skonfigurowania usługi Microsoft Flow, która wysyła wiadomość e-mail, jeśli potencjalny klient zostanie dodany do tabeli platformy Azure.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Mam skonfigurowany program Salesforce jako lokalizację docelową mojego potencjalnego klienta, ale dlaczego nie mogę znaleźć potencjalnych klientów?

Sprawdź, czy formularz sieci Web do potencjalnego klienta jest polem obowiązkowym opartym na liście wyboru. Jeśli tak jest, przełącz pole do nieobowiązkowego pola tekstowego.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Wystąpił problem z miejscem docelowym potencjalnego klienta i pominięto kilka potencjalnych klientów. Czy mogę je wysłać do mnie w wiadomości e-mail?

Ze względu na zasady dotyczące informacji osobistych nie można udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonej poczty e-mail.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Tabela platformy Azure została skonfigurowana jako lokalizacja docelowa mojego potencjalnego klienta. Ile to będzie kosztować?

Dane generowania potencjalnego klienta są niskie. Jest ona mniejsza niż 1 GB dla niemal wszystkich wydawców. Koszt zależy od liczby odebranych potencjalnych klientów. Jeśli na przykład w miesiącu otrzymasz 1 000 potencjalnych klientów, koszt wynosi około 50 centów. Aby uzyskać więcej informacji o cenach magazynu, zobacz [Cennik usługi Azure Storage — Omówienie](https://azure.microsoft.com/pricing/details/storage/).

Jeśli Twoje pytanie nie zostało odebrane, skontaktuj się z firmą pomoc techniczna firmy Microsoft za pomocą [pomocy i pomocy technicznej Centrum partnerskiego](https://aka.ms/marketplacepublishersupport). Następnie wybierz pozycję **Utwórz ofertę**  >  **Typ**oferty  >  **Konfiguracja zarządzania liderem**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Otrzymuję powiadomienia e-mail po odebraniu nowych potencjalnych klientów. Jak skonfigurować kogoś innego do otrzymywania tych wiadomości e-mail?

Uzyskaj dostęp do oferty w centrum partnerskim i przejdź do strony **Konfiguracja oferty** > **Zarządzanie potencjalnymi klientami**  >  **Edit**. Zaktualizuj adresy e-mail w polu **kontaktowy adres e-mail** .

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Rozwiązywanie problemów z błędami konfiguracji potencjalnego klienta

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

