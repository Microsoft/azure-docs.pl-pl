---
title: Alerty zabezpieczeń w Azure Security Center | Microsoft Docs
description: W tym temacie opisano alerty zabezpieczeń i różne typy dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: memildin
ms.openlocfilehash: 7f5f920f14a556d63f433b36658d7b265d51a28d
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340380"
---
# <a name="security-alerts-in-azure-security-center"></a>Alerty zabezpieczeń w Centrum zabezpieczeń Azure

W Security Center istnieje wiele alertów dla wielu różnych typów zasobów. Security Center generuje alerty dotyczące zasobów wdrożonych na platformie Azure, a także dla zasobów wdrożonych w środowiskach lokalnych i hybrydowych w chmurze.

Alerty zabezpieczeń są wyzwalane przez zaawansowane wykrywania i są dostępne tylko w usłudze Azure Defender. Dostępna jest bezpłatna wersja próbna. Uaktualnienie można przeprowadzić na stronie **ustawień & cenowych** . Dowiedz się więcej o [cenach Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="respond-to-todays-threats"></a>Reagowanie na bieżące zagrożenia <a name="respond-threats"> </a>

W ciągu ostatnich 20 lat miały miejsce istotne zmiany dotyczące charakteru występujących zagrożeń. W przeszłości firmy zwykle musiały martwić się o rozpiętie witryny sieci Web przez pojedynczych osób atakujących, którzy głównie chcą zobaczyć "co można zrobić". Obecnie osoby atakujące są znacznie bardziej zaawansowane i zorganizowane. Często mają określone cele finansowe i strategiczne. Ponadto dysponują większą ilością zasobów, ponieważ mogą być finansowane przez państwa lub zorganizowane grupy przestępcze.

Te zmiany mogą doprowadziły do niewcześniejszego poziomu profesjonalizmu w rangach osoby atakującej. Nie wystarcza im już wpływ na zawartość witryn internetowych. Są one teraz zainteresowane kradzieżą informacji, kont finansowych i danych prywatnych, z których mogą korzystać w celu wygenerowania środków pieniężnych na otwartym rynku lub do korzystania z określonych pozycji gospodarczych, politycznych lub wojskowych. Bardziej niepokojący od osób o motywach finansowych są atakujący, którzy naruszają bezpieczeństwo sieci, aby uszkodzić infrastrukturę lub zaszkodzić ludziom.

W odpowiedzi na takie działania organizacje często wdrażają różne rozwiązania punktowe, które poprzez wyszukiwanie znanych sygnatur ataku koncentrują się na obronie obwodów lub punktów końcowych przedsiębiorstwa. Tego typu rozwiązania zazwyczaj generują duże ilości alertów niskiej jakości, które wymagają dokonania klasyfikacji i przeprowadzenia badań przez analityka zabezpieczeń. Większość organizacji nie dysponuje czasem i niezbędną wiedzą, które umożliwiłyby odpowiednie zareagowanie na te ostrzeżenia — wiele z nich nie jest rozwiązywanych.  

Ponadto osoby atakujące rozwinęły swoje metody w celu podwyższenia liczby zabezpieczeń opartych na sygnaturach i [adaptacji do środowisk chmurowych](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Wymagane jest nowe podejście do problemu zabezpieczeń, które pozwoliłoby na szybszą identyfikację potencjalnych zagrożeń, a także przyspieszone wykrywanie ich i reagowanie na nie.

## <a name="what-are-security-alerts-and-security-incidents"></a>Co to są alerty zabezpieczeń i zdarzenia związane z bezpieczeństwem? 

**Alerty** są powiadomieniami generowanymi przez Security Center w przypadku wykrycia zagrożeń dotyczących zasobów. Security Center priorytetów i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Usługa Security Center udostępnia również zalecenia dotyczące sposobu korygowania ataku.

**Zdarzenie zabezpieczeń** to kolekcja powiązanych alertów, a nie każdego pojedynczego alertu. Security Center używa [korelacji alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md) w celu skorelowania różnych alertów i niskiej dokładności sygnałów do zdarzeń zabezpieczeń.

Korzystając z incydentów, Security Center zapewnia jeden widok kampanii ataku i wszystkich powiązanych alertów. Ten widok umożliwia szybkie zrozumienie działań podejmowanych przez osobę atakującą oraz zasobów, których dotyczy. Aby uzyskać więcej informacji, zobacz [korelacja alertów inteligentnych w chmurze](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Jak Security Center wykrywać zagrożenia? <a name="detect-threats"> </a>

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Ze względu na naszą globalną obecność w chmurze i lokalnie mamy dostęp do zestawu danych telemetrycznych rozległych. Szeroka i różnorodna kolekcja zestawów danych pozwala nam odkrywać nowe wzorce ataków i trendy w ramach naszych lokalnych produktów konsumenckich i korporacyjnych, a także naszych Usługi online. W związku z tym usługa Security Center może szybko zaktualizować swoje algorytmy wykrywania, w miarę jak atakujący wprowadzają nowe i coraz bardziej zaawansowane metody. Takie podejście pomaga sprostać wymaganiom szybko zmieniającego się środowiska zagrożenia.

Aby wykrywać rzeczywiste zagrożenia i zmniejszyć liczbę fałszywych wyników, Security Center gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure i sieci. Współpracuje również z rozwiązaniami partnerskimi, takimi jak zapora i rozwiązania programu Endpoint Protection. Security Center analizuje te informacje, często skorelowane informacje z wielu źródeł, aby identyfikować zagrożenia.

![Prezentacja i zbieranie danych przez usługę Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Przełomowe rozwiązania dotyczące danych big data i technologii [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) są używane do oceny zdarzeń zachodzących w całej sieci szkieletowej chmury. Wykrywane są zagrożenia, które byłyby niemożliwe do wykrycia przy użyciu ręcznych metod, i przewidywany jest kierunek ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

* **Zintegrowana analiza zagrożeń**: Firma Microsoft ma ogromnąą ilość globalnej analizy zagrożeń. Dane telemetryczne są przepływane z wielu źródeł, takich jak Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Zbrodnis Unit (DCU) i Microsoft Security Response Center (MSRC). Badacze otrzymują również informacje o analizie zagrożeń, które są współużytkowane przez głównych dostawców usług w chmurze i źródła danych od innych stron trzecich. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów.

* **Analiza zachowań**: Analiza behawioralna to technika, która analizuje i porównuje dane z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych. Są one również określane przez specjalistów od analizy, którzy dokonują dokładnej analizy złośliwych zachowań. Azure Security Center może korzystać z analizy behawioralnej w celu identyfikowania zasobów narażonych na podstawie analizy dzienników maszyn wirtualnych, dzienników urządzeń sieci wirtualnej, dzienników sieci szkieletowej i innych źródeł.

* **Wykrywanie anomalii**: Azure Security Center używa także wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców pochodzących z dużych zestawów danych), wykrywanie anomalii jest bardziej "spersonalizowane" i koncentruje się na liniach bazowych, które są specyficzne dla wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł definiujących odstające warunki, które mogą reprezentować zdarzenie związane z zabezpieczeniami.

## <a name="how-are-alerts-classified"></a>Jak są klasyfikowane alerty?

Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu przejść do niego. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

> [!NOTE]
> Ważność alertu jest wyświetlana w różny sposób w portalu i wersjach interfejsu API REST, który jest przedstawiony jako 01-01-2019. Jeśli używasz starszej wersji interfejsu API, Uaktualnij środowisko pod kątem spójnego środowiska opisanego poniżej.

- **Wysoka:** Istnieje duże prawdopodobieństwo naruszenia bezpieczeństwa zasobu. Należy od razu przyjrzeć się do niego. Security Center ma wysoki poziom zaufania zarówno w złośliwym zamiarach, jak i w ustaleniach używanych do wystawiania alertu. Na przykład alert, który wykrywa wykonywanie znanego złośliwego narzędzia, takiego jak program mimikatz, typowego narzędzia używanego do kradzieży poświadczeń.
- **Średni:** Prawdopodobnie podejrzane działanie może wskazywać na naruszenie bezpieczeństwa zasobu.
Stopień zaufania Security Center w analitycznym lub wyszukiwaniu jest średni, a wiarygodność złośliwego celu jest wysoka. Zazwyczaj mogą to być Uczenie maszynowe lub wykryte anomalie. Na przykład próba logowania z nietypowej lokalizacji.
- **Niska:** Może to być niegroźne pozytywne lub zablokowany atak.
   * Security Center nie ma wystarczającej pewności, że zamiar jest złośliwy, a działanie może być nieszkodliwe. Na przykład dziennik czyszczenie jest akcją, która może wystąpić, gdy osoba atakująca próbuje ukryć swoje ścieżki, ale w wielu przypadkach jest to procedura wykonywana przez administratorów.
   * Security Center zazwyczaj nie informuje użytkownika o zablokowaniu ataków, chyba że jest to interesujący przypadek, który sugerujemy. 
- **Informacje:** Podczas przechodzenia do szczegółów zdarzenia związanego z bezpieczeństwem będą wyświetlane tylko alerty informacyjne lub użycie interfejsu API REST z określonym IDENTYFIKATORem alertu. Zdarzenie zwykle składa się z wielu alertów, które mogą być wyświetlane na własne potrzeby tylko w celu uzyskania informacji, ale w kontekście innych alertów może być zaufanego bliższego wyglądu. 

## <a name="continuous-monitoring-and-assessments"></a>Ciągłe monitorowanie i oceny

Azure Security Center korzyści ze stosowania przez firmę Microsoft zespołów badawczych i naukowych dotyczących zabezpieczeń, którzy stale monitorują zmiany w poziomie zagrożeń. Obejmuje to następujące inicjatywy:

* **Monitorowanie analizy zagrożeń**: analiza zagrożeń obejmuje mechanizmy, wskaźniki, implikacje i porady umożliwiające podejmowanie działań dotyczących istniejących lub powstających zagrożeń. Te informacje są udostępniane w branży zabezpieczeń, a firma Microsoft stale monitoruje zagrożenia płynące z wewnętrznych i zewnętrznych źródeł.
* **Udostępnianie sygnałów**: szczegółowe informacje z zespołów ds. zabezpieczeń w szerokim portfelu usług w chmurze i lokalnych usługach, serwerach i klientach w firmie Microsoft są udostępniane i analizowane.
* **Specjaliści ds. zabezpieczeń firmy Microsoft**: ciągła współpraca zespołów firmy Microsoft w zakresie wyspecjalizowanych zabezpieczeń, takich jak analiza śledcza i wykrywanie ataków w sieci Web.
* **Dostrajanie wykrywania zagrożeń**: algorytmy są uruchamiane na rzeczywistych zestawach danych klienta, a pracownicy naukowo-badawczy z zakresu zabezpieczeń pracują z klientami w celu weryfikacji otrzymanych wyników. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Te połączone wysiłki skutkują w nowych i ulepszonych wykryciuch, z których możesz korzystać natychmiast — nie ma żadnych działań do wykonania.

## <a name="export-alerts"></a>Eksportowanie alertów

Masz szereg opcji wyświetlania alertów poza Security Center, w tym:

- **Pobierz raport CSV** na pulpicie nawigacyjnym alertów umożliwia jednorazowe Eksportowanie do pliku CSV.
- **Eksport ciągły** z ustawień & cenowych umożliwia skonfigurowanie strumieni alertów zabezpieczeń i zaleceń dotyczących log Analytics obszarów roboczych i Event Hubs. [Dowiedz się więcej na temat eksportu ciągłego](continuous-export.md)
- **Łącznik wskaźnikowy platformy Azure** przesyła strumieniowo alerty usługi Azure Defender z Azure Security Center do platformy Azure. [Dowiedz się więcej na temat łączenia Azure Security Center ze wskaźnikiem kontrolnym](../sentinel/connect-azure-security-center.md)


## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o różnych typach alertów dostępnych w Security Center. Aby uzyskać więcej informacji, zobacz:

- **Alerty zabezpieczeń w dzienniku aktywności platformy Azure** — oprócz dostępnych w Azure Portal lub programowo, alerty zabezpieczeń i zdarzenia są poddawane inspekcji jako zdarzenia w [dzienniku aktywności platformy Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log). Aby uzyskać więcej informacji o schemacie zdarzeń, zobacz [alerty zabezpieczeń w dzienniku aktywności platformy Azure](https://go.microsoft.com/fwlink/?linkid=2114113)