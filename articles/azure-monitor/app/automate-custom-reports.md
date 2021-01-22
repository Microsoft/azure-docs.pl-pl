---
title: Automatyzowanie niestandardowych raportów za pomocą usługi Azure Application Insights Data
description: Automatyzowanie niestandardowych raportów codziennych/cotygodniowych/miesięcznych przy użyciu usługi Azure Application Insights Data
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: da465fbc899cb52fc48dbde830d3db4f3b58c68b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680454"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatyzowanie niestandardowych raportów za pomocą usługi Azure Application Insights Data

Raporty okresowe ułatwiają zespołowi informowanie o tym, jak działają usługi o krytycznym znaczeniu dla firmy. Deweloperzy, zespoły DevOps/SRE i ich menedżerów mogą wydajnie pracować dzięki automatycznym raportom niezawodnym dostarczającym szczegółowe informacje bez konieczności logowania się do portalu. Takie raporty mogą również pomóc w identyfikowaniu stopniowego wzrostu opóźnień, obciążenia lub częstotliwości niepowodzeń, które mogą nie wyzwalać żadnych reguł alertów.

Każde przedsiębiorstwo ma swoje unikatowe potrzeby związane z raportowaniem, na przykład:

* Określone agregacja percentylu metryk lub metryki niestandardowe w raporcie.
* Różne raporty codziennie, co tydzień i miesięczne zestawienie danych dla różnych odbiorców.
* Segmentacja według atrybutów niestandardowych, takich jak region lub środowisko.
* Grupowanie zasobów AI w jednym raporcie, nawet jeśli mogą znajdować się w różnych subskrypcjach lub grupach zasobów itd.
* Oddzielne raporty zawierające poufne metryki wysyłane do selektywnych odbiorców.
* Raporty do uczestników projektu, którzy mogą nie mieć dostępu do zasobów portalu.

> [!NOTE] 
> Cotygodniowa poczta e-mail Application Insights Digest nie zezwolił na dostosowanie i zostanie wycofana na korzyść opcji niestandardowych wymienionych poniżej. Ostatnia tygodniowa wiadomość e-mail z podsumowaniem zostanie wysłana od 11 czerwca 2018. Skonfiguruj jedną z następujących opcji, aby uzyskać podobne niestandardowe raporty (Użyj zapytania z sugestią poniżej).

## <a name="to-automate-custom-report-emails"></a>Aby zautomatyzować wiadomości e-mail dotyczące raportów niestandardowych

