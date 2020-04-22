---
title: Konfigurowanie zarządzania potencjalnymi klientami przy użyciu punktu końcowego HTTPS | Azure Marketplace
description: Dowiedz się, jak używać punktu końcowego HTTP do obsługi potencjalnych klientów microsoft appsource i portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770196"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurowanie zarządzania potencjalnymi klientami przy użyciu punktu końcowego HTTPS

Punktu końcowego protokołu HTTPS można używać do obsługi potencjalnych klientów microsoft appsource i portalu Azure Marketplace. Przewody te mogą być zapisywane do systemu zarządzania relacjami z klientami (CRM) lub wysyłane jako powiadomienie e-mail. W tym artykule opisano sposób konfigurowania zarządzania potencjalnymi klientami za pomocą usługi [automatyzacji automatyzacji microsoft power automatyki.](https://powerapps.microsoft.com/automate-processes/)

## <a name="create-a-flow-using-microsoft-power-automate"></a>Tworzenie przepływu przy użyciu automatyzacji zasilania Microsoft Power

1. Otwórz stronę power [automatyzuj.](https://flow.microsoft.com/) Wybierz **pozycję Zaloguj się** lub wybierz zarejestruj się **bezpłatnie,** aby utworzyć bezpłatne konto Flow.

1. Zaloguj się i wybierz **pozycję Moje przepływy** na pasku menu.
    > [!div class="mx-imgBorder"]
    > ![Moje przepływy](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. W obszarze **+ Nowy**, wybierz **+ Natychmiastowy — z pustego**.
    > [!div class="mx-imgBorder"]
    > ![Tworzenie z pustego](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nazwij przepływ, a następnie w obszarze **Wybierz sposób wyzwalania tego przepływu**wybierz pozycję Po **odebraniu żądania HTTP**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie wyzwalacza odebranego żądania HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kliknij krok przepływu, aby go rozwinąć.

    > [!div class="mx-imgBorder"]
    > ![Rozwiń krok przepływu](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Użyj jednej z następujących metod, aby skonfigurować **schemat JSON treści żądania:**

   - Skopiuj [schemat JSON](#json-schema) na końcu tego artykułu do pola tekstowego **Schemat JSON treści żądania.**
   - Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. W polu **tekstowym Wprowadzanie lub wklejanie przykładowego ładunku JSON** wklej w [przykładzie JSON](#json-example). Wybierz **pozycję Gotowe,** aby utworzyć schemat.

   >[!Note]
   >W tym momencie w przepływie można połączyć się z systemem CRM lub skonfigurować powiadomienie e-mail.

### <a name="to-connect-to-a-crm-system"></a>Aby połączyć się z systemem CRM

1. Wybierz pozycję **+ Nowy krok**.
2. Wybierz wybrany system CRM z akcją, aby utworzyć nowy rekord. Następujące zrzut ekranu pokazuje **Dynamics 365 — utwórz nowy rekord** jako przykład.

    ![Tworzenie nowego rekordu](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Podaj **nazwę organizacji,** która jest wejściami połączenia dla łącznika. Wybierz **pozycję Potencjalni klienci** z listy rozwijanej Nazwa **encji.**

    ![Wybieranie potencjalnych klientów](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Przepływ pokazuje formularz do dostarczania informacji o potencjalnych klientach. Elementy można mapować z żądania wprowadzania, wybierając opcję dodawania zawartości dynamicznej. Poniższe zrzut ekranu pokazuje **OfferTitle** jako przykład.

    ![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Zamapuj żądane pola, a następnie wybierz pozycję **Zapisz,** aby zapisać przepływ.

6. W żądaniu zostanie utworzony adres URL wpisu HTTP. Skopiuj ten adres URL i użyj go jako punktu końcowego HTTPS.

    ![Adres URL wpisu HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Aby skonfigurować powiadomienia e-mail

1. Wybierz pozycję **+ Nowy krok**.
2. W obszarze **Wybierz akcję**wybierz pozycję **Akcje**.
3. W obszarze **Akcje** wybierz pozycję **Wyślij wiadomość e-mail**.

    ![Dodawanie akcji e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. W **polu Wyślij wiadomość e-mail**skonfiguruj następujące wymagane pola:

   - **Do** - Wprowadź co najmniej jeden prawidłowy adres e-mail.
   - **Temat** — przepływ umożliwia dodanie zawartości dynamicznej, takiej jak **LeadSource** w poniższym przechwytywaniu ekranu.

     ![Dodawanie akcji wiadomości e-mail przy użyciu zawartości dynamicznej](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Treść** — z listy Zawartość dynamiczna dodaj żądane informacje w treści wiadomości e-mail. Na przykład LastName, FirstName, E-mail i Firma.

   Po zakończeniu konfigurowania powiadomienia e-mail będzie wyglądać jak przykład w poniższym przechwytywaniu ekranu.

   ![Dodawanie akcji e-mail](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Wybierz **pozycję Zapisz,** aby zakończyć przepływ.

6. W żądaniu zostanie utworzony adres URL wpisu HTTP. Skopiuj ten adres URL i użyj go jako punktu końcowego HTTPS.

    ![Adres URL wpisu HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do punktu końcowego HTTPS

Podczas konfigurowania informacji o zarządzaniu potencjalnymi klientami dla oferty wybierz pozycję **Punkt końcowy HTTPS** dla **miejsca docelowego potencjalnego klienta** i wklej adres URL HTTP POST skopiowany w poprzednim kroku.  

![Dodaj zawartość dynamiczną](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Gdy potencjalni klienci są generowani, firma Microsoft wysyła potencjalnych klientów do przepływu automatyzacji zasilania, które są kierowane do skonfigurowanyego systemu CRM lub adresu e-mail.

## <a name="json-schema-and-example"></a>Schemat JSON i przykład

W przykładzie testu JSON użyto następującego schematu:

### <a name="json-schema"></a>Schemat JSON

``` json
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
    "Description": {
      "id": "/properties/Description",
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

Można skopiować i edytować następujący przykład JSON do użycia jako test w przepływie.

### <a name="json-example"></a>Przykład JSON

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, skonfiguruj [potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) w portalu cloud partner.
