---
title: Komercyjne zarządzanie potencjalnymi klientami portalu Marketplace firmy Microsoft z użyciem protokołu HTTPS
description: Skonfiguruj zarządzanie liderem komercyjnego portalu Microsoft Marketplace dla punktu końcowego HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 1c3337e970fdbb22cb1ed88f105d5e7798a68f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133739"
---
# <a name="configure-lead-management-by-using-an-https-endpoint"></a>Konfigurowanie zarządzania liderem przy użyciu punktu końcowego HTTPS

>[!NOTE]
>Łącznik automatyzacji, używany w tych instrukcjach, wymaga płatnej subskrypcji do automatyzacji. Przed wykonaniem instrukcji opisanych w tym artykule upewnij się, że to konto jest odpowiednie.

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w centrum partnerskim, aby otrzymywać Microsoft AppSource i potencjalni klienci portalu Azure Marketplace, możesz użyć punktu końcowego HTTPS w programie PowerShell, aby obsłużyć te potencjalni klienci. Za pomocą punktu końcowego HTTPS te potencjalni klienci mogą być wysyłane jako powiadomienia e-mail lub mogą być zapisywane w systemie CRM obsługiwanym przez program. Instrukcje zawarte w tym artykule przeprowadzą Cię przez proces podstawowy, aby utworzyć nowy przepływ przy użyciu narzędzia do automatyzowania, które generuje adres URL post protokołu HTTP, który zostanie wprowadzony do portalu wydawców w polu > **adres URL punktu końcowego protokołu HTTPS** **zarządzania liderem**. Uwzględniono również instrukcje testowania przepływu za pomocą narzędzia o nazwie [Poster](https://www.getpostman.com/downloads/), które jest dostępne online.

## <a name="create-a-flow-by-using-power-automate"></a>Tworzenie przepływu za pomocą automatyzacji

1. Otwórz stronę sieci Web [automatyzacji](https://flow.microsoft.com/) . Wybierz pozycję **Zaloguj się**. Jeśli nie masz jeszcze konta, wybierz pozycję **zarejestruj się bezpłatnie** , aby utworzyć bezpłatne konto automatyzacji.

1. Zaloguj się, a następnie wybierz pozycję **Moje przepływy** w menu.

1. Wybierz pozycję **+ Automatyczne — od pustej**.

    ![Moje przepływy + automatyczne — z pustej](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. W oknie **kompilacja przepływu automatycznego** wybierz pozycję **Pomiń**. 

    ![Kompiluj przycisk pomijania okna przepływu automatycznego](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

1. W polu **Łączniki wyszukiwania i wyzwalacze** wprowadź **żądanie** znalezienia łącznika żądania.
1. W obszarze **wyzwalacze**wybierz opcję **po odebraniu żądania HTTP**. 

    ![Menu wyzwalaczy](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

1. W oknie dialogowym **po odebraniu żądania HTTP** skopiuj i wklej poniższy schemat JSON do pola tekstowego **schemat JSON treści żądania** . Ten schemat jest używany przez firmę Microsoft do przechowywania danych potencjalnych klientów.

    ![Pole tekstowe schematu JSON treści żądania](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Schemat JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!NOTE]
>W tym momencie konfiguracji można wybrać opcję nawiązywania połączenia z systemem CRM lub skonfigurować powiadomienie e-mail. Postępuj zgodnie z pozostałymi instrukcjami.

### <a name="connect-to-a-crm-system"></a>Nawiązywanie połączenia z systemem CRM

1. Wybierz pozycję **+ Nowy krok**.
1. Wybierz wybrany przez siebie system CRM, wyszukując go w miejscu, w którym widnieją **łączniki i akcje**. Wybierz ją na karcie **Akcje** z akcją, aby utworzyć nowy rekord. Na poniższym ekranie przedstawiono przykład **tworzenia nowego rekordu (Dynamics 365)** .

    ![Tworzenie nowego rekordu](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Podaj **nazwę organizacji** skojarzoną z systemem CRM. Wybierz pozycję **potencjalni klienci** z listy rozwijanej **Nazwa jednostki** .

    ![Wybierz potencjalnych klientów](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Automatyzacja w programie PowerShell przedstawia formularz do udostępniania informacji o potencjalnych klientach. Możesz mapować elementy z żądania wejściowego, wybierając pozycję Dodaj zawartość dynamiczną. Na poniższym ekranie przedstawiono przykład **OfferTitle** .

    ![Dodaj zawartość dynamiczną](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Zamapuj odpowiednie pola, a następnie wybierz pozycję **Zapisz** , aby zapisać przepływ. Adres URL POST protokołu HTTP jest tworzony i jest dostępny w oknie **po odebraniu żądania HTTP** . Skopiuj ten adres URL przy użyciu kontrolki Kopiuj znajdującej się po prawej stronie adresu URL POST protokołu HTTP. Użycie kontrolki Copy jest ważne, aby nie pominąć żadnej części całego adresu URL. Zapisz ten adres URL, ponieważ będzie on potrzebny podczas konfigurowania zarządzania liderem w portalu wydawców.

    ![Po odebraniu żądania HTTP](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Konfigurowanie powiadomienia e-mail

1. Po zakończeniu schematu JSON wybierz pozycję **+ nowy krok**.
1. W obszarze **Wybierz akcję**wybierz pozycję **Akcje**.
1. Na karcie **Akcje** wybierz pozycję **Wyślij wiadomość e-mail (Office 365 Outlook)**.

    >[!NOTE]
    >Jeśli chcesz użyć innego dostawcy poczty e-mail, Wyszukaj i wybierz opcję **Wyślij powiadomienie e-mail (poczta)** jako akcję.

    ![Dodaj akcję poczty e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. W oknie **wysyłanie wiadomości e-mail** skonfiguruj następujące wymagane pola:

   - **Do**: wprowadź co najmniej jeden prawidłowy adres e-mail, na który będą wysyłane potencjalni klienci.
   - **Temat**: energia automatyzuje zapewnia możliwość dodania zawartości dynamicznej, na przykład **LeadSource** pokazanej na poniższym ekranie. Zacznij od wprowadzenia nazwy pola. Następnie wybierz listę dynamiczne pobranie zawartości z okna podręcznego. 

        >[!NOTE] 
        > Po dodaniu nazw pól można wykonać każdą nazwę z dwukropkiem (:) a następnie wybierz **klawisz ENTER** , aby utworzyć nowy wiersz. Po dodaniu nazw pól można dodać każdy skojarzony parametr z listy pobrań dynamicznych.

        ![Dodawanie akcji poczty e-mail przy użyciu zawartości dynamicznej](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Treść**: z listy dynamiczne pobranie zawartości Dodaj odpowiednie informacje w treści wiadomości e-mail. Na przykład użyj LastName, FirstName, poczty E-mail i firmy. Po zakończeniu konfigurowania powiadomienia e-mail wygląda jak przykład na poniższym ekranie.


       ![Przykład powiadomienia e-mail](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Wybierz pozycję **Zapisz** , aby zakończyć przepływ. Adres URL POST protokołu HTTP jest tworzony i jest dostępny w oknie **po odebraniu żądania HTTP** . Skopiuj ten adres URL przy użyciu kontrolki Kopiuj znajdującej się po prawej stronie adresu URL POST protokołu HTTP. Użycie tego formantu jest ważne, aby nie pominąć żadnej części całego adresu URL. Zapisz ten adres URL, ponieważ będzie on potrzebny podczas konfigurowania zarządzania liderem w portalu wydawców.

   ![ADRES URL POST PROTOKOŁU HTTP](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testowanie

Możesz sprawdzić, czy wszystko działa zgodnie z oczekiwaniami przy użyciu narzędzia o nazwie [Poster](https://app.getpostman.com/app/download/win64), które można pobrać w trybie online. To narzędzie jest dostępne dla systemu Windows. 

1. Uruchom program Poster i wybierz pozycję **nowe** > **żądanie** , aby skonfigurować narzędzie testowe. 

   ![Żądanie skonfigurowania narzędzia testowego](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Wypełnij formularz **Zapisz żądanie** , a następnie zapisz go w utworzonym folderze.

   ![Zapisz formularz żądania](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Z listy rozwijanej wybierz pozycję **post** . 

   ![Testowanie mojego przepływu](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Wklej adres URL POST protokołu HTTP z przepływu utworzonego w programie w celu zautomatyzowania, w którym znajduje się **adres URL żądania**.

   ![Wklej adres URL POST protokołu HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Wróć do trybu [automatyzacji](https://flow.microsoft.com/). Znajdź utworzony przepływ w celu wysłania potencjalnych klientów, przechodząc do **obszaru Moje przepływy** z paska menu usługi Automatyzacja. Wybierz wielokropek obok nazwy przepływu, aby wyświetlić więcej opcji, a następnie wybierz pozycję **Edytuj**.


1. Wybierz pozycję **Testuj** w prawym górnym rogu, wybierz opcję **Chcę wykonać akcję wyzwalacza**, a następnie wybierz pozycję **Testuj**. Na górze ekranu zobaczysz wskazanie, że test został uruchomiony.

   ![Wykonaję akcję wyzwalacza](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Wróć do aplikacji w programie Poster i wybierz pozycję **Wyślij**.

   ![Przycisk Wyślij](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Wróć do przepływu i sprawdź wynik. Jeśli wszystko działa zgodnie z oczekiwaniami, zobaczysz komunikat informujący o tym, że przepływ zakończył się pomyślnie.

   ![Sprawdź wyniki](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Użytkownik powinien również otrzymać wiadomość e-mail. Sprawdź skrzynkę odbiorczą poczty e-mail. 

    >[!NOTE] 
    >Jeśli nie widzisz wiadomości e-mail z testu, Sprawdź swoje spam i foldery śmieci. Na poniższym ekranie zobaczysz tylko etykiety pól dodane podczas konfigurowania powiadomienia e-mail. Jeśli jako rzeczywisty potencjalny klient został wygenerowany z oferty, zobaczysz również rzeczywiste informacje z kontaktu potencjalnego klienta w treści i w wierszu tematu.

   ![Odebrano wiadomość e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące kroki.

1. Przejdź do strony **konfiguracji oferty** oferty.
1. Wybierz pozycję **Połącz** w sekcji **Zarządzanie potencjalnymi klientami** .
1. W oknie podręcznym **szczegóły połączenia** wybierz pozycję **punkt końcowy https** dla **miejsca docelowego potencjalnego klienta**. Wklej adres URL POST protokołu HTTP z przepływu utworzonego przez wykonanie wcześniejszych kroków w polu **adres URL punktu końcowego HTTPS** .
1. W obszarze **kontaktowy adres e-mail**wprowadź adresy e-mail osób w firmie, które powinny otrzymywać powiadomienia e-mail po odebraniu nowego potencjalnego klienta. Można podać wiele wiadomości e-mail, rozdzielając je średnikami.
1. Wybierz przycisk **OK**.

Aby upewnić się, że pomyślnie nawiązano połączenie z miejscem docelowym potencjalnego klienta, wybierz przycisk **Weryfikuj** . Jeśli się powiedzie, będziesz mieć test w miejscu docelowym potencjalnego klienta.

>[!NOTE] 
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

Gdy są generowane potencjalni klienci, firma Microsoft wysyła potencjalni klienci do przepływu. Potencjalni klienci są kierowani do skonfigurowanego systemu CRM lub adresu e-mail.

![Przycisk Połącz z zarządzaniem potencjalnym klientem](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Miejsce docelowe lidera szczegółów połączenia](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Adres e-mail osoby kontaktowej szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

