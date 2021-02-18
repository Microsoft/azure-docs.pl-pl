---
title: Wykrywanie anomalii dla rozliczeń naliczanych | Portal Azure Marketplace
description: Dowiedz się, jak automatyczne wykrywanie anomalii w rozliczeniach taryfowych pomaga zapewnić, że klienci są rozliczani prawidłowo w przypadku mierzonych użycia oferty komercyjnej witryny Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/17/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 7f1bcac49ccb12ea1e0517aed5a139e549451ec0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653010"
---
# <a name="anomaly-detection-for-metered-billing"></a>Wykrywanie anomalii dla rozliczeń naliczanych

Opcja Niestandardowa opłata naliczana jest obecnie dostępna dla usług SaaS ( [Software as a Service](plan-saas-offer.md) ) i [aplikacji platformy Azure](plan-azure-application-offer.md#types-of-plans) z zarządzanym planem aplikacji.

Jeśli używasz opcji rozliczania naliczanych w przypadku tworzenia ofert w komercyjnym programie Marketplace, który umożliwia naliczanie opłat za użycie w oparciu o jednostki niestandardowe, musisz wiedzieć, kiedy Klient użył więcej usług niż oczekiwano.

## <a name="use-the-anomaly-detection-feature"></a>Korzystanie z funkcji wykrywania anomalii

Firma Microsoft korzysta z naszych partnerów, aby zgłosić użycie nadwyżkowe przez klientów usług SaaS lub aplikacji zarządzanych platformy Azure przed fakturą firmy Microsoft. Jeśli zgłoszono nieprawidłowe użycie, klient może potencjalnie otrzymać niepoprawną fakturę, a jednocześnie uzyskać wiarygodność firmy Microsoft i jej partnera.

Aby zapewnić, że klienci są rozliczani prawidłowo, użyj funkcji **wykrywania anomalii** zarówno dla aplikacji SaaS, jak i planów aplikacji zarządzanych przez aplikacje platformy Azure. Ta funkcja służy do monitorowania użycia w ramach naliczanych opłat i przewidywania oczekiwanej wartości użycia w oczekiwanym zakresie. Jeśli użycie wykracza poza oczekiwany zakres, jest traktowany jako nieoczekiwany (anomalia), a użytkownik otrzyma powiadomienie o alercie na stronie przeglądu oferty w komercyjnym programie partnerskim usługi Partner Center. Możesz śledzić dzienne użycie klientów dla wszystkich ustawionych niestandardowych wymiarów miernika.

## <a name="view-and-manage-metered-usage-anomalies"></a>Wyświetlaj anomalie użycia taryfowego i zarządzaj nimi

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
1. W menu nawigacji po lewej stronie wybierz pozycję **komercyjna**  >  **Analiza** Marketplace.
1. Wybierz kartę **anomalie użycia taryfowego** .

    [![Ilustruje kartę anomalie użycia mierzony na stronie użycie.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Rysunek 1. karta anomalie użycia taryfowego***

1. W przypadku wszystkich anomalii użycia wykrytych w przypadku rozliczeń naliczanych jako wydawca zostanie wyświetlony monit o zbadanie i potwierdzenie, czy anomalia ma wartość true, czy nie. Wybierz pozycję **Oznacz jako anomalia** , aby potwierdzić diagnostykę.

     [![Ilustruje okno dialogowe Oznacz jako anomalie.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Rysunek 2. Oznaczanie jako nietypowe okno dialogowe***

1. Jeśli uważasz, że wykryta anomalie użycia nadwyżkowe nie jest oryginalna, możesz podać tę opinię, wybierając **nieanomalię** dla Centrum partnerskiego oflagowanego nietypowego użycia.

    [![Ilustruje dlaczego nie jest to okno dialogowe anomalie.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Rysunek 3. Dlaczego nie jest to anomalia? okno dialogowe***

1. Przewiń stronę w dół, aby wyświetlić listę spisu niepotwierdzonych anomalii. Lista zawiera spis anomalii, które nie zostały potwierdzone. Możesz oznaczyć dowolne nietypowe anomalie Centrum partnerskiego jako autentyczne lub fałszywe.

   [![Przedstawia listę niepotwierdzonych anomalii Centrum partnerskiego na stronie użycie.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Rysunek 4. Lista niepotwierdzonych anomalii Centrum partnerskiego***

1. Zobaczysz również dziennik akcji anomalii pokazujący akcje podjęte w przypadku użycia nadwyżkowe. W dzienniku akcji będzie można zobaczyć, które zdarzenia użycia nadwyżkowe zostały oznaczone jako prawdziwe lub fałszywe.

   [ ![ Ilustruje dziennik akcji anomalii na stronie użycie.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Rysunek 5. Dziennik akcji anomalii***

1. Analiza w centrum partnerskim nie będzie obsługiwać zdarzeń użycia nadwyżkowe w raportach eksportu. Centrum partnerskie pozwala wprowadzić skorygowane użycie nadwyżkowe w przypadku anomalii, a szczegółowe informacje są przesyłane do zespołów Microsoft Teams w celu zbadania tego problemu. Zgodnie z badaniem firma Microsoft wystawia opłaty za środki na korzystanie z naliczanego klienta w razie potrzeby. W przypadku wybrania dowolnego z oflagowanych anomalii można wybrać opcję **Oznacz jako anomalia** , aby oznaczyć użycie nadwyżkowe w charakterze oryginalności.

   [ ![ Ilustruje oznaczenie jako okno dialogowe anomalie.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Rysunek: 6. Oznacz jako nietypowe okno dialogowe***

Gdy użycie nadwyżkowe jest oflagowane jako nieregularne w centrum partnerskim, zostanie wyświetlone okno z 30-dniowym wystąpieniem do oznaczenia anomalii jako autentyczne lub fałszywe. Po upływie 30-dniowego okresu, ponieważ Wydawca nie będzie mógł działać na anomaliach.

> [!IMPORTANT]
> W obszarze zgłoszone jednostki użycia nadwyżkowe nie kwalifikują się do przeprowadzenia ponownego stanu i korekty finansowej.

W wybranym okresie obliczeń można zobaczyć wszystkie anomalie (potwierdzone lub w inny sposób). Różne okresy obliczeniowe to ostatnie 30 dni, ostatnie 60 dni i ostatnie 90 dni.

> [!IMPORTANT]
> Użytkownik prosi o działanie na oflagowanych anomaliach w ciągu 30 dni od momentu pierwszego zgłoszenia anomalii w centrum partnerskim.

Modalny filtr w widżecie służy do wybierania pojedynczych ofert i indywidualnych liczników niestandardowych.

Po oznaczeniu użycia nadwyżkowego jako anomalii lub potwierdzeniu modelu, który oznaczył anomalię jako oryginalna, nie można zmienić wyboru na "nieanomalia".

> [!IMPORTANT]
> Użycie nadwyżkowe można przesłać ponownie w przypadku sytuacji związanych z naliczeniem opłat.

## <a name="see-also"></a>Zobacz też
- [Naliczanie opłat za SaaS przy użyciu komercyjnej usługi pomiarowej Marketplace](./partner-center-portal/saas-metered-billing.md)
- [Rozliczenia taryfowe zarządzanej aplikacji](./partner-center-portal/azure-app-metered-billing.md)
