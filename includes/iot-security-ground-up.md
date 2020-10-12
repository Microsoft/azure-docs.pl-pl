---
title: plik dołączany
description: plik dołączany
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4f7ec9bbf7877d8067b3d8b1566a5cd2c2075281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050324"
---
Internet rzeczy (IoT) stanowi unikatowe wyzwania w zakresie zabezpieczeń, prywatności i zgodności dla firm na całym świecie. W przeciwieństwie do tradycyjnej technologii cybernetycznymi, w której te problemy są nanoszone wokół oprogramowania i sposobu ich implementacji, IoT ma wpływ na to, co się dzieje w przypadku zbieżności cybernetycznymi i fizycznych rozwiązań. Ochrona rozwiązań IoT wymaga zapewnienia bezpiecznego aprowizacji urządzeń, bezpiecznej łączności między tymi urządzeniami i chmurą oraz zapewnienia bezpiecznej ochrony danych w chmurze podczas przetwarzania i przechowywania. Korzystanie z tych funkcji jest jednak urządzeniami z ograniczeniami zasobów, dystrybucją geograficzną wdrożeń i dużą liczbą urządzeń w ramach rozwiązania.

W tym artykule przedstawiono sposób, w jaki Akceleratory rozwiązań IoT zapewniają bezpieczne i prywatne rozwiązanie Internet rzeczy w chmurze. Akceleratory rozwiązań zapewniają kompleksowe kompleksowe rozwiązanie z zabezpieczeniami wbudowanymi na każdy etap. Firma Microsoft opracowuje bezpieczne oprogramowanie, które jest częścią ćwiczeń inżynieryjnych firmy Microsoft, w przypadku tworzenia bezpiecznego oprogramowania. Aby to zapewnić, cykl projektowania zabezpieczeń (SDL) to podstawowe metodologie programowania, powiązane z hostem usług zabezpieczeń na poziomie infrastruktury, takich jak gwarancja bezpieczeństwa operacyjnego (OSA) i jednostka zbrodni cyfrowych firmy Microsoft, centrum Microsoft Security Response Center oraz centrum firmy Microsoft ds. ochrony przed złośliwym oprogramowaniem.

Akceleratory rozwiązań oferują unikatowe funkcje, które umożliwiają udostępnianie, łączenie i przechowywanie danych z urządzeń IoT w łatwy i przejrzysty i najbardziej bezpieczny sposób. W tym artykule przedstawiono funkcje zabezpieczeń akceleratorów rozwiązań Azure IoT i strategie wdrażania, aby zapewnić rozwiązywanie problemów z zabezpieczeniami, ochroną prywatności i zgodnością.

## <a name="introduction"></a>Wprowadzenie

Internet rzeczy (IoT) to fala w przyszłości, oferująca firmie bezpośrednie i rzeczywiste możliwości zmniejszania kosztów, zwiększania przychodów i przekształcania ich działalności. Wiele firm jest jednak wątpliwości do wdrażania IoT w swoich organizacjach ze względu na kwestie związane z bezpieczeństwem, prywatnością i zgodnością. Najważniejszym punktem obaw jest niepowtarzalność infrastruktury IoT, która polega na scaleniu cybernetycznymi i fizycznych sieci komputerowych, które wiążą się z indywidualnymi zagrożeniami, które są związane z tymi dwoma środowiskami. Bezpieczeństwo IoT dotyczy zapewniania integralności kodu działającego na urządzeniach, zapewniania uwierzytelniania urządzeń i użytkowników, definiowania jasnej własności urządzeń (jak również danych generowanych przez te urządzenia) i odporności na ataki cybernetycznymi i fizyczne.

Następnie występuje problem z ochroną prywatności. Firmy potrzebują przejrzystości dotyczącej zbierania danych, jak to, co jest zbierane i dlaczego mogą je zobaczyć, kto kontroluje dostęp itd. Na koniec istnieją ogólne problemy z bezpieczeństwem sprzętu wraz z osobami pracującymi nad nimi oraz problemy z utrzymywaniem standardów branżowych zgodności.

Ze względu na kwestie dotyczące zabezpieczeń, ochrony prywatności, przejrzystości i zgodności, wybór odpowiedniego dostawcy rozwiązań IoT nadal jest wyzwaniem. Łącząc się z indywidualnymi elementami oprogramowania i usług IoT oferowanych przez różnych dostawców, wprowadzono luki w zabezpieczeniach, prywatności, przejrzystości i zgodności, które mogą być trudne do wykrycia. Wybór odpowiedniego oprogramowania i dostawcy usług IoT jest oparty na znalezieniu dostawców, którzy mają rozległe doświadczenie w działaniu usług, które rozciągają się na lokalizacje geograficzney i w sposób bezpieczny i przejrzysty. Podobnie, w przypadku wybranego dostawcy można korzystać z dekady doświadczenia w tworzeniu bezpiecznego oprogramowania działającego w miliardach maszyn na całym świecie i mieć możliwość doceniania zagrożeń powodowanych przez ten nowy świat Internet rzeczy.

## <a name="secure-infrastructure-from-the-ground-up"></a>Bezpieczna infrastruktura od podstaw

Infrastruktura [Microsoft Cloud](https://azure.microsoft.com) obsługuje ponad 1 000 000 000 klientów w 127 krajach/regionach. W przypadku korzystania z oprogramowania firmy Microsoft z dużą ilością, w której można tworzyć oprogramowanie korporacyjne i uruchamiać niektóre największe Usługi online na świecie, Microsoft Cloud zapewnia wyższe poziomy zabezpieczeń, ochrony prywatności, zgodności i zagrożeń, które nie są stosowane w przypadku większości klientów.

[Cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/) stanowi obowiązkowy proces programistyczny dla całej firmy, który osadza wymagania dotyczące zabezpieczeń w całym cyklu życia oprogramowania. Aby zapewnić, że działania operacyjne są zgodne z tym samym poziomem praktyk zabezpieczeń, SDL korzysta ze rygorystycznych wytycznych dotyczących zabezpieczeń, które zostały ustanowione w procesie Microsoft Security Assurance (OSA). Firma Microsoft współpracuje również z firmami inspekcyjnymi innych firm w celu przeprowadzania ciągłej weryfikacji, że spełnia ona swoje zobowiązania dotyczące zgodności, a firma Microsoft zaangażuje się w szersze wysiłki w zakresie bezpieczeństwa dzięki tworzeniu centrów doskonałości, takich jak jednostka zbrodni cyfrowych firmy Microsoft, centrum Microsoft Security Response Center oraz centrum firmy Microsoft ds. ochrony przed złośliwym oprogramowaniem.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure — Bezpieczna infrastruktura IoT dla Twojej firmy

Microsoft Azure oferuje kompletne rozwiązanie w chmurze, które łączy stale rosnącą kolekcję zintegrowanych usług w chmurze — analizy, uczenia maszynowego, magazynu, zabezpieczeń, sieci i sieci Web — dzięki wiodącym w branży zobowiązaniom do ochrony i prywatności danych. Strategia założenia [naruszenia](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) firmy Microsoft korzysta z dedykowanego, *czerwonego zespołu* ekspertów ds. zabezpieczeń oprogramowania, którzy symulują ataki, testując możliwości platformy Azure w celu wykrywania, ochrony przed nowymi zagrożeniami i odzyskiwania po naruszeniu. Globalny zespół [reagowania na zdarzenia](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) firmy Microsoft działa wokół zegara, aby wyeliminować skutki ataków i złośliwych działań. Zespół postępuje zgodnie z procedurami dotyczącymi zarządzania zdarzeniami, komunikacji i odzyskiwania oraz korzystania z możliwości odnajdywania i przewidywalnych interfejsów z partnerami wewnętrznymi i zewnętrznymi.

Systemy firmy Microsoft zapewniają ciągłą ochronę przed włamaniami, zapobieganie atakom, regularne testowanie penetracji i narzędzia śledczej, które pomagają identyfikować i ograniczać zagrożenia. [Uwierzytelnianie wieloskładnikowe](../articles/active-directory/authentication/multi-factor-authentication.md) zapewnia dodatkową warstwę zabezpieczeń dla użytkowników końcowych, którzy uzyskują dostęp do sieci. W przypadku aplikacji i dostawcy hosta firma Microsoft oferuje funkcje kontroli dostępu, monitorowania, ochrony przed złośliwym oprogramowaniem, skanowania, poprawek i zarządzania konfiguracją.

Akceleratory rozwiązań wykorzystują funkcje zabezpieczeń i ochrony prywatności wbudowane w platformę Azure, a także procesy SDL i OSA umożliwiające bezpieczne opracowywanie i obsługę całego oprogramowania firmy Microsoft. Te procedury zapewniają ochronę infrastruktury, ochronę sieci oraz funkcje tożsamości i zarządzania podstawowe dla bezpieczeństwa dowolnego rozwiązania.

Usługa [azure IoT Hub](../articles/iot-hub/about-iot-hub.md) w ramach [akceleratorów rozwiązań IoT](../articles/iot-fundamentals/iot-introduction.md) oferuje w pełni zarządzaną usługę, która umożliwia niezawodne i niezawodną komunikację dwukierunkową między urządzeniami IoT i usługami platformy Azure, takimi jak [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) i [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) , przy użyciu poświadczeń zabezpieczeń poszczególnych urządzeń i kontroli dostępu.

Aby najlepiej komunikować funkcje zabezpieczeń i prywatności wbudowane w Akceleratory rozwiązań usługi Azure IoT, w tym artykule zostaje podzielony pakiet na trzy podstawowe obszary zabezpieczeń.

![Akceleratory rozwiązań Azure IoT](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Bezpieczne Inicjowanie obsługi i uwierzytelnianie urządzeń

Akceleratory rozwiązań zabezpieczają urządzenia w niezmienionej postaci, dostarczając unikatowy klucz tożsamości dla każdego urządzenia, który może być używany przez infrastrukturę IoT do komunikowania się z urządzeniem podczas jego działania. Proces jest szybki i łatwy do skonfigurowania. Wygenerowany klucz z IDENTYFIKATORem urządzenia wybranego przez użytkownika stanowi podstawę tokenu używanego do komunikacji między urządzeniem a IoT Hub platformy Azure.

Identyfikatory urządzeń można kojarzyć z urządzeniem w trakcie produkcji (czyli w ramach sprzętowego modułu zaufania) lub użyć istniejącej stałej tożsamości jako serwera proxy (na przykład numerów seryjnych procesora CPU). Ze względu na to, że zmiana informacji identyfikacyjnych na urządzeniu nie jest prosta, ważne jest wprowadzenie identyfikatorów urządzeń logicznych na wypadek zmiany sprzętu podstawowego urządzenia, ale urządzenie logiczne pozostaje takie samo. W niektórych przypadkach skojarzenie urządzenia może wystąpić w czasie wdrażania urządzenia (na przykład uwierzytelniony inżynier polowy fizycznie konfiguruje nowe urządzenie podczas komunikowania się z zapleczem rozwiązania). [Rejestr tożsamości usługi Azure IoT Hub](../articles/iot-hub/iot-hub-devguide.md) zapewnia bezpieczny magazyn tożsamości urządzeń i kluczy zabezpieczeń dla rozwiązania. Osoby lub grupy tożsamości urządzeń można dodawać do listy dozwolonych lub zablokowanych, umożliwiając pełną kontrolę nad dostępem urządzeń.

Zasady kontroli dostępu IoT Hub platformy Azure w chmurze umożliwiają aktywację i wyłączanie każdej tożsamości urządzenia, co pozwala na skojarzenie urządzenia z wdrożeniem IoT w razie potrzeby. To skojarzenie i rozskojarzenie urządzeń jest zależne od tożsamości urządzeń.

Dodatkowe funkcje zabezpieczeń urządzeń obejmują:

* Urządzenia nie akceptują niezamówionych połączeń sieciowych. Ustanawiają wszystkie połączenia i trasy w sposób tylko wychodzący. Aby urządzenie otrzymywało polecenie z zaplecza, urządzenie musi zainicjować połączenie w celu sprawdzenia, czy istnieją oczekujące polecenia do przetworzenia. Po poprawieniu połączenia między urządzeniem a IoT Hubm można wysłać komunikaty z chmury do urządzenia i urządzenia do chmury.

* Urządzenia łączą się tylko z lub ustanawiają trasy do dobrze znanych usług, z którymi są połączone za pomocą komunikacji równorzędnej, takie jak IoT Hub platformy Azure.

* Autoryzacja na poziomie systemu i uwierzytelnianie przy użyciu tożsamości poszczególnych urządzeń, udostępnianie poświadczeń dostępu i uprawnień niemal natychmiastowo revocable.

### <a name="secure-connectivity"></a>Bezpieczna łączność

Trwałość komunikatów jest ważną funkcją dowolnego rozwiązania IoT. Konieczność trwale dostarczania poleceń i/lub odbierania danych z urządzeń jest podkreślana przez fakt, że urządzenia IoT są połączone przez Internet, lub inne podobne sieci, które mogą być zawodne. Usługa Azure IoT Hub oferuje trwałość komunikatów między chmurą i urządzeniami za pomocą systemu potwierdzania w odpowiedzi na komunikaty. Dodatkowa trwałość do obsługi komunikatów jest uzyskiwana przez buforowanie komunikatów w IoT Hub przez maksymalnie siedem dni w przypadku danych telemetrycznych i dwóch dni dla poleceń.

Wydajność jest ważna, aby zapewnić ochronę zasobów i operacji w środowisku z ograniczeniami zasobów. Protokół HTTPS (Secure protokołu HTTP) — standardowa, branżowa, bezpieczna wersja popularnego protokołu HTTP, jest obsługiwana przez usługę Azure IoT Hub, co umożliwia efektywną komunikację. Advanced Message Queuing Protocol (AMQP) i transport telemetrii usługi kolejkowania komunikatów (MQTT) obsługiwane przez usługę Azure IoT Hub są zaprojektowane nie tylko w celu wykorzystania zasobów, ale również niezawodnego dostarczania komunikatów. 

Skalowalność wymaga możliwości bezpiecznego współdziałania z szeroką gamę urządzeń. Usługa Azure IoT Hub umożliwia bezpieczne połączenie z urządzeniami z włączonym IP i nieopartymi na adresie IP. Urządzenia obsługujące protokół IP mogą bezpośrednio łączyć się z IoT Hub za pośrednictwem bezpiecznego połączenia i komunikować się z nimi. Urządzenia nieobsługujące protokołu IP są ograniczone do zasobów i łączą się tylko za pośrednictwem protokołów komunikacji z krótką odległością, takich jak zwave, ZigBee i Bluetooth. Brama pola służy do agregowania tych urządzeń i przeprowadzania translacji protokołów w celu zapewnienia bezpiecznej komunikacji dwukierunkowej z chmurą.

Dostępne są dodatkowe funkcje zabezpieczeń połączeń:

* Ścieżka komunikacji między urządzeniami i usługą Azure IoT Hub lub między bramami a usługą Azure IoT Hub jest zabezpieczona przy użyciu standardu branżowego Transport Layer Security (TLS) z usługą Azure IoT Hub uwierzytelnianą przy użyciu protokołu X. 509.

* Aby chronić urządzenia przed nieproszonymi połączeniami przychodzącymi, usługa Azure IoT Hub nie otwiera żadnego połączenia z urządzeniem. Urządzenie inicjuje wszystkie połączenia.

* Usługa Azure IoT Hub trwale przechowuje komunikaty dla urządzeń i czeka na nawiązanie połączenia z urządzeniem. Te polecenia są przechowywane przez dwa dni, co pozwala na wypróbowanie urządzeń łączących się sporadycznie ze względu na problemy z mocą lub łącznością. Usługa Azure IoT Hub obsługuje kolejkę poszczególnych urządzeń dla każdego urządzenia.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Bezpieczne przetwarzanie i przechowywanie w chmurze

Z zaszyfrowanej komunikacji do przetwarzania danych w chmurze Akceleratory rozwiązań pomagają zapewnić bezpieczeństwo danych. Zapewnia elastyczność implementacji dodatkowego szyfrowania i zarządzania kluczami zabezpieczeń.

Korzystając z Azure Active Directory (AAD) do uwierzytelniania i autoryzacji użytkowników, akceleratory rozwiązań usługi Azure IoT mogą zapewnić model autoryzacji oparty na zasadach dla danych w chmurze, co umożliwia łatwe zarządzanie dostępem, które można poddać inspekcji i przeglądać. Ten model umożliwia również niemal natychmiastowe odwoływanie dostępu do danych w chmurze oraz urządzeń podłączonych do akceleratorów rozwiązań usługi Azure IoT.

Gdy dane znajdują się w chmurze, mogą być przetwarzane i przechowywane w dowolnym zdefiniowanym przez użytkownika przepływie pracy. Dostęp do każdej części danych jest kontrolowany przy użyciu Azure Active Directory, w zależności od używanej usługi magazynu.

Wszystkie klucze używane przez infrastrukturę IoT są przechowywane w chmurze w bezpiecznym magazynie, z możliwością przejęcia kluczy przypadków należy ponownie zainicjować obsługę administracyjną. Dane mogą być przechowywane w [Azure Cosmos DB](../articles/cosmos-db/introduction.md) lub w [SQL Database](../articles/sql-database/sql-database-faq.md), co pozwala na zdefiniowanie żądanego poziomu zabezpieczeń. Ponadto platforma Azure umożliwia monitorowanie i inspekcję całego dostępu do danych w taki sposób, aby ostrzegał o wszelkich włamaniach lub nieautoryzowanym dostępie.

## <a name="conclusion"></a>Podsumowanie

Internet rzeczy rozpoczyna się od Twoich rzeczy — rzeczy, które najlepiej odpowiadają firmom. IoT może dostarczać niezwykłe wartości do firmy przez redukcję kosztów, wzrost przychodu i transformowanie firmy. Pomyślne przekształcenie jest w dużym stopniu zależne od wyboru odpowiedniego oprogramowania i dostawcy usług IoT. Oznacza to znalezienie dostawcy, który nie tylko catalyzes tę transformację, przez zrozumienie potrzeb i wymagań firmy, ale także udostępnia usługi i oprogramowanie stworzone z myślą o bezpieczeństwie, prywatności, przezroczystości i zgodności w ramach głównych zagadnień projektowych. Firma Microsoft oferuje rozległe doświadczenia związane z tworzeniem i wdrażaniem bezpiecznego oprogramowania i usług i nadal jest liderem w tym nowym wieku Internet rzeczy.

Akceleratory rozwiązań kompilują się w miary zabezpieczeń przez zaprojektowanie, co umożliwia bezpieczne monitorowanie zasobów w celu poprawy efektywności, zwiększenie wydajności działania w celu umożliwienia innowacji i zastosowanie zaawansowanej analizy danych w celu przekształcania firm. Dzięki podejściu warstwowego do zabezpieczeń, wielu funkcjom zabezpieczeń i wzorcem projektowym Akceleratory rozwiązań pomagają wdrożyć infrastrukturę, która może być zaufana, aby przekształcić każdą firmę.

## <a name="additional-information"></a>Dodatkowe informacje

Każdy akcelerator rozwiązań tworzy wystąpienia usług platformy Azure, takich jak:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): Brama, która łączy chmurę z urządzeniami. Możesz skalować do milionów połączeń na koncentrator i przetwarzać duże ilości danych z obsługą uwierzytelniania dla urządzeń, ułatwiając Zabezpieczanie rozwiązania.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Skalowalna, w pełni indeksowana usługa bazy danych dla danych z częściową strukturą, która zarządza metadanymi dla zainicjowanych urządzeń, takich jak atrybuty, konfiguracja i właściwości zabezpieczeń. Azure Cosmos DB oferuje przetwarzanie wysokiej wydajności i wysoką przepływność, niezależny od indeksowanie danych i bogaty interfejs zapytań SQL.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Przetwarzanie strumienia w czasie rzeczywistym w chmurze, które pozwala szybko opracowywać i wdrażać rozwiązanie do analizy o niskich kosztach w celu odkrywania szczegółowych informacji z urządzeń, czujników, infrastruktury i aplikacji. Dane z tej w pełni zarządzanej usługi mogą być skalowane do dowolnego woluminu i nadal osiągać wysoką przepływność, małe opóźnienia i odporność.

* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): platforma w chmurze umożliwiająca tworzenie zaawansowanych aplikacji sieci Web i mobilnych, które łączą się z danymi w dowolnym miejscu; w chmurze lub lokalnie. Twórz atrakcyjne aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowaniem jako usługą (SaaS) i aplikacjami przedsiębiorstwa dzięki wbudowanej łączności z dziesiątami z wielu usług w chmurze i aplikacji dla przedsiębiorstw. Kod w ulubionym języku i środowisku IDE — .NET, Node.js, PHP, Python lub Java — do tworzenia aplikacji sieci Web i interfejsów API szybciej niż kiedykolwiek.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): funkcja Logic Apps Azure App Service umożliwia integrację rozwiązania IoT z istniejącymi systemami biznesowymi i automatyzowanie procesów przepływu pracy. Logic Apps umożliwia deweloperom projektowanie przepływów pracy rozpoczynających się od wyzwalacza, a następnie wykonanie serii kroków — reguł i akcji, które używają zaawansowanych łączników do integracji z procesami biznesowymi. Logic Apps oferuje wbudowaną łączność z obszernym ekosystemem aplikacji SaaS, opartych na chmurze i lokalnych.

* [**Usługa Azure Blob Storage**](https://azure.microsoft.com/services/storage/): niezawodny i ekonomiczny magazyn w chmurze dla danych wysyłanych przez urządzenia do chmury.