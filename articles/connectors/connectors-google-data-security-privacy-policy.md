---
title: Zasady zabezpieczeń i ochrony danych dla łączników Google
description: Dowiedz się więcej na temat wpływu, jaki zasady usługi Google Security i zasad ochrony prywatności dotyczą łączników Google, takich jak Gmail, w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400761"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Zasady zabezpieczeń i ochrony danych dla łączników Google w Azure Logic Apps

Od **1 maja 2020** zmiany wynikające z [zasad bezpieczeństwa i ochrony danych](https://www.blog.google/technology/safety-security/project-strobe/) firmy Google mogą mieć wpływ na przepływy pracy aplikacji logiki, które korzystają z [łącznika usługi Gmail](/connectors/gmail/). Jeśli aplikacje logiki korzystają z łącznika usługi Gmail przy użyciu konta użytkownika usługi Gmail (adresu e-mail kończącego się na @gmail.com lub @googlemail.com ), Aplikacje logiki mogą używać tylko określonych [wyzwalaczy, akcji i łączników zatwierdzonych przez firmę Google](#approved-connectors).

> [!NOTE]
> Jeśli aplikacje logiki korzystają z łącznika usługi Gmail z kontem biznesowym usługi G (adres e-mail z domeną niestandardową), nie ma to wpływu na aplikacje logiki ani nie mają ograniczeń dotyczących używania łącznika usługi Gmail.

## <a name="affected-logic-apps"></a>Narażone Aplikacje logiki

Jeśli masz aplikacje logiki korzystające z łącznika usługi Gmail, otrzymasz wiadomość e-mail o potencjalnie narażonych aplikacjach logiki. Jednak od **15 czerwca 2020** wszystkie niezgodne przepływy pracy zostaną wyłączone. Można wykonać jedną z następujących czynności:

* Zaktualizuj odpowiednie Aplikacje logiki, [wykonując czynności opisane w tym temacie](#update-affected-workflows). Należy utworzyć aplikację kliencką Google, która udostępnia identyfikator klienta i klucz tajny klienta, które są używane do uwierzytelniania w wyzwalaczu lub akcji usługi Gmail.

* Zaktualizuj aplikacje logiki, których to dotyczy, tak aby korzystały tylko z [łączników zatwierdzonych przez firmę Google](#approved-connectors) przed ponownym włączeniem wyłączonych aplikacji logiki.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Łączniki z systemem Google — zatwierdzone

W ramach tych zasad, jeśli używasz konta użytkownika usługi Gmail, możesz użyć łącznika usługi Gmail z tylko określonymi usługami zatwierdzonymi przez firmę Google, które mogą ulec zmianie. Nasze zespoły inżynieryjne kontynuują pracę z firmą Google, aby dodać do niej więcej usług. Obecnie poniżej przedstawiono wyzwalacze, akcje i łączniki zatwierdzone przez firmę Google, których można użyć w tym samym przepływie pracy aplikacji logiki przy użyciu łącznika usługi Gmail w przypadku korzystania z konta użytkownika usługi Gmail:

* Logic Apps wbudowane wyzwalacze i akcje: dane wsadowe, kontrolki, operacje na danych, Data/godzina, prosty plik, ciecz, żądanie, harmonogram, zmienne i XML

  Wbudowane wyzwalacze i akcje, które nie są zatwierdzone przez firmę Google, takie jak HTTP, Azure Functions, Azure Logic Apps i inne, sprawiają, że aplikacja logiki nie jest zgodna z łącznikiem usługi Gmail, ponieważ aplikacja może wysyłać lub odbierać dane z dowolnego miejsca.

* Usługi Google Services: Gmail, Kalendarz Google, kontakty Google, dysk Google, Arkusze Google i zadania Google

* Zatwierdzone usługi firmy Microsoft: Dynamics 365, Excel Online, Microsoft Teams, Microsoft 365, OneDrive i SharePoint Online

* Łączniki dla źródeł danych zarządzanych przez klienta: FTP, RSS, SFTP, SMTP i SQL Server

## <a name="non-compliant-examples"></a>Niezgodne przykłady

Poniżej przedstawiono kilka przykładów użycia łącznika usługi Gmail z wbudowanymi wyzwalaczami i akcjami lub łącznikami zarządzanymi, które nie są zatwierdzone przez firmę Google:

* Ta aplikacja logiki używa łącznika usługi Gmail z wbudowanym wyzwalaczem protokołu HTTP:

  ![Niezgodna aplikacja logiki — przykład 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  Aplikacja logiki używa również łącznika usługi Google Calendar, który został zatwierdzony.

* Ta aplikacja logiki używa łącznika gmail przy użyciu łącznika usługi Azure Blob Storage:

  ![Niezgodna aplikacja logiki — przykład 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Ta aplikacja logiki używa łącznika usługi Gmail z łącznikiem usługi Twitter:

  ![Niezgodna aplikacja logiki — przykład 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Aby uzyskać najnowsze informacje, zapoznaj się z [dokumentacją techniczną łącznika usługi Gmail](/connectors/gmail/).

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Kroki dla aplikacji logiki, których to dotyczy

Jeśli musisz używać łącznika usługi Gmail z kontem konsumenta usługi Gmail i niezatwierdzonymi łącznikami w aplikacji logiki, możesz utworzyć własną aplikację Google do użytku osobistego lub wewnętrznego w przedsiębiorstwie. W tym scenariuszu poniżej przedstawiono ogólne kroki, które należy wykonać:

1. Utwórz aplikację kliencką Google przy użyciu [konsoli interfejsu API firmy Google](https://console.developers.google.com).

1. W łączniku usługi Gmail Użyj wartości identyfikator klienta i klucz tajny klienta z aplikacji klienckiej Google.

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją techniczną łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

### <a name="create-google-client-app"></a>Tworzenie aplikacji klienckiej Google

Aby skonfigurować projekt dla aplikacji klienckiej, użyj [Kreatora konsoli interfejsu API Google](https://console.developers.google.com/start/api?id=gmail&credential=client_key) i postępuj zgodnie z instrukcjami. Lub, aby uzyskać szczegółowe instrukcje, zapoznaj się z instrukcjami w [dokumentacji Technical Reference łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

Gdy skończysz, Twój ekran będzie wyglądać podobnie do tego przykładu, z tą różnicą, że będziesz mieć własny **Identyfikator klienta** i wartości **klucza tajnego klienta** , których później używasz w aplikacji logiki.

![Identyfikator klienta i klucz tajny klienta aplikacji Google Client](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Używanie ustawień aplikacji klienta w aplikacji logiki

Aby użyć identyfikatora klienta i klucza tajnego klienta z aplikacji klienckiej Google w wyzwalaczu Gmail lub akcji, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Jeśli dodajesz nowy wyzwalacz lub akcję usługi Gmail i utworzysz całkowicie nowe połączenie, przejdź do następnego kroku. W przeciwnym razie w wyzwalaczu lub akcji usługi Gmail wybierz pozycję **Zmień połączenie**  >  **Dodaj nowe**, na przykład:

   ![Wybierz pozycję "Zmień połączenie" > "Dodaj nowe"](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Podaj informacje o połączeniu, na przykład:

   ![Podaj informacje o połączeniu](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Typ uwierzytelniania** | **Przenoszenie własnej aplikacji** | Określa, że do uwierzytelniania będzie używana Twoja aplikacja kliencka. |
   | **Identyfikator klienta** | <*Identyfikator klienta*> | Identyfikator klienta z aplikacji klienckiej Google |
   | **Klucz tajny klienta** | <*Klient-klucz tajny*> | Wpis tajny klienta z aplikacji klienckiej Google |
   ||||

1. Gdy skończysz, wybierz pozycję **Zaloguj**.

   Zostanie wyświetlona strona przedstawiająca utworzoną aplikację kliencką. Jeśli używasz konta użytkownika usługi Gmail, możesz uzyskać stronę, która pokazuje, że aplikacja kliencka nie została zweryfikowana przez firmę Google i wyświetli prośbę o zezwolenie na dostęp do konta Google.

   ![Monituj o dostęp do konta Google](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. W razie potrzeby wybierz pozycję **Zezwalaj**.

   Możesz teraz używać łącznika usługi Gmail bez ograniczeń w aplikacji logiki.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łączniku usługi Gmail](/connectors/gmail/)

