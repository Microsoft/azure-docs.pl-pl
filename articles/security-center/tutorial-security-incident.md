---
title: Samouczek odpowiedzi na zdarzenia — Azure Security Center
description: W tym samouczku dowiesz się, jak Klasyfikacja alerty zabezpieczeń, określić przyczynę główną & zakres zdarzenia i przeszukać dane zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: c508591fe9909578dcc04b0922c0b76691898743
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90901083"
---
# <a name="tutorial-respond-to-security-incidents"></a>Samouczek: Reagowanie na zdarzenia związane z zabezpieczeniami
Usługa Security Center w sposób ciągły analizuje obciążenia chmury hybrydowej i ostrzega o złośliwych działaniach, używając zaawansowanych funkcji analitycznych oraz funkcji analizy zagrożeń. Ponadto można integrować alerty z innymi produktami i usługami zabezpieczeń w usłudze Security Center oraz tworzyć niestandardowe alerty bazujące na własnych wskaźnikach lub źródłach analiz. Po wygenerowaniu alertu potrzebne jest sprawne działanie w celu zbadania i skorygowania jego przyczyny. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Klasyfikacja alertów zabezpieczeń
> * Dalsze badanie w celu ustalenia głównej przyczyny i zakresu zdarzenia zabezpieczeń
> * Wyszukiwanie danych zabezpieczeń pomocnych w badaniu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne
Aby przekroczyć funkcje omówione w tym samouczku, musisz mieć włączoną usługę Azure Defender. Możesz bezpłatnie wypróbować usługę Azure Defender. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Przewodnik Szybki Start [dotyczący rozpoczynania pracy z Security Center](security-center-get-started.md) przeprowadzi Cię przez proces uaktualniania.

## <a name="scenario"></a>Scenariusz
Firma Contoso dokonała ostatnio migracji niektórych lokalnych zasobów na platformę Azure. Dotyczyło to niektórych obciążeń biznesowych na maszynach wirtualnych i baz danych SQL. Obecnie zespół reagowania na zdarzenia zabezpieczeń firmy Contoso (CSIRT, Computer Security Incident Response Team) ma kłopot ze zbadaniem problemów dotyczących zabezpieczeń. Jest to spowodowane brakiem integracji funkcji analizy zabezpieczeń z ich obecnymi narzędziami do reagowania na zdarzenia. Ten brak integracji powoduje problem na etapach wykrywania (za dużo fałszywych alarmów), oceniania i diagnozowania. W ramach tej migracji zespół postanowił skorzystać z usługi Security Center, aby rozwiązać ten problem.

Pierwsza faza migracji zakończyła się po wprowadzeniu wszystkich zasobów i zastosowaniu się do wszystkich zaleceń dotyczących bezpieczeństwa z usłudze Security Center. Zespół Contoso CSIRT stanowi centralny punkt rozwiązywania problemów związanych ze zdarzeniami bezpieczeństwa komputerowego. Zespół składa się z grupy osób odpowiedzialnych za zajmowanie się wszystkimi zdarzeniami zabezpieczeń. Członkowie zespołu mają jasno określone obowiązki obejmujące wszystkie obszary reagowania.

Na potrzeby tego scenariusza skoncentrujemy się na rolach następujących członków zespołu Contoso CSIRT:

![Cykl życia reakcji na zdarzenie](./media/tutorial-security-incident/security-center-incident-response.png)

Magda zajmuje się operacjami zabezpieczeń. Ich obowiązki obejmują:

* Stałe monitorowanie zagrożeń bezpieczeństwa i reagowanie na nie.
* W razie potrzeby eskalację problemu do właściciela obciążeń chmurowych lub analityka zabezpieczeń.

Sam jest analitykiem zabezpieczeń, a ich obowiązki obejmują:

* Badanie ataków.
* Korygowanie działań na podstawie alertów.
* Współpraca z właścicielami obciążeń w celu określania i stosowania rozwiązań.

Jak widać, Magda i Szymon mają różne obowiązki i muszą współpracować, dzieląc się informacjami z usługi Security Center.

## <a name="triage-security-alerts"></a>Klasyfikacja alertów zabezpieczeń
Usługa Security Center zapewnia spójny widok wszystkich alertów zabezpieczeń. Alerty zabezpieczeń są klasyfikowane na podstawie ich ważności i możliwości połączenia powiązanych alertów w zdarzenia zabezpieczeń. Klasyfikując alerty i zdarzenia, wykonaj następujące czynności:

