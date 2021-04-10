---
title: Usługa wykrywania anomalii dla rozliczeń naliczanych — Microsoft Azure Marketplace
description: Opisuje sposób działania wykrywania anomalii, gdy powiadomienia są wysyłane i co należy zrobić z nimi, i opcje pomocy technicznej.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: ac3e07c67ca82c2960de1c4341a714b33751bfc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092407"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Usługa wykrywania anomalii dla rozliczeń taryfowych

[Usługa pomiarów w portalu Marketplace](marketplace-metering-service-apis-faq.md) umożliwia tworzenie ofert w komercyjnym programie Marketplace, który jest naliczany zgodnie z jednostkami niestandardowymi. Korzystając z naliczanych rozliczeń, możesz wysyłać do firmy Microsoft zdarzenia użycia dotyczące użycia klienta i przygotować rozliczenia na podstawie tego użycia.

Nieprawidłowe dane użycia mogą pochodzić z różnych przyczyn, takich jak błędy, błędne konfiguracje w śledzeniu użycia lub oszustwa. Nieprawidłowe dane dotyczące użycia spowodują nieprawidłowe opłaty dla klientów i kwestie dotyczące rozliczeń.

Aby zmniejszyć ryzyko, Nasza usługa wykrywania anomalii stosuje algorytmy uczenia maszynowego w celu określenia normalnych zachowań dotyczących rozliczeń, przeanalizowania użycia rozliczeń i wykrycia anomalii z minimalną interwencją użytkownika.

Zostanie wyświetlone powiadomienie o wykryciu anomalii w wykorzystaniu naliczanych opłat. Pozwala to na badanie i powiadamianie nas o potwierdzeniu nietypowego problemu, w którym można podjąć działania podejmowane w celu rozwiązania problemu z rozliczeniami klientów.

Oprócz nagłych skoków, wartości DIP i trendów związanych z naliczaniem obciążeń, nasz model również konta dla efektów sezonowych. Ze względu na to, że opłaty mierzone są przekazywane za pośrednictwem danych nadwyżkowych, nasz model jest również w stanie bezpiecznie obsługiwać długie okresy brakujących danych.

Poniżej przedstawiono przykłady wyników wykrywania anomalii. Oczekiwany zakres jest wyświetlany jako żółty pasek. Użycie mierzonych opłat naliczanych jest widoczne jako Zielona gwiazdka w paśmie. Użycie rozliczeń poza pasmem jest wyświetlane jako czerwona kropka.  

Anomalie wykryte poza przewidywalną tendencją:

![Przedstawia anomalie wykryte poza przewidywalną tendencją.](media/anomaly-1.png)

Wykryto anomalie poza cyklicznym trendem cyklicznym:

![Przedstawia anomalie wykryte poza cyklicznym trendem cyklicznym.](media/anomaly-2.png)

Anomalie wykryte w trendach w górę:

![Przedstawia anomalie wykryte w trendach w górę.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Jak działa usługa wykrywania anomalii

Wykrywanie anomalii jest włączane automatycznie dla wszystkich naliczanych obciążeń. Po przesłaniu zdarzeń użycia do firmy Microsoft usługa wykrywania anomalii tworzy model oczekiwanych wartości na podstawie przeszłych danych użycia. Ten model działa co tydzień.

Funkcje wykrywania anomalii na poziomie poszczególnych liczników i poszczególnych klientów. Oznacza to, że każdy licznik każdego klienta będzie miał model przeszkolony w oparciu o poprzedni wzorzec użycia tego klienta.

Model działa przez wygenerowanie interwałów zaufania z mocą wsteczną. Prognoza szeregów czasowych to Uogólniony model, składający się z części przewidywania trendu i części sezonowości. Ponieważ model jest formułowany jako zadanie regresji, może bezpiecznie obsłużyć długie okresy brakujących danych. Jeśli obserwacja wykracza poza przewidywane interwały zaufania, oznacza to, że obserwacje nie mogą być wyjaśnione w oparciu o historyczne wzorce naliczania opłat i w związku z tym może być anomalią.

## <a name="anomaly-detection-notification"></a>Powiadomienie o wykryciu anomalii

Można oszacować, zarządzać i potwierdzać anomalie w centrum partnerskim. Aby dowiedzieć się, jak to zrobić, zobacz [Zarządzanie anomaliami rozliczeń naliczanych w centrum partnerskim](../anomaly-detection.md).

Aby zapewnić, że klienci nie są obciążeni opłatami za użycie taryfowe, należy sprawdzić, czy wykryte anomalie są prawdziwymi problemami. W takim przypadku można potwierdzić nieprawidłowe użycie w centrum partnerskim.

Zalecamy potwierdzenie, czy wykryte anomalie są normalnym użyciem. Wykonanie tej czynności poprawi dane anomalii, które zapewniamy. Jeśli anomalia reprezentuje potencjalnie wysokie ryzyko finansowe, firma Microsoft może skontaktować się z Tobą w celu potwierdzenia użycia.

## <a name="when-and-how-to-get-support"></a>Kiedy i jak uzyskać pomoc techniczną

Jeśli wysłałeś nam nieprawidłowe użycie taryfowe lub nastąpi naliczenie opłat dla klienta, nie zainicjujemy rachunku dla klienta w ramach zgłoszonego użycia lub płacisz za to użycie. Twoim obowiązkiem będzie wyrównanie straty przychodu powstałej z powodu zaniżenia raportowanych wartości.

W przypadku zastosowania jednego z następujących przypadków można dostosować kwotę użycia w centrum partnerskim, co spowoduje korektę zwrotną lub rozliczenia dla klientów:

- Potwierdzono, że jedna ze znalezionych anomalii jest rzeczywistym problemem i nieprawidłowe użycie spowoduje Przeciążenie klienta.
- Użytkownik stwierdzi, że do nas wysłano nieprawidłowe użycie, a niewłaściwe użycie spowoduje Przeciążenie klienta.

Aby przesłać bilet pomocy technicznej dotyczącej anomalii rozliczeń naliczanych:

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) przy użyciu swojego konta służbowego.
1. W menu w prawym górnym rogu strony wybierz ikonę **Pomoc techniczna** . Okienko **Pomoc i obsługa techniczna** pojawia się po prawej stronie strony.
1. Aby uzyskać pomoc dotyczącą komercyjnej witryny Marketplace, wybierz pozycję **Marketing komercyjny**.
   ![Ilustruje okienko pomoc techniczna.](../media/support/commercial-marketplace-support-pane.png)
1. W polu **Podsumowanie problemu** wprowadź **komercyjne rozliczenia > Marketplace**.
1. W polu **typ problemu** wybierz jedną z następujących opcji:
    - **Komercyjne >owe rozliczanie w witrynie Marketplace > błędne użycie wysłane dla oferty aplikacji platformy Azure**
    - **Komercyjne >owe rozliczanie w witrynie Marketplace > nieprawidłowe użycie wysłane dla oferty SaaS**
1. W **następnym kroku** wybierz pozycję **Przejrzyj rozwiązania**.
1. Przejrzyj zalecane dokumenty, jeśli istnieją lub wybierz opcję **Podaj szczegóły problemu** , aby przesłać bilet pomocy technicznej.

Aby uzyskać więcej informacji na temat opcji pomocy technicznej wydawcy, zobacz [Pomoc techniczna dla komercyjnego programu w witrynie Marketplace w centrum partnerskim](../support.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsie API usługi pomiaru Marketplace](marketplace-metering-service-apis.md).
- [Zarządzanie anomaliami rozliczeń naliczanych w centrum partnerskim](../anomaly-detection.md)
