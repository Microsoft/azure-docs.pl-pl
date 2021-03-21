---
title: Alerty i zdarzenia zabezpieczeń w Azure Security Center
description: Dowiedz się, jak Azure Security Center generuje alerty zabezpieczeń i skorelowanie ich w zdarzenia.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: memildin
ms.openlocfilehash: 5824457a9f2ce411a871b5e76802ecf2e2f106c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099509"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Alerty i zdarzenia zabezpieczeń w Azure Security Center

Security Center generuje alerty dotyczące zasobów wdrożonych na platformie Azure, w środowiskach lokalnych i w chmurze hybrydowej.

Alerty zabezpieczeń są wyzwalane przez zaawansowane wykrywania i są dostępne tylko w usłudze Azure Defender. Możesz przeprowadzić uaktualnienie ze strony **ustawień & cenowych** , zgodnie z opisem w [przewodniku szybki start: Włączanie usługi Azure Defender](enable-azure-defender.md). Dostępna jest bezpłatna 30-dniowa wersja próbna. Aby uzyskać szczegółowe informacje o cenach w wybranej walucie i według regionu, zobacz [Security Center Cennik](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Co to są alerty zabezpieczeń i zdarzenia związane z bezpieczeństwem? 

**Alerty** są powiadomieniami generowanymi przez Security Center w przypadku wykrycia zagrożeń dotyczących zasobów. Security Center priorytetów i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Usługa Security Center udostępnia również zalecenia dotyczące sposobu korygowania ataku.

**Zdarzenie zabezpieczeń** to kolekcja powiązanych alertów, a nie każdego pojedynczego alertu. Security Center używa [korelacji alertów inteligentnych w chmurze](#cloud-smart-alert-correlation-in-azure-security-center-incidents) w celu skorelowania różnych alertów i niskiej dokładności sygnałów do zdarzeń zabezpieczeń.

Korzystając z incydentów, Security Center zapewnia jeden widok kampanii ataku i wszystkich powiązanych alertów. Ten widok umożliwia szybkie zrozumienie działań podejmowanych przez osobę atakującą oraz zasobów, których dotyczy.

## <a name="respond-to-todays-threats"></a>Reagowanie na bieżące zagrożenia

W ciągu ostatnich 20 lat miały miejsce istotne zmiany dotyczące charakteru występujących zagrożeń. W przeszłości firmy zwykle musiały martwić się o rozpiętie witryny sieci Web przez pojedynczych osób atakujących, którzy głównie chcą zobaczyć "co można zrobić". Obecnie osoby atakujące są znacznie bardziej zaawansowane i zorganizowane. Często mają określone cele finansowe i strategiczne. Posiadają również więcej zasobów dostępnych dla nich, ponieważ mogą one być finansowane według państw lub zorganizowanej przestępczości.

Te zmiany mogą doprowadziły do niewcześniejszego poziomu profesjonalizmu w rangach osoby atakującej. Nie wystarcza im już wpływ na zawartość witryn internetowych. Są one teraz zainteresowane kradzieżą informacji, kont finansowych i danych prywatnych, z których mogą korzystać w celu wygenerowania środków pieniężnych na otwartym rynku lub do korzystania z określonych pozycji gospodarczych, politycznych lub wojskowych. Bardziej niepokojący od osób o motywach finansowych są atakujący, którzy naruszają bezpieczeństwo sieci, aby uszkodzić infrastrukturę lub zaszkodzić ludziom.

W odpowiedzi na takie działania organizacje często wdrażają różne rozwiązania punktowe, które poprzez wyszukiwanie znanych sygnatur ataku koncentrują się na obronie obwodów lub punktów końcowych przedsiębiorstwa. Tego typu rozwiązania zazwyczaj generują duże ilości alertów niskiej jakości, które wymagają dokonania klasyfikacji i przeprowadzenia badań przez analityka zabezpieczeń. Większość organizacji nie dysponuje czasem i niezbędną wiedzą, które umożliwiłyby odpowiednie zareagowanie na te ostrzeżenia — wiele z nich nie jest rozwiązywanych.  

Ponadto osoby atakujące rozwinęły swoje metody w celu podwyższenia liczby zabezpieczeń opartych na sygnaturach i [adaptacji do środowisk chmurowych](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Wymagane jest nowe podejście do problemu zabezpieczeń, które pozwoliłoby na szybszą identyfikację potencjalnych zagrożeń, a także przyspieszone wykrywanie ich i reagowanie na nie.


## <a name="continuous-monitoring-and-assessments"></a>Ciągłe monitorowanie i oceny

Azure Security Center korzyści ze stosowania przez firmę Microsoft zespołów badawczych i naukowych dotyczących zabezpieczeń, którzy stale monitorują zmiany w poziomie zagrożeń. Obejmuje to następujące inicjatywy:

* **Monitorowanie analizy zagrożeń**: analiza zagrożeń obejmuje mechanizmy, wskaźniki, implikacje i porady umożliwiające podejmowanie działań dotyczących istniejących lub powstających zagrożeń. Te informacje są udostępniane w branży zabezpieczeń, a firma Microsoft stale monitoruje zagrożenia płynące z wewnętrznych i zewnętrznych źródeł.
* **Udostępnianie sygnałów**: szczegółowe informacje z zespołów ds. zabezpieczeń w szerokim portfelu usług w chmurze i lokalnych usługach, serwerach i klientach w firmie Microsoft są udostępniane i analizowane.
* **Specjaliści ds. zabezpieczeń firmy Microsoft**: ciągła współpraca zespołów firmy Microsoft w zakresie wyspecjalizowanych zabezpieczeń, takich jak analiza śledcza i wykrywanie ataków w sieci Web.
* **Dostrajanie wykrywania zagrożeń**: algorytmy są uruchamiane na rzeczywistych zestawach danych klienta, a pracownicy naukowo-badawczy z zakresu zabezpieczeń pracują z klientami w celu weryfikacji otrzymanych wyników. Wyniki prawdziwie i fałszywie dodatnie są używane w celu udoskonalania algorytmów uczenia maszynowego.

Te połączone wysiłki skutkują w nowych i ulepszonych wykryciuch, z których możesz korzystać natychmiast — nie ma żadnych działań do wykonania.

## <a name="how-does-security-center-detect-threats"></a>Jak Security Center wykrywać zagrożenia? <a name="detect-threats"> </a>

Pracownicy naukowo-badawczy firmy Microsoft stale poszukują nowych zagrożeń. Ze względu na naszą globalną obecność w chmurze i lokalnie mamy dostęp do zestawu danych telemetrycznych rozległych. Szeroka i różnorodna kolekcja zestawów danych pozwala nam odkrywać nowe wzorce ataków i trendy w ramach naszych lokalnych produktów konsumenckich i korporacyjnych, a także naszych Usługi online. W związku z tym usługa Security Center może szybko zaktualizować swoje algorytmy wykrywania, w miarę jak atakujący wprowadzają nowe i coraz bardziej zaawansowane metody. Takie podejście pomaga sprostać wymaganiom szybko zmieniającego się środowiska zagrożenia.

Aby wykrywać rzeczywiste zagrożenia i zmniejszyć liczbę fałszywych wyników, Security Center gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure i sieci. Współpracuje również z rozwiązaniami partnerskimi, takimi jak zapora i rozwiązania programu Endpoint Protection. Security Center analizuje te informacje, często skorelowane informacje z wielu źródeł, aby identyfikować zagrożenia.

![Prezentacja i zbieranie danych przez usługę Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Usługa Security Center wykorzystuje zaawansowane narzędzia analizy zabezpieczeń, które wykraczają daleko poza metody bazujące na sygnaturze. Przełomowe rozwiązania dotyczące danych big data i technologii [uczenia maszynowego](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) są używane do oceny zdarzeń zachodzących w całej sieci szkieletowej chmury. Wykrywane są zagrożenia, które byłyby niemożliwe do wykrycia przy użyciu ręcznych metod, i przewidywany jest kierunek ewolucji ataków. Do narzędzi analizy zabezpieczeń należą:

- **Zintegrowana analiza zagrożeń**: Firma Microsoft ma ogromnąą ilość globalnej analizy zagrożeń. Dane telemetryczne są przepływane z wielu źródeł, takich jak Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Zbrodnis Unit (DCU) i Microsoft Security Response Center (MSRC). Badacze otrzymują również informacje o analizie zagrożeń, które są współużytkowane przez głównych dostawców usług w chmurze i źródła danych od innych stron trzecich. Usługa Azure Security Center może użyć tych informacji, aby ostrzec użytkownika przed zagrożeniami ze strony znanych złośliwych podmiotów.

- **Analiza zachowań**: Analiza behawioralna to technika, która analizuje i porównuje dane z kolekcją znanych wzorców. Wzorce te nie są jednak prostymi sygnaturami. Określa się je za pośrednictwem złożonych algorytmów uczenia maszynowego, które są stosowane w przypadku wielkich zestawów danych. Są one również określane przez specjalistów od analizy, którzy dokonują dokładnej analizy złośliwych zachowań. Azure Security Center może korzystać z analizy behawioralnej w celu identyfikowania zasobów narażonych na podstawie analizy dzienników maszyn wirtualnych, dzienników urządzeń sieci wirtualnej, dzienników sieci szkieletowej i innych źródeł.

- **Wykrywanie anomalii**: Azure Security Center używa także wykrywania anomalii do identyfikowania zagrożeń. W przeciwieństwie do analizy behawioralnej (która zależy od znanych wzorców pochodzących z dużych zestawów danych), wykrywanie anomalii jest bardziej "spersonalizowane" i koncentruje się na liniach bazowych, które są specyficzne dla wdrożeń. Uczenie maszynowe jest stosowane do określania normalnego działania wdrożeń, a następnie generowania reguł definiujących odstające warunki, które mogą reprezentować zdarzenie związane z zabezpieczeniami.

## <a name="how-are-alerts-classified"></a>Jak są klasyfikowane alerty?

Security Center przypisuje ważność do alertów, ułatwiając określanie priorytetów kolejności, w której uczestniczy każdy alert, dzięki czemu w przypadku naruszenia zabezpieczeń zasobów można od razu przejść do niego. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu.

> [!NOTE]
> Ważność alertu jest wyświetlana w różny sposób w portalu i wersjach interfejsu API REST, który jest przedstawiony jako 01-01-2019. Jeśli używasz starszej wersji interfejsu API, Uaktualnij środowisko pod kątem spójnego środowiska opisanego poniżej.

| Ważność          | Zalecana odpowiedź                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Wysoki**          | Istnieje duże prawdopodobieństwo naruszenia bezpieczeństwa zasobu. Należy od razu przyjrzeć się do niego. Security Center ma wysoki poziom zaufania zarówno w złośliwym zamiarach, jak i w ustaleniach używanych do wystawiania alertu. Na przykład alert, który wykrywa wykonywanie znanego złośliwego narzędzia, takiego jak program mimikatz, typowego narzędzia używanego do kradzieży poświadczeń.                                                                                                               |
| **Średni**        | Prawdopodobnie podejrzane działanie może wskazywać na naruszenie bezpieczeństwa zasobu. Stopień zaufania Security Center w analitycznym lub wyszukiwaniu jest średni, a wiarygodność złośliwego celu jest wysoka. Zazwyczaj mogą to być Uczenie maszynowe lub wykryte anomalie. Na przykład próba logowania z nietypowej lokalizacji.                                                                                                                |
| **Niski**           | Może to być niegroźne pozytywne lub zablokowany atak. Security Center nie ma wystarczającej pewności, że zamiar jest złośliwy, a działanie może być nieszkodliwe. Na przykład dziennik czyszczenie jest akcją, która może wystąpić, gdy osoba atakująca próbuje ukryć swoje ścieżki, ale w wielu przypadkach jest to procedura wykonywana przez administratorów. Security Center zazwyczaj nie informuje użytkownika o zablokowaniu ataków, chyba że jest to interesujący przypadek, który sugerujemy. |
| **Informacyjne** | Podczas przechodzenia do szczegółów zdarzenia związanego z bezpieczeństwem będą wyświetlane tylko alerty informacyjne lub użycie interfejsu API REST z określonym IDENTYFIKATORem alertu. Zdarzenie zwykle składa się z wielu alertów, a niektóre z nich mogą być wyświetlane tylko w celu uzyskania informacji, ale w kontekście innych alertów może być zaufanego bliższego wyglądu.                                                                                                                         |  |
|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

## <a name="export-alerts"></a>Eksportowanie alertów

Masz szereg opcji wyświetlania alertów poza Security Center, w tym:

- **Pobierz raport CSV** na pulpicie nawigacyjnym alertów umożliwia jednorazowe Eksportowanie do pliku CSV.
- **Eksport ciągły** z ustawień & cenowych umożliwia skonfigurowanie strumieni alertów zabezpieczeń i zaleceń dotyczących log Analytics obszarów roboczych i Event Hubs. [Dowiedz się więcej na temat eksportu ciągłego](continuous-export.md)
- **Łącznik wskaźnikowy platformy Azure** przesyła strumieniowo alerty zabezpieczeń z Azure Security Center do platformy Azure. [Dowiedz się więcej na temat łączenia Azure Security Center z platformą Azure — wskaźnikiem](../sentinel/connect-azure-security-center.md)

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelacja inteligentnego alertu w chmurze w Azure Security Center (zdarzenia)

Azure Security Center stale analizuje obciążenia chmury hybrydowej dzięki zaawansowanej analizie i analizie zagrożeń, aby ostrzec o złośliwych działaniach.

Rośnie zasięg zagrożeń. Konieczność wykrywania nawet niewielkich kompromisów jest istotna i może być trudne dla analityków zabezpieczeń, aby klasyfikacja różne alerty i identyfikować faktyczny atak. Security Center ułatwia analitykom radzenie sobie z tym zmęczeniem alertów. Pomaga w diagnozowaniu ataków w miarę ich występowania, przez skorelowanie różnych alertów i słabych sygnałów w przypadku zdarzeń zabezpieczeń.

Analiza Fusion jest technologią i zapleczem analitycznym, która umożliwia Security Center incydenty, co umożliwia działowi IT skorelowanie różnych alertów i sygnałów kontekstowych. Fusion przegląda różne sygnały raportowane w ramach subskrypcji dla zasobów. Funkcja Fusion odnajduje wzorce, które ujawniają postęp ataku lub sygnalizują się z udostępnionymi informacjami kontekstowymi, wskazując, że należy użyć ujednoliconej procedury odpowiedzi.

Funkcja Fusion Analytics łączy informacje o domenie zabezpieczeń z AI, aby analizować alerty, odkrywając nowe wzorce ataku w miarę ich występowania. 

Security Center korzysta z macierzy ataków MITRE do kojarzenia alertów z ich zamiarem, pomagając w prace prowadzone informacji o domenie zabezpieczeń. Ponadto, korzystając z informacji zebranych dla każdego kroku ataku, Security Center może wyrównać działanie, które wydaje się być czynnościami ataku, ale w rzeczywistości nie jest.

Ponieważ ataki często występują między różnymi dzierżawcami, Security Center mogą łączyć algorytmy AI, aby analizować sekwencje ataków, które są zgłaszane w poszczególnych subskrypcjach. Ta technika identyfikuje sekwencje ataków jako nieznane wzorce alertów, a nie tylko incydentowo powiązane ze sobą.

Podczas badania zdarzenia analitykowie często potrzebują dodatkowego kontekstu, aby dotrzeć do Verdict o charakterze zagrożenia i sposobach ich rozwiązywania. Na przykład nawet w przypadku wykrycia nietypowej sieci, bez zrozumienia, co się dzieje w sieci lub w odniesieniu do zasobu, trudno jest zrozumieć, jakie działania należy podjąć dalej. Aby pomóc, zdarzenie zabezpieczeń może obejmować artefakty, powiązane zdarzenia i informacje. Dodatkowe informacje dostępne dla zdarzeń związanych z zabezpieczeniami różnią się w zależności od typu wykrytego zagrożenia i konfiguracji środowiska. 

> [!TIP]
> Aby uzyskać listę alertów dotyczących zdarzeń zabezpieczeń, które mogą być tworzone przez usługę Fusion Analytics, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Zrzut ekranu przedstawiający raport wykrytych zdarzeń zabezpieczeń":::

Aby zarządzać zdarzeniami zabezpieczeń, zobacz [jak zarządzać zdarzeniami zabezpieczeń w Azure Security Center](security-center-incident.md).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o różnych typach alertów dostępnych w Security Center. Aby uzyskać więcej informacji, zobacz:

- [Alerty zabezpieczeń w dzienniku aktywności platformy Azure](https://go.microsoft.com/fwlink/?linkid=2114113) — oprócz dostępnych w Azure Portal lub programowo, alerty zabezpieczeń i zdarzenia są poddawane inspekcji jako zdarzenia w dzienniku aktywności platformy Azure
- [Tabela referencyjna alertów usługi Azure Defender](alerts-reference.md)
- [Odpowiadanie na alerty zabezpieczeń](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)