Można [programowo wykonywać zapytania dotyczące Application Insights](https://dev.applicationinsights.io/) danych, aby generować raporty niestandardowe zgodnie z harmonogramem. Następujące opcje mogą pomóc szybko rozpocząć pracę:

* [Automatyzowanie raportów za pomocą automatyzacji](../platform/logicapp-flow-connector.md)
* [Automatyzowanie raportów za pomocą Logic Apps](automate-with-logic-apps.md)
* Użyj szablonu [funkcji platformy Azure](../../azure-functions/functions-get-started.md) "Application Insights zaplanowanego skrótu" w scenariuszu monitorowania. Ta funkcja używa SendGrid do dostarczania wiadomości e-mail. 

    ![Szablon funkcji platformy Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Przykładowe zapytanie dla cotygodniowej wiadomości e-mail
Poniższe zapytanie pokazuje sprzężenie wielu zestawów danych w celu uzyskania cotygodniowej wiadomości e-mail zawierającej podsumowanie. Dostosuj ją zgodnie z potrzebami i użyj jej w celu zautomatyzowania cotygodniowego raportu.

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Raport Application Insights zaplanowanego podsumowania

1. Utwórz aplikacja funkcji platformy Azure. _(Application Insights jest_ wymagane tylko wtedy, gdy chcesz monitorować nowy aplikacja funkcji z Application Insights)

   Odwiedź dokumentację Azure Functions, aby dowiedzieć się, jak [utworzyć aplikację funkcji](../../azure-functions/functions-get-started.md)

2. Po zakończeniu wdrażania nowego aplikacja funkcji wybierz pozycję **Przejdź do zasobu**.

3. Wybierz pozycję **Nowa funkcja**.

   ![Utwórz nowy zrzut ekranu funkcji](./media/automate-custom-reports/new-function.png)

4. Wybierz **_szablon Application Insights zaplanowanego podsumowania_**.

     > [!NOTE]
     > Domyślnie aplikacje funkcji są tworzone przy użyciu środowiska uruchomieniowego w wersji 3. x. Musisz mieć [Azure Functions środowiska uruchomieniowego w wersji](../../azure-functions/set-runtime-version.md) **1. x** , aby użyć szablonu Application Insights zaplanowanego podsumowania. Przejdź do pozycji Konfiguracja > ustawienia środowiska uruchomieniowego, aby zmienić wersję środowiska uruchomieniowego. ![zrzut ekranu środowiska uruchomieniowego](./media/automate-custom-reports/change-runtime-v.png)

   ![Zrzut ekranu szablonu nowej funkcji Application Insights](./media/automate-custom-reports/function-app-04.png)

5. Wprowadź odpowiedni adres e-mail odbiorcy dla raportu i wybierz pozycję **Utwórz**.

   ![Zrzut ekranu ustawień funkcji](./media/automate-custom-reports/scheduled-digest.png)

6. Wybierz konfigurację   >  **funkcji platformy** aplikacja funkcji  >  .

    ![Zrzut ekranu ustawień aplikacji funkcji platformy Azure](./media/automate-custom-reports/config.png)

7. Utwórz trzy nowe ustawienia aplikacji z odpowiednimi odpowiednimi wartościami ``AI_APP_ID`` , ``AI_APP_KEY`` i ``SendGridAPI`` . Wybierz pozycję **Zapisz**.

     ![Zrzut ekranu interfejsu integracji funkcji](./media/automate-custom-reports/app-settings.png)
    
    (Wartości AI_ można znaleźć w obszarze dostęp do interfejsu API dla zasobu Application Insights, dla którego chcesz utworzyć raport. Jeśli nie masz klucza interfejsu API Application Insights, istnieje możliwość **utworzenia klucza interfejsu API**.
    
   * AI_APP_ID = identyfikator aplikacji
   * AI_APP_KEY = klucz interfejsu API
   * SendGridAPI = SendGrid — klucz interfejsu API

     > [!NOTE]
     > Jeśli nie masz konta SendGrid, możesz go utworzyć. Dokumentacja usługi SendGrid dla Azure Functions jest [tutaj](../../azure-functions/functions-bindings-sendgrid.md). Jeśli chcesz tylko określić, jak skonfigurować SendGrid, i wygenerować klucz interfejsu API, który jest dostępny na końcu tego artykułu. 

8. Wybierz pozycję **integracja** i w obszarze wyjściowe kliknij pozycję **SendGrid ($Return)**.

     ![Zrzut ekranu wyjściowego](./media/automate-custom-reports/integrate.png)

9. W obszarze **Ustawienia aplikacji klucza SendGridAPI** wybierz nowo utworzone ustawienie aplikacji dla **SendGridAPI**.

     ![Uruchom aplikacja funkcji zrzut ekranu](./media/automate-custom-reports/sendgrid-output.png)

10. Uruchom i przetestuj aplikacja funkcji.

     ![Zrzut ekranu testu](./media/automate-custom-reports/function-app-11.png)

11. Sprawdź wiadomość e-mail, aby upewnić się, że wiadomość została wysłana/odebrana pomyślnie.

     ![Zrzut ekranu z wierszem tematu wiadomości E-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid z platformą Azure

Te kroki mają zastosowanie tylko wtedy, gdy nie masz jeszcze skonfigurowanego konta SendGrid.

1. W Azure Portal wybierz pozycję **Utwórz zasób** > Wyszukaj **SendGrid poczty e-mail** > kliknij pozycję **Utwórz** > Wypełnij odpowiednie instrukcje tworzenia SendGrid.

     ![Utwórz zrzut ekranu zasobów SendGrid](./media/automate-custom-reports/sendgrid.png)

2. Po utworzeniu konta w obszarze SendGrid wybierz pozycję **Zarządzaj**.

     ![Zrzut ekranu klucza interfejsu API ustawień](./media/automate-custom-reports/sendgrid-manage.png)

3. Spowoduje to uruchomienie witryny SendGrid. Wybierz pozycję **Ustawienia**  >  **klucze interfejsu API**.

     ![Tworzenie i Wyświetlanie zrzutu ekranu aplikacji klucza interfejsu API](./media/automate-custom-reports/function-app-15.png)

4. Utwórz klucz interfejsu API > wybierz pozycję **utwórz & widok**. (Zapoznaj się z dokumentacją SendGrid dotyczącą ograniczonego dostępu, aby określić, jaki poziom uprawnień jest odpowiedni dla klucza interfejsu API. Pełny dostęp jest wybierany tutaj tylko do celów.

   ![Zrzut ekranu pełnego dostępu](./media/automate-custom-reports/function-app-16.png)

5. Skopiuj cały klucz, ta wartość jest wymagana w ustawieniach aplikacja funkcji jako wartość SendGridAPI

   ![Kopiuj zrzut ekranu klucza interfejsu API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat tworzenia [zapytań analitycznych](../log-query/get-started-queries.md).
* Dowiedz się więcej na temat [programowo przeszukiwania danych Application Insights](https://dev.applicationinsights.io/)
* Dowiedz się więcej na temat usługi [Logic Apps](../../logic-apps/logic-apps-overview.md).
* Dowiedz się więcej o [programie Microsoft PowerShell](https://ms.flow.microsoft.com).
