---
title: Tworzenie komercyjnej aplikacji usługi Azure Remote Rendering
description: Strategie i zagadnienia dotyczące tworzenia komercyjnej aplikacji gotowej przy użyciu renderowania zdalnego platformy Azure
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0ff6d064b6bfabbd8a648c23b5648aae9b12a11d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207499"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Samouczek: Tworzenie komercyjnej gotowej aplikacji do zdalnego renderowania platformy Azure

Ten samouczek zawiera informacje dotyczące:

> [!div class="checklist"]
>
> * Zarządzanie sesją dla aplikacji komercyjnych
> * Śledzenie sesji dla rozliczeń
> * Optymalizowanie środowiska użytkownika wokół czasu ładowania sesji
> * Zagadnienia dotyczące opóźnień sieci

## <a name="prerequisites"></a>Wymagania wstępne

* Ten samouczek kompiluje się w [samouczku: Zabezpieczanie zdalnego renderowania i magazynu modeli na platformie Azure](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>Wprowadzenie do gotowości komercyjnej

Zdalne renderowanie na platformie Azure rozszerza możliwości z rzeczywistości mieszanej. Gdy podstawowe informacje są zintegrowane z rozwiązaniem, istnieje kilka dodatkowych zagadnień, które należy zapewnić, że rozwiązanie jest bezpieczne, skalowalne i gotowe do dostarczenia wartości.

Ten moduł przedstawia pewne dodatkowe możliwości, które należy wziąć pod uwagę w przypadku aplikacji komercyjnej.

Aby zapoznać się z szeroką omówieniem najlepszych rozwiązań w zakresie architektury, odwiedź stronę:

* [Centrum architektury platformy Azure](/azure/architecture/)
* [Przewodnik wprowadzenie dla deweloperów platformy Azure](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>Analiza

Integrowanie narzędzi analitycznych ułatwia zarządzanie, śledzenie i ulepszanie rozwiązania.

Aby uzyskać pełną listę dostępnych dla Ciebie zasobów analitycznych, odwiedź:

* [Usługi analityczne platformy Azure](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Śledzenie użycia na potrzeby rozliczeń

Śledzenie użycia zdalnego renderowania na platformie Azure przez wielu zespołów wewnętrznych lub klientów zewnętrznych jest ważnym zagadnieniem, szczególnie w sytuacjach wielodostępnych.

Aby to osiągnąć, platforma Azure oferuje usługę o nazwie tagowanie zasobów, która kojarzy użycie usługi zdalnego renderowania na platformie Azure z każdym klientem.

Aby uzyskać więcej informacji o nazewnictwie i znakowaniu zasobów, dobrym miejscem do rozpoczęcia jest:

* [Przewodnik po nazewnictwie i znakowaniu zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%252fazure%252fazure-resource-manager%252fmanagement%252ftoc.json)

### <a name="diagnostics"></a>Diagnostyka

Zaawansowane narzędzia, takie jak śledzenie zdarzeń dla systemu Windows (ETW) i rejestrowanie śledzenia zdarzeń (ETL), ułatwiają generowanie zdarzeń śledzenia w aplikacji, a także ułatwiają diagnozowanie sieci, pozyskiwanie zawartości, sesji, aplikacji oraz inne problemy, które mogą wystąpić w przypadku wdrożenia rozwiązania handlowego.

Aby uzyskać więcej informacji, odwiedź stronę:

* [Tworzenie śladów wydajności po stronie klienta](../../../how-tos/performance-tracing.md)
* [Jak zbierać dane śledzenia zdarzeń systemu Windows (ETW)](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Korzystanie z portalu urządzeń z systemem Windows: rejestrowanie](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Analiza użycia

Usługa Azure Application Insights pomaga zrozumieć, jak użytkownicy korzystają z aplikacji do zdalnego renderowania na platformie Azure. Za każdym razem, gdy aktualizujesz swoją aplikację, możesz ocenić, jak dobrze działa dla użytkowników i odpowiednio ulepszyć rozwiązanie. Korzystając z tej wiedzy, można podejmować decyzje oparte na danych dotyczące kolejnych cykli tworzenia oprogramowania.

Aby uzyskać więcej informacji, odwiedź stronę:

* [Analiza użycia za pomocą Application Insights](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>Strategie szybkiego uruchamiania

Twoje przypadki użycia mogą wymagać szybkiego uruchomienia z uruchamiania aplikacji do wyświetlania modelu 3W. Na przykład podczas ważnych spotkań, w przypadku których najważniejsze jest, aby wszystko było niezwykle czasochłonne. Innym przykładem jest podczas projektowania modelu 3W CAD, gdzie szybka iteracja projektu między aplikacją CAD i rzeczywistości mieszanej ma kluczowe znaczenie dla wydajności.

Zdalne renderowanie na platformie Azure wymaga wstępnie przetworzonych modeli 3W, a platforma Azure trwa kilka minut, aby można było utworzyć sesję i załadować model do renderowania. Proces ten zapewnia bezproblemowe i szybkie, jak to możliwe, wymaga przygotowania danych modelu 3D i sesji ARR przed czasem.

Sugestie udostępnione w tym miejscu nie są obecnie częścią standardowego renderowania zdalnego platformy Azure, ale można je wdrożyć na własne potrzeby w krótszym czasie uruchamiania.

### <a name="initiate-early"></a>Inicjuj wczesne

W celu skrócenia czasu uruchamiania najprostszym rozwiązaniem jest przeniesienie i zainicjowanie sesji tak szybko, jak to możliwe w przepływie pracy użytkownika. Jedną z strategii jest zainicjowanie sesji, gdy tylko wiadomo, że wymagana jest sesja ARR. Jest to często konieczne, gdy użytkownik rozpocznie przekazywanie modelu 3D do usługi Azure Blob Storage w celu użycia z funkcją zdalnego renderowania platformy Azure. W takim przypadku można zainicjować tworzenie i inicjalizację sesji w tym samym czasie, co model 3W, co umożliwia równoległe uruchamianie obu strumieni służbowych.

Proces ten można usprawnić, upewniając się, że wybrane kontenery danych wejściowych i wyjściowych platformy Azure Blob Storage znajdują się w tym samym regionalnym centrum danych co sesja renderowania zdalnego platformy Azure.

### <a name="scheduling"></a>Planowanie

Jeśli wiesz, że masz przyszłą potrzebę do zdalnego renderowania na platformie Azure, możesz zaplanować określoną datę i godzinę, aby rozpocząć sesję renderowania zdalnego platformy Azure.

Ta opcja może być oferowana za pomocą portalu sieci Web, w którym ludzie mogą zarówno przekazać model 3D, jak i zaplanować czas wyświetlania go w przyszłości. Jest to również dobre miejsce do poproszenia o inne preferencje, takie jak renderowanie [*standardowe*](../../../reference/vm-sizes.md) lub [*Premium*](../../../reference/vm-sizes.md) . Renderowanie w *warstwie Premium* może być odpowiednie, jeśli istnieje potrzeba pokazania różnych zasobów, w których idealny rozmiar jest trudniejszy do automatycznego określania lub potrzeby zapewnienia, że w regionie świadczenia usługi Azure są dostępne maszyny wirtualne o określonym czasie.

### <a name="session-pooling"></a>Buforowanie sesji

W najbardziej wymagających sytuacjach inną opcją jest buforowanie sesji, w której co najmniej jedna sesja jest tworzona i inicjowana przez cały czas. Spowoduje to utworzenie puli sesji do natychmiastowego użycia przez żądającego użytkownika. Minusem tego podejścia polega na tym, że po zainicjowaniu maszyny wirtualnej naliczanie opłat za usługę zostanie rozpoczęte. Utrzymywanie puli sesji przez cały czas może nie być opłacalne, ale w oparciu o analizę, może być możliwe przewidywalnie obciążeń szczytowych lub połączone z strategią planowania powyżej, aby przewidzieć czas, w którym sesje będą konieczne i odpowiednio zwiększają poziom puli sesji.

Ta strategia pomaga również zoptymalizować wybór między sesjami *Standard* i *Premium* w bardziej dynamiczny sposób, ponieważ może on być dużo szybszy, aby przełączać się między dwoma typami w ramach jednej sesji użytkownika, np. w przypadku, gdy najpierw jest wyświetlany model złożoności *Premium* , a następnie taki, który może działać w ramach warstwy *standardowa*. Jeśli te sesje użytkowników są długotrwałe, mogą wystąpić znaczne oszczędności.

Aby uzyskać więcej informacji na temat sesji renderowania zdalnego platformy Azure, zapoznaj się z tematem:

* [Sesje usługi Remote Rendering](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Strategie routingu rozmiaru serwera w warstwie Standardowa i Premium

Konieczność wybrania opcji tworzenia rozmiaru serwera w *warstwie Standardowa* lub *Premium* stanowi wyzwanie podczas projektowania środowiska użytkownika i kompleksowego systemu. Chociaż użycie tylko sesji *Premium* jest opcją, *standardowe* sesje używają znacznie mniej zasobów obliczeniowych platformy Azure i są tańsze niż w *warstwie Premium*. Zapewnia to silną motywację do korzystania z sesji *standardowych* w miarę możliwości i w razie potrzeby używa tylko wersji *Premium* .

W tym miejscu udostępniamy kilka opcji, od najmniej do najbardziej kompleksowych, aby sprostać chęci do zarządzania opcjami sesji.

### <a name="use-only-standard-or-premium"></a>Używaj tylko wersji Standard lub Premium

Jeśli masz pewność, że Twoje potrzeby będą *zawsze* mniejsze niż próg między warstwami *standardowa* i *Premium*, upraszcza to decyzje. Używaj tylko *Standard*. Należy pamiętać, że wpływ na środowisko użytkownika jest istotny, jeśli całkowita złożoność załadowanych zasobów jest odrzucana jako zbyt złożona dla sesji *standardowej* .

Podobnie, jeśli oczekujesz, że duża część użycia przekroczy wartość progową w warstwach *standardowa* i *Premium*, lub koszty nie są kluczowymi czynnikami w przypadku użycia, a następnie zawsze wybierasz opcję *Premium* .

### <a name="ask-the-user"></a>Poproszenie użytkownika

Jeśli chcesz obsłużyć warstwę *standardowa* i *Premium*, najprostszym sposobem ustalenia, który typ sesji ma być wystąpieniem, jest poproszenie użytkownika o wybranie zasobów 3D do wyświetlenia. Tym podejściem jest wymaganie od użytkownika zrozumienia złożoności zasobu 3W lub nawet wielu zasobów, które zostaną wyświetlone. Zwykle nie jest to zalecane z tego powodu. Jeśli użytkownik wybierze pozycję nieprawidłowość i wybiera wartość *standardowa*, wyniki środowiska użytkownika mogą zostać naruszone w inopportune momencie.

### <a name="analyze-the-3d-model"></a>Analizowanie modelu 3W

Inna stosunkowo prosta metoda polega na analizie złożoności wybranych zasobów 3W. Jeśli złożoność modelu jest niższa niż wartość progowa dla warstwy *standardowa*, zainicjuj sesję *standardową* , w przeciwnym razie zainicjuj sesję *Premium* . W tym przypadku wyzwaniem jest, że jedna sesja może być używana do wyświetlania wielu modeli, w których niektóre mogą przekroczyć próg złożoności sesji *standardowej* , co spowodowało bezproblemową obsługę tej samej sesji dla sekwencji różnych zasobów 3W.

### <a name="automatic-switching"></a>Automatyczne przełączanie

Automatyczne przełączanie między sesjami *Standard* i *Premium* może znacznie sensować projekt systemu, który obejmuje również buforowanie sesji. Ta strategia umożliwia dalsze Optymalizacja użycia zasobów. Gdy użytkownik ładuje modele do wyświetlania, zostanie ustalona złożoność i jest wymagany prawidłowy rozmiar sesji z usługi buforowania sesji.

## <a name="working-with-networks"></a>Praca z sieciami

### <a name="diagnostics"></a>Diagnostyka

Zdalne renderowanie na platformie Azure wymaga szybkiego połączenia z Internetem z małym opóźnieniem. Jakość sieci użytkownika może mieć znaczny wpływ na jakość środowiska. Mając na względzie, że klienci mogą mieć różne konfiguracje sieci i tylko sporadycznie złe opóźnienia sieci, narzędzia diagnostyczne są kluczem.  

Aby zapewnić spójność wysokiej jakości środowiska, zalecamy integrację narzędzi analitycznych po stronie serwera i klienta z aplikacjami do zdalnego renderowania na platformie Azure. Dzięki temu możesz poradzić sobie z informacjami potrzebnymi do zdiagnozowania i rozwiązania problemów z siecią, które mogą wystąpić klienci.

### <a name="client-network-configurations"></a>Konfiguracje sieci klienta

Jednym z największych wyzwań związanych z tworzeniem niezawodnych rozwiązań do współpracy, które są wdrażane w różnych środowiskach korporacyjnych, jest przygotowanie do różnych topologii sieci i konfiguracji zapory przedsiębiorstwa, które mogą być używane przez klientów.

Wiele przedsiębiorstw blokuje cały ruch równorzędny w sieci LAN. Dzięki temu trudno jest wykorzystać prostotę i usprawnione środowisko użytkownika funkcji automatycznego odnajdywania sieci LAN w celu ustanowienia lokalnej udostępnionej sesji między wszystkimi wykrytymi wystąpieniami aplikacji rzeczywistości mieszanej.

Inne potencjalne punkty awarii są routerami skonfigurowanymi do celowego ograniczania przepustowości i zapór, które blokują większość portów TCP/IP.

Za każdym razem, gdy planujesz korzystanie z zdalnego renderowania platformy Azure w nieznanej sieci, zalecamy wykonanie następujących czynności:

* Podaj listę kontrolną przedspotkaniu, aby ocenić gotowość sieci.
* Upewnij się, że odpowiednie regionalne centrum danych może obsłużyć żądanie.
* Umożliwia wiele czasu na zdiagnozowanie wszelkich problemów.
* Przenieś Hotspot mobilny do planu danych o wysokiej przepustowości jako kopii zapasowej.

### <a name="end-to-end-bandwidth"></a>Przepustowość kompleksowa

Ważne jest, aby ocenić możliwości przepustowości dla każdego rozgałęzienia sieci, które mogą istnieć między maszyną wirtualną renderowania zdalnego platformy Azure a klientem końcowym. Należy pamiętać, że segment sieci z centrum danych platformy Azure do usługodawcy internetowego klienta może być bardziej ograniczony niż od usługodawcy internetowego do klienta. Test szybkości pobierania obiektów BLOB może pomóc w zdiagnozowaniu takich problemów.

### <a name="bandwidth-competition"></a>Konkurencja przepustowości

Projektując aplikację rzeczywistości mieszanej, pamiętaj, że różne funkcje aplikacji mogą konkurować ze zdalnym renderowaniem na platformie Azure. Najlepszym nieoczekiwanym przykładem jest to, że wiele uczestników w jednym pokoju oczekuje jednocześnie na użycie ARR do wyświetlenia zasobów 3W. Każdy etap przepływu danych w sieci musi mieć pojemność do transportu łączną sumę wszystkich strumieni danych ARR.

Inne przykłady obejmują przesyłane strumieniowo wideo, jednoczesne przekazywanie w tle do innej powiązanej zawartości i rozmowy głosowej, szczególnie tam, gdzie istnieje wiele uczestników, a system używa rozproszonego podejścia równorzędnego, które jest oparte na serwerze mieszania audio w środkowym podejściu.

Aby uzyskać więcej informacji na temat analizy sieci, zobacz:

* [Test szybkości pobierania Azure Storage Blob](https://www.azurespeed.com/Azure/Download)
* [Statystyka opóźnienia podróży sieci platformy Azure](../../../../networking/azure-network-latency.md)
* [Ślady wydajności po stronie serwera](../../../overview/features/performance-queries.md)
* [Ślady wydajności po stronie klienta](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>Zagadnienia dotyczące współpracy

Niektóre z najbardziej przydatnych sposobów użycia zdalnego renderowania platformy Azure obejmują współpracę między wieloma uczestnikami, w tym samym czasie. W tych udostępnionych sesjach warto rozpoznać, że każdy uczestnik będzie potrzebować unikatowej sesji renderowania zdalnego platformy Azure, niezależnie od tego, czy znajdują się one w tym samym miejscu w tej samej sieci, czy nie.

Dzieje się tak, ponieważ każdy uczestnik rzeczywiście widzi to samo środowisko z różnych punktów Vantage, co wymaga jednoczesnego renderowania tych samych zasobów 3W z każdego z tych perspektyw.

### <a name="multiple-azure-remote-rendering-sessions"></a>Wiele sesji renderowania zdalnego na platformie Azure

Jeśli zamierzasz obsługiwać udostępnione środowiska za pomocą renderowania zdalnego platformy Azure, należy przygotować systemy przeznaczone do tworzenia i zarządzania sesjami ARR, aby inicjować wiele sesji. Te sesje mogą wymagać zainicjowania w różnych centrach danych platformy Azure, jeśli uczestnicy są geograficznie rozproszeni.

System musi również zarządzać możliwością, że co najmniej jeden z uczestników może znajdować się w regionie geograficznym, który obecnie nie jest obsługiwany przez renderowanie zdalne na platformie Azure lub obecnie nie ma dostępnych wystąpień maszyny wirtualnej do zdalnego renderowania platformy Azure.

To zarządzanie wieloma jednoczesnymi sesjami można dodatkowo usprawnić w połączeniu z pulą sesji i innymi strategiami omówionymi w tym dokumencie.

### <a name="azure-blob-storage-considerations"></a>Zagadnienia dotyczące Blob Storage platformy Azure

Wszystkie jednoczesne sesje z ARR mogą odwoływać się do tego samego identyfikatora URI sygnatury dostępu współdzielonego dla konwertowanego modelu. Dzięki temu możliwe jest przekazanie i przekonwertowanie żądanych zasobów 3D raz, a następnie udostępnienie go we wszystkich sesjach. Jest to szczególnie ważne, gdy uczestnicy znajdują się na jednym z nich i korzystają z tego samego centrum danych, w którym nie ma żadnych problemów dotyczących wydajności związanych z Blob Storage platformy Azure znajdujących się w innym centrum danych niż serwer zdalnego renderowania platformy Azure i użytkownik.

Jeśli zasoby 3W są zwykle przekazywane dla pojedynczej sesji wyświetlania, a następnie odrzucone, na przykład w sesji przeglądu projektu, region geograficzny Blob Storage platformy Azure względem serwera zdalnego renderowania platformy Azure jest również mniej krytyczny.

Jednak w przypadku zasobów 3W, które będą używane wielokrotnie, na przykład w przypadku użycia szkoleń, zalecamy przechowywanie gotowych zasobów 3W w magazynie obiektów BLOB w poszczególnych regionalnych centrach danych, w których planujesz korzystanie z zdalnego renderowania platformy Azure. Można to zrobić automatycznie przy użyciu nadmiarowości usługi Azure Storage. Sieć CDN jest często używana do tego celu, ale nie jest to opcja dla zdalnego renderowania platformy Azure.

Więcej informacji:

* [Udostępnione środowiska w rzeczywistości mieszanej](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Nadmiarowość usługi Azure Storage](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>Zarządzanie dostępem do modelu

Korzystanie z zdalnego renderowania platformy Azure w pełni wymaga starannej uwagi na kompleksową infrastrukturę do zarządzania modelami 3W.

Zaletą korzystania z funkcji renderowania zdalnego na platformie Azure jest to, że duże zasoby 3W nigdy nie muszą być przesyłane bezpośrednio do urządzenia rzeczywistości mieszanej przed wyświetleniem.  Ponadto, gdy zasób 3W został przekazany i przekonwertowany do użycia z funkcją renderowania zdalnego platformy Azure, dowolna liczba użytkowników może współużytkować to pojedyncze wystąpienie modelu 3W.

### <a name="considerations-for-3d-model-access"></a>Zagadnienia dotyczące dostępu do modelu 3W

Poniżej przedstawiono kilka najważniejszych zagadnień związanych z decydowaniem o strategii dostępu do modelu.

W oparciu o przewidywany przypadek użycia Określ najlepsze miejsce lub kombinację miejsc, aby umożliwić użytkownikowi wybranie zasobów 3D do wyświetlenia. Poniżej przedstawiono niektóre typowe opcje:

* Bezpośrednio w środowisku rzeczywistości mieszanej
* Za pośrednictwem portalu sieci Web towarzyszącego
* Na pulpicie pomocnika lub aplikacji mobilnej

Jeśli w przypadku użycia istnieją wzorce użycia, w których można przekazać ten sam zasób 3W wielokrotnie, zaplecze będzie śledzić, które modele są już konwertowane do użycia z ARR, tak że model jest tylko wstępnie przetworzony tylko raz dla wielu przyszłych wyborów. Przykładem przeglądu projektu jest miejsce, w którym zespół ma dostęp do wspólnego oryginalnego elementu zawartości 3W. Każdy członek zespołu powinien przejrzeć model przy użyciu ARR w pewnym momencie w swoim strumieniu służbowym. Tylko pierwszy widok wywoła krok wstępnego przetwarzania. Kolejne widoki spowodują przeszukanie skojarzonego pliku, który został przetworzony, w kontenerze wyjściowym sygnatury dostępu współdzielonego.

W zależności od przypadku użycia, prawdopodobnie zechcesz określić i potencjalnie utrzymać prawidłowy rozmiar serwera renderowania zdalnego dla platformy Azure, *Standard* lub *Premium*, dla każdego zasobu 3W lub grupy zasobów, które będą wyświetlane razem w ramach tej samej sesji.  

### <a name="on-device-model-selection-list"></a>Lista wyboru modelu na urządzeniu

W wielu przypadkach użycia, takich jak szkolenia, wskazówki dotyczące zadań lub aplikacje marketingowe, zestaw zasobów 3W, który ma być często wyświetlany w zdalnej renderowania na platformie Azure, może być dość statyczny. W takich sytuacjach nadzorowany zestaw zasobów 3W może zostać wstępnie przekonwertowany i udostępniony za pośrednictwem bazy danych zawierającej informacje niezbędne do zapełnienia listy wybranych zasobów. Te dane można następnie pobrać z aplikacji rzeczywistości mieszanej w celu wypełnienia menu wyboru.

Można to zrobić krokowo, oferując także możliwość przekazywania prywatnych zasobów 3D, unikatowych dla poszczególnych osób lub grup. Tę listę zasobów prywatnych można następnie połączyć z listą wspólnych, nadzorowanych zasobów w środowisku użytkownika w celu pobrania zasobów 3D do wyświetlenia.

### <a name="on-device-onedrive-access"></a>Dostęp do usługi OneDrive na urządzeniu

Mając na względzie, że selektor plików w usłudze OneDrive jest zbudowany natywnie na urządzeniach firmy Microsoft w rzeczywistości mieszanej, należy wybrać zasoby 3D na urządzeniu z usługi OneDrive, szczególnie w przypadku przypadków użycia, w których często można ładować różne lub zmodyfikowane modele 3W. W tym scenariuszu użytkownik wybiera jeden lub więcej zasobów 3W za pośrednictwem selektora plików usługi OneDrive w aplikacji rzeczywistości mieszanej. Zasoby 3W byłyby migrowane do kontenera wejściowego SAS, konwertowane do kontenera wyjściowego sygnatury dostępu współdzielonego i dołączone do sesji ARR. W idealnym przypadku aplikacja rzeczywistości mieszanej wywoła proces oparty na chmurze, aby wykonać te kroki, zamiast przenieść wszystkie bity z usługi OneDrive do urządzenia i wrócić do usługi Azure Blob Storage.

Takie podejście można wykonać jeszcze jeden krok poprzez utrwalanie skojarzenia między zasobami 3W, które były wcześniej przeglądane w taki sposób, że po ponownym wybraniu tego samego modelu z usługi OneDrive aplikacja może ominąć proces konwersji i bezpośrednio załadować skojarzone przekonwertowane zasoby 3W za pośrednictwem identyfikatora URI sygnatury dostępu współdzielonego.

Więcej informacji:

* [Szablon programu Microsoft PowerShell dla usługi OneDrive do usługi Azure Storage](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Omówienie interfejsu API File Storage usługi OneDrive](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Bezpośredni dostęp do CAD

Jednym z atrakcyjnych przypadków użycia dla rzeczywistości mieszanej jest projektowe przeglądy pracy w toku. W tym scenariuszu najszybszy czas ładowania z pulpitu do rzeczywistości mieszanej jest kluczem. Idealne rozwiązanie może dotyczyć tworzenia wtyczek dla określonych aplikacji CAD. Te wtyczki mogą bezpośrednio zarządzać każdym aspektem procesu ładowania, konwersji i wyświetlania:

* Podaj środowisko użytkownika:
  * Parowanie aplikacji CAD z określonym urządzeniem rzeczywistości mieszanej (jeden raz).
  * Zażądaj, aby wybrana geometria była wyświetlana na tym urządzeniu rzeczywistości mieszanej.
* Jeśli jeszcze nie działa, uruchom sesję renderowania zdalnego platformy Azure, aby umożliwić jej równoległe przetwarzanie podczas przekazywania i konwertowania pliku CAD
* Normalizuje dane geometrii CAD do jednego z formatów obsługiwanych przez renderowanie zdalne na platformie Azure
* Przesyłanie znormalizowanych danych bezpośrednio do kontenera danych wejściowych Blob Storage platformy Azure
* Inicjowanie procesu konwersji modelu
* Połącz identyfikator URI sygnatury dostępu współdzielonego kontenera danych wyjściowych modelu z sesją renderowania zdalnego platformy Azure
* Powiadom sparowaną aplikację rzeczywistości mieszanej, którą model jest dostępny i gotowy do wyświetlania i podaj identyfikator URI sygnatury dostępu współdzielonego kontenera danych wyjściowych, aby aplikacja mogła dołączyć ją do sesji.

Znacznie prostsze, ale nieco mniej usprawnione podejście może zautomatyzować proces zapisywania modelu 3D na lokalnym dysku twardym, a następnie zainicjować proces przesyłania tego zapisanego pliku do kontenera wejściowego SAS.

### <a name="azure-marketplace"></a>Azure Marketplace

Wielu klientów korporacyjnych zezwala na wdrożenie Azure Stack w ramach własnych kont platformy Azure i poświadczeń ze względów bezpieczeństwa. Aby to umożliwić, należy rozważyć pakowanie aplikacji zarządzanej przez platformę Azure w taki sposób, aby mogła zostać opublikowana w portalu Azure Marketplace jako oferta aplikacji platformy Azure.

Więcej informacji:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Samouczek: publikowanie aplikacji zarządzanych przez platformę Azure w portalu Marketplace](../../../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)

### <a name="security"></a>Zabezpieczenia

Niezwykle ważne jest, aby utworzyć kompleksowe rozwiązanie do zdalnego renderowania na platformie Azure w celu zapewnienia bezpieczeństwa od podstaw. Istnieje wiele aspektów zabezpieczeń, które należy wziąć pod uwagę w projekcie kompleksowego rozwiązania, w tym:

* Strategie uwierzytelniania
* Zarządzanie dostępem — grupy, zasady i uprawnienia
* Obsługa wielu dzierżawców
* Przechowywanie i transfer danych
* Tokeny użycia tymczasowego
* Ataki rozproszonego typu "odmowa usługi" (DDoS)
* Wykrywanie zagrożeń
* VPN i sieci zabezpieczone
* Zapory
* Zarządzanie certyfikatami i kluczami tajnymi
* Luki w zabezpieczeniach i wykorzystanie aplikacji

W celu uwierzytelnienia warto przenieść do usługi sieci Web platformy Azure tylko tyle samo uwierzytelniania i zarządzania sesją, jak to możliwe. Spowoduje to lepsze zarządzanie i bezpieczniejsze rozwiązanie.

Więcej informacji:

* [Uwierzytelnianie usługi Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [Wzmocnij stan zabezpieczeń dzięki platformie Azure](https://azure.microsoft.com/overview/security/)
* [Zabezpieczenia chmury](https://azure.microsoft.com/product-categories/security/)