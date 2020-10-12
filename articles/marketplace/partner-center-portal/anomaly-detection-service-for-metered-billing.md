---
title: Usługa wykrywania anomalii dla rozliczeń naliczanych — Microsoft Azure Marketplace
description: Opisuje sposób działania wykrywania anomalii, gdy powiadomienia są wysyłane i co należy zrobić z nimi, i opcje pomocy technicznej.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: cb134c82f231eb8b6329b1acafb181032edd6936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320089"
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

Powiadomienia o wykryciu anomalii są wysyłane co tydzień. Obejmuje ona wszystkie anomalie wykryte przez tydzień dla wszystkich liczników i klientów. Ta wiadomość e-mail jest wysyłana do kontaktów **inżynieryjnych** i **pomocy technicznej** dostarczonych podczas tworzenia oferty.

Oczekujesz, że wykryte anomalie są rzeczywistymi problemami, a jeśli tak, skontaktuj się z firmą Microsoft, aby zgłosić nieprawidłowe użycie (zobacz sekcję dotyczącą pomocy technicznej poniżej).

Jeśli użytkownik potwierdzi, że wykryte anomalie są normalnym użyciem, nie jest wymagana żadna dodatkowa akcja. Jeśli jednak anomalia reprezentuje potencjalnie wysokie ryzyko finansowe, firma Microsoft może skontaktować się z Tobą w celu potwierdzenia użycia.  

## <a name="when-and-how-to-get-support"></a>Kiedy i jak uzyskać pomoc techniczną

Jeśli w firmie Microsoft zostało wysłane nieprawidłowe użycie, a to nastąpi naliczenie opłaty od klienta, firma Microsoft nie będzie inicjować rachunku dla klienta w ramach zgłoszonego użycia lub uiszczania tego użycia. Twoim obowiązkiem będzie wyrównanie straty przychodu powstałej z powodu zaniżenia raportowanych wartości.

Jeśli jeden z następujących przypadków ma zastosowanie, możesz otworzyć bilet pomocy technicznej w celu zażądania zwrotu lub korekty rozliczeń dla klientów:

- Potwierdzono, że jedna ze znalezionych anomalii jest rzeczywistym problemem i nieprawidłowe użycie spowoduje **Przeciążenie** klienta.
- Użytkownik stwierdzi, że do nas wysłano nieprawidłowe użycie, a niewłaściwe użycie spowoduje **Przeciążenie** klienta.
- Należy poprosić o zwrot opłaty za użycie naliczanych opłat przez klienta.

Aby przesłać bilet:

1. Przejdź do strony pomocy technicznej. W **polu powiedz nam o Twoim problemie**wprowadź wartość "Nieprawidłowe użycie".
2. W obszarze Tematy pomocy technicznej na liście rozwijanej wyniki wyszukiwania wybierz jedną z następujących opcji:
    - **Komercyjne witryny Marketplace**  >  **Naliczanie opłat**  >  **Wysłano nieprawidłowe użycie dla oferty aplikacji platformy Azure**lub
    - **Komercyjne witryny Marketplace**  >  **Naliczanie opłat**  >  **Wysłano nieprawidłowe użycie dla oferty SaaS**
3. W **następnym kroku**wybierz przycisk **Przejrzyj rozwiązania** , aby skierować do Centrum partnerskiego, aby przesłać bilet pomocy technicznej.

Aby uzyskać więcej informacji na temat opcji pomocy technicznej wydawcy, zobacz [Pomoc techniczna dla komercyjnego programu w witrynie Marketplace w centrum partnerskim](support.md).

## <a name="next-step"></a>Następny krok

- Dowiedz się więcej o [interfejsie API usługi pomiaru Marketplace](marketplace-metering-service-apis.md).