- Odrzuć alerty, w przypadku których nie jest wymagane żadne dodatkowe działanie, na przykład gdy alert jest fałszywie dodatni
- Wykonaj czynności korygujące znane ataki, na przykład blokując ruch sieciowy ze złośliwego adresu IP
- Określ alerty, które wymagają dalszych badań


1. W menu głównym usługi Security Center w sekcji **WYKRYWANIE** wybierz pozycję **Alerty zabezpieczeń**:

   ![Alerty zabezpieczeń](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. Na liście alertów wybierz zdarzenie zabezpieczeń, które jest kolekcją alertów, aby dowiedzieć się więcej o tym zdarzeniu. Zostanie otwarte okno **Wykryto zdarzenie zabezpieczeń**.

   ![Wykryto zdarzenie zabezpieczeń](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. U góry tego ekranu znajduje się opis zdarzenia zabezpieczeń, a poniżej lista alertów będących częścią tego zdarzenia. Kliknij alert, który chcesz zbadać dokładniej, aby uzyskać więcej informacji.

   ![Szczegóły alertu ze zdarzenia](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   Typy alertów mogą być różne. Aby uzyskać więcej szczegółowych informacji na temat typu alertu i możliwych czynności korygujących, przeczytaj [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). W przypadku alertów, które można bezpiecznie odrzucić, możesz kliknąć alert prawym przyciskiem myszy i wybrać pozycję **Odrzuć**:

   ![Alerty](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Jeśli główna przyczyna i zakres złośliwych działań są nieznane, przejdź do następnego kroku, aby zbadać problem dokładniej.

## <a name="investigate-an-alert-or-incident"></a>Badanie alertu lub zdarzenia
1. Na stronie **Alert zabezpieczeń** kliknij przycisk **Rozpocznij badanie** (jeśli badanie jest już rozpoczęte, nazwa przycisku zmienia się na **Kontynuuj badanie**).

   ![Badanie](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   Mapa badania jest graficzną reprezentacją obiektów związanych z tym alertem lub zdarzeniem zabezpieczeń. Kliknięcie obiektu na mapie powoduje, że w informacjach o tym obiekcie są wyświetlane nowe obiekty, a mapy są rozwijane. Właściwości obiektu zaznaczonego na mapie są wyróżnione w okienku po prawej stronie. Informacje dostępne na poszczególnych kartach różnią się w zależności od wybranego obiektu. Podczas procesu badania zapoznaj się ze wszystkimi odpowiednimi informacjami, aby lepiej zrozumieć przenoszenie osoby atakującej.

2. Jeśli potrzebujesz więcej dowodów lub musisz dokładniej zbadać obiekty, które zostały znalezione w trakcie badania, przejdź do następnego kroku.

## <a name="search-data-for-investigation"></a>Wyszukiwanie danych do badania

Aby znaleźć więcej dowodów na naruszenie zabezpieczeń systemów i uzyskać więcej informacji na temat obiektów, które są częścią dochodzenia, możesz skorzystać z funkcji wyszukiwania w usłudze Security Center.

Aby przeprowadzić wyszukiwanie, otwórz pulpit nawigacyjny usługi **Security Center**, kliknij pozycję **Wyszukaj** w lewym okienku nawigacji, wybierz obszar roboczy zawierający obiekty, które chcesz wyszukać, wpisz zapytanie wyszukiwania i kliknij przycisk wyszukiwania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Zachowaj automatyczną obsługę administracyjną i usługę Azure Defender. Jeśli nie planujesz kontynuować ani wyłączyć usługi Azure Defender:

1. Wróć do menu głównego Security Center i wybierz pozycję **Cennik i ustawienia**.
1. Wybierz subskrypcję, którą chcesz obniżyć.
1. Ustaw **usługę Azure Defender** na off.
1. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczną aprowizację:

1. Wróć do menu głównego Security Center i wybierz pozycję **zasady zabezpieczeń**.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. W sekcji **Zasady zabezpieczeń — zbieranie danych** wybierz pozycję **Wyłącz** w obszarze **Dołączanie**, aby wyłączyć automatyczną aprowizację.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono funkcje usługi Security Center, z których należy korzystać, podczas reagowania na zdarzenia zabezpieczeń, takie jak:

> [!div class="checklist"]
> * Zdarzenie zabezpieczeń będące agregacją powiązanych alertów dla zasobu
> * Mapa badania, która jest graficzną reprezentacją obiektów związanych z alertem lub zdarzeniem zabezpieczeń
> * Funkcje wyszukiwania umożliwiające znajdowanie dodatkowych dowodów na naruszenie zabezpieczeń systemów