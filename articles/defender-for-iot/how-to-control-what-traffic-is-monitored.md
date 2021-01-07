---
title: Sterowanie monitorowanym ruchem
description: Czujniki automatycznie wykonują głębokie wykrywanie pakietów dla ruchu IT i niezwiązanych z ruchem oraz rozwiązują informacje o urządzeniach sieciowych, takich jak atrybuty urządzenia i zachowanie sieciowe. Dostępnych jest kilka narzędzi do sterowania typem ruchu wykrywanym przez poszczególne czujnika.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b2f95ddb13896ed461a20f1948fef34569586d1f
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97977086"
---
# <a name="control-what-traffic-is-monitored"></a>Sterowanie monitorowanym ruchem

Czujniki automatycznie wykonują głębokie wykrywanie pakietów dla ruchu IT i niezwiązanych z ruchem oraz rozwiązują informacje o urządzeniach sieciowych, takich jak atrybuty i zachowania urządzeń. Dostępnych jest kilka narzędzi do sterowania typem ruchu wykrywanym przez poszczególne czujnika.

## <a name="learning-and-smart-it-learning-modes"></a>Nauka i inteligentne tryby nauki IT

Tryb uczenia instruuje czujnik, aby poznać typowe działania sieci. Przykładami są urządzenia odnalezione w sieci, protokoły wykryte w sieci, transfery plików między określonymi urządzeniami itd. To działanie jest podstawą sieci.

Tryb uczenia jest automatycznie włączany po zakończeniu instalacji i pozostanie włączony do momentu wyłączenia. Przybliżony okres trybu nauki wynosi od dwóch do sześciu tygodni, w zależności od rozmiaru i złożoności sieci. Po upływie tego czasu w przypadku wyłączenia trybu uczenia zostaną wyzwolone wszystkie nowe działania. Alerty są wyzwalane, gdy aparat zasad odnajduje odchylenia od poznanej linii bazowej.

Po zakończeniu okresu nauki i włączeniu trybu uczenia czujnik może wykryć nienormalnie wysoki poziom zmian linii bazowej, które są wynikiem normalnej aktywności IT, takich jak żądania DNS i HTTP. Działanie jest nazywane zachowaniem niedeterministycznym IT. Zachowanie może również spowodować niepotrzebne alerty naruszenia zasad i powiadomienia systemowe. Aby zmniejszyć liczbę alertów i powiadomień, można włączyć funkcję **inteligentnej nauki IT** .

Gdy inteligentne uczenie IT jest włączone, czujnik śledzi ruch sieciowy, który generuje niedeterministyczne zachowanie IT na podstawie określonych scenariuszy alertów.

Czujnik monitoruje ten ruch przez siedem dni. Jeśli wykryje ten sam Niedeterministyczny ruch IT w ciągu siedmiu dni, nadal monitoruje ruch przez kolejne siedem dni. Gdy ruch nie zostanie wykryty przez pełne siedem dni, inteligentna nauka IT jest wyłączona w tym scenariuszu. Nowy ruch wykryty dla tego scenariusza spowoduje jedynie wygenerowanie alertów i powiadomień.

Praca z inteligentną nauką IT pomaga zmniejszyć liczbę niepotrzebnych alertów i powiadomień spowodowanych przez scenariusze scenariuszy IT.

Jeśli czujnik jest kontrolowany przez lokalną konsolę zarządzania, nie można wyłączyć trybów uczenia. W takim przypadku tryb uczenia można wyłączyć tylko z poziomu konsoli zarządzania.

Możliwości uczenia (uczenie i inteligentna nauka IT) są domyślnie włączone.

Aby włączyć lub wyłączyć uczenie:

- Wybierz pozycję **Ustawienia systemowe** i Przełącz opcje **nauka** i **inteligentne uczenie IT** .

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Ekran ustawień systemowych.":::

## <a name="configure-subnets"></a>Konfigurowanie podsieci

Konfiguracje podsieci mają wpływ na sposób wyświetlania urządzeń na mapie urządzeń.

Domyślnie czujnik odnajduje konfigurację podsieci i wypełnia okno dialogowe **Konfiguracja podsieci** informacjami o tych informacjach.

Aby włączyć fokus na urządzeniach, urządzenia IT są automatycznie agregowane przez podsieć na mapie urządzeń. Każda podsieć jest prezentowana jako jedna jednostka na mapie, łącznie z interaktywną zwijaniem/rozwijaną funkcją "drążenie" do podsieci IT i z powrotem.

Podczas pracy z podsieciami wybierz podsieć usługi ICS, aby zidentyfikować podsieci. Można następnie skupić widok mapy w sieciach z nieaktywnym i ICS i zwinąć do minimum prezentację elementów sieci IT. Ten nakład pracy zmniejsza łączną liczbę urządzeń wyświetlanych na mapie i zawiera jasne zdjęcie elementów sieci OT i ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Zrzut ekranu pokazujący filtrowanie w sieci.":::

Można zmienić konfigurację lub ręcznie zmienić informacje o podsieci, eksportując odnalezione dane, zmieniając je ręcznie, a następnie importując z powrotem listę ręcznie zdefiniowanych podsieci. Aby uzyskać więcej informacji na temat eksportowania i importowania, zobacz [Importowanie informacji o urządzeniu](how-to-import-device-information.md).

W niektórych przypadkach, takich jak środowiska korzystające z zakresów publicznych jako zakresy wewnętrzne, można nakazać czujnikowi rozpoznać wszystkie podsieci jako podsieci wewnętrzne, wybierając opcję **nie wykrywaj działania z Internetu** . Po wybraniu tej opcji:

- Publiczne adresy IP będą traktowane jako adresy lokalne.

- Nie będą wysyłane żadne alerty dotyczące nieautoryzowanych działań w Internecie, co zmniejsza liczbę powiadomień i alertów odebranych w adresach zewnętrznych.

Aby skonfigurować podsieci:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W oknie **Ustawienia systemu** wybierz pozycję **podsieci**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Zrzut ekranu przedstawiający ekran konfiguracja podsieci."::: 

3. Aby automatycznie dodawać podsieci w przypadku odnalezienia nowych urządzeń, zaznacz opcję **nauka automatycznie podsieć** .

4. Aby rozpoznać wszystkie podsieci jako podsieci wewnętrzne, wybierz pozycję **nie wykrywaj aktywności internetowej**.

5. Wybierz pozycję **Dodaj sieć** i Zdefiniuj następujące parametry dla każdej podsieci:

    - Adres IP podsieci.
    - Adres maski podsieci.
    - Nazwa podsieci. Zaleca się, aby nazwy każdej podsieci miały zrozumiałą nazwę, którą można łatwo zidentyfikować, aby można było rozróżnić między nimi i niezależnymi sieciami. Nazwa może składać się z maksymalnie 60 znaków.

6. Aby oznaczyć tę podsieć jako podsieć, wybierz pozycję **podsieć usługi ICS**.

7. Aby zaprezentować podsieć oddzielnie podczas organizowania mapy zgodnie z poziomem Purdue, wybierz opcję **rozdzielone**.

8. Aby usunąć podsieć, wybierz opcję :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Aby usunąć wszystkie podsieci, wybierz pozycję **Wyczyść wszystko**.

10. Aby wyeksportować skonfigurowane podsieci, wybierz pozycję **Eksportuj**. Tabela podsieci jest pobierana do stacji roboczej.

11. Wybierz pozycję **Zapisz**.

### <a name="importing-information"></a>Importowanie informacji 

Aby zaimportować informacje o podsieci, wybierz pozycję **Importuj** i wybierz plik CSV do zaimportowania. Informacje o podsieci są aktualizowane przy użyciu zaimportowanych informacji. Jeśli ważne jest, aby puste pole, utracisz dane.

## <a name="detection-engines"></a>Aparaty wykrywania

Aparaty analizy samoszkoleniowej eliminują konieczność aktualizowania podpisów lub definiowania reguł. Aparaty wykorzystują analizy behawioralne specyficzne dla usługi ICS, aby stale analizować ruch sieciowy pod kątem anomalii, złośliwego oprogramowania, problemów operacyjnych, naruszeń protokołu i odchyleń działań w sieci podstawowej.

> [!NOTE]
> Zalecamy włączenie wszystkich aparatów zabezpieczeń.

Gdy silnik wykryje odchylenia, zostanie wyzwolony alert. Alerty można wyświetlać i zarządzać nimi na ekranie alertu lub w systemie partnerskim.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Zrzut ekranu pokazujący wykrywanie odchylenia.":::

Nazwa aparatu, który wyzwolił alert, jest wyświetlana pod tytułem alertu.

### <a name="protocol-violation-engine"></a>Aparat naruszenia protokołu 

Naruszenie protokołu występuje, gdy wartości struktury pakietu lub pola nie są zgodne ze specyfikacją protokołu.

Przykładowy scenariusz: *"Niedozwolona operacja Modbus (kod funkcji zero)"* . Ten alert wskazuje, że urządzenie podstawowe wysłało żądanie z kodem funkcji 0 do urządzenia pomocniczego. Ta akcja jest niedozwolona zgodnie ze specyfikacją protokołu, a urządzenie pomocnicze może nieprawidłowo obsłużyć dane wejściowe.

### <a name="policy-violation-engine"></a>Aparat naruszenia zasad

Naruszenie zasad występuje w odróżnieniu od zachowania linii bazowej zdefiniowanej w temacie nauczyne lub skonfigurowane ustawienia.

Przykładowy scenariusz: *"nieautoryzowany agent użytkownika http"* . Ten alert wskazuje na to, że aplikacja, która nie została wybrana lub zatwierdzona przez zasady, jest używana jako klient HTTP na urządzeniu. Może to być Nowa przeglądarka lub aplikacja sieci Web na tym urządzeniu.

### <a name="malware-engine"></a>Aparat złośliwego oprogramowania

Aparat złośliwego oprogramowania wykrywa złośliwe działanie sieciowe.

Przykładowy scenariusz: *"podejrzenie złośliwego działania (Stuxnet)"* . Ten alert wskazuje, że czujnik wykrył podejrzaną aktywność sieci znaną jako powiązaną ze złośliwym oprogramowaniem Stuxnet. Złośliwe oprogramowanie jest zaawansowanym trwałym zagrożeniem, którego celem jest kontrola przemysłowa i sieci SCADA.

### <a name="anomaly-engine"></a>Aparat anomalii

Aparat anomalii wykrywa anomalie w działaniu sieciowym.

Przykładowy scenariusz: *"okresowe zachowanie w kanale komunikacji"* . Składnik sprawdza połączenia sieciowe i wyszukuje okresowe i cykliczne zachowanie transmisji danych. To zachowanie jest typowe w sieciach przemysłowych.

### <a name="operational-engine"></a>Aparat operacyjny

Aparat operacyjny wykrywa zdarzenia operacyjne lub wadliwe jednostki.

Przykładowy scenariusz: *"urządzenie jest podejrzane o odłączenie (bez odpowiedzi)"* alertu. Ten alert jest zgłaszany, gdy urządzenie nie odpowiada żadnemu typowi żądania dla wstępnie zdefiniowanego okresu. Ten alert może wskazywać na zamknięcie urządzenia, odłączenie lub nieprawidłowe działanie.

### <a name="enable-and-disable-engines"></a>Włączanie i wyłączanie aparatów

Po wyłączeniu aparatu zasad informacje generowane przez aparat nie będą dostępne dla czujnika. Jeśli na przykład wyłączysz aparat anomalii, nie będziesz otrzymywać alertów dotyczących anomalii w sieci. Jeśli utworzono regułę przekazywania, anomalie, które nie są wysyłane przez aparat. Aby włączyć lub wyłączyć aparat zasad, wybierz opcję **włączone** lub **wyłączone** dla określonego aparatu.

Ogólny wynik jest wyświetlany w prawym dolnym rogu ekranu **Ustawienia systemu** . Wynik wskazuje procent dostępnej ochrony z włączoną obsługą aparatów ochrony przed zagrożeniami. Każdy silnik ma 20% dostępnej ochrony.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Zrzut ekranu pokazujący wynik.":::

## <a name="configure-dhcp-address-ranges"></a>Skonfiguruj zakresy adresów DHCP

Sieć może zawierać zarówno statyczne, jak i dynamiczne adresy IP. Zwykle adresy statyczne są dostępne w odniesieniu do sieci za pośrednictwem historians, kontrolerów i urządzeń infrastruktury sieciowej, takich jak przełączniki i routery. Dynamiczna alokacja adresów IP jest zwykle implementowana w sieciach gościa przy użyciu laptopów, komputerów, telefonów Smartphone i innych urządzeń przenośnych (przy użyciu połączeń fizycznych Wi-Fi lub sieci LAN w różnych lokalizacjach).

Jeśli pracujesz z sieciami dynamicznymi, będziesz obsługiwać zmiany adresów IP występujące podczas przypisywania nowych adresów IP. W tym celu należy zdefiniować zakresy adresów DHCP.

Mogą wystąpić zmiany, na przykład gdy serwer DHCP przypisuje adresy IP.

Zdefiniowanie dynamicznych adresów IP na każdym czujniku umożliwia kompleksową, przejrzystą obsługę w wystąpieniach zmian adresów IP. Zapewnia to kompleksowe raportowanie dla każdego unikatowego urządzenia.

Konsola czujnika przedstawia najbardziej aktualny adres IP skojarzony z urządzeniem i wskazuje, które urządzenia są dynamiczne. Na przykład:

- Raport wyszukiwania danych i spis urządzeń są konsolidowane wszystkie działania uzyskane z urządzenia jako jedna jednostka, niezależnie od zmiany adresu IP. Te raporty wskazują, które adresy zostały zdefiniowane jako adresy DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Zrzut ekranu przedstawiający spis urządzeń.":::

- Okno **Właściwości urządzenia** wskazuje, czy urządzenie zostało zdefiniowane jako urządzenie DHCP.

Aby ustawić zakres adresów DHCP:

1.  W menu po stronie wybierz pozycję **zakresy DHCP** z okna **Ustawienia systemu** .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Zrzut ekranu pokazujący wybór zakresów DHCP.":::

2.  Zdefiniuj nowy zakres przez ustawienie wartości **z** i **na** wartość.

3.  Opcjonalnie: Zdefiniuj nazwę zakresu, do 256 znaków.

4.  Aby wyeksportować zakresy do pliku CSV, wybierz pozycję **Eksportuj**.

5. Aby ręcznie dodać wiele zakresów z pliku CSV, wybierz pozycję **Importuj** , a następnie wybierz plik.

    > [!NOTE]
    > Zakresy importowane z pliku CSV zastępują istniejące ustawienia zakresu.

6. Wybierz pozycję **Zapisz**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Skonfiguruj serwery DNS do rozpoznawania odwrotnego wyszukiwania

Aby zwiększyć wzbogacenie zasobów, można skonfigurować wiele serwerów DNS do Carryout wyszukiwania wstecznego. Można rozpoznać nazwy hostów lub nazwy FQDN skojarzone z adresami IP wykrytymi w podsieciach sieciowych. Na przykład, jeśli czujnik odnajduje adres IP, może wysyłać zapytania do wielu serwerów DNS w celu rozpoznania nazwy hosta.

Obsługiwane są wszystkie formaty CIDR.

Nazwa hosta zostanie wyświetlona w spisie zasobów i mapie zasobów, a także w raportach.

Harmonogramy rozdzielczości wyszukiwania wstecznego można zaplanować dla określonych interwałów godzinowych, takich jak co 12 godzin. Lub można zaplanować określony czas.

Aby zdefiniować serwery DNS:

1. Wybierz pozycję **Ustawienia systemowe** , a następnie wybierz pozycję **Ustawienia DNS**.

2. Wybierz pozycję **Dodaj serwer DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Zrzut ekranu pokazujący wybór opcji Dodaj serwer DNS.":::

3. W polu **Zaplanuj odwrotne wyszukiwanie DNS** wybierz jedną z opcji:

    - Interwały (za godzinę).
  
    - Określony czas. Użyj formatowania Europejskiego. Na przykład użyj **14:30** , a nie **2:30 PM**.

4. W polu **adres serwera DNS** wprowadź adres IP DNS.

5. W polu **port serwera DNS** Wprowadź port DNS.

6. Rozpoznaj adresy IP sieci z nazwami FQDN zasobów. W polu **Liczba etykiet** Dodaj liczbę wyświetlanych etykiet domeny. Do 30 znaków są wyświetlane od lewej do prawej.

7. W polu **podsieci** wprowadź podsieci, do których serwer DNS ma wykonać zapytanie.

8. Wybierz przełącznik **enable (Włącz** ), jeśli chcesz zainicjować wyszukiwanie wsteczne.

### <a name="test-the-dns-configuration"></a>Testowanie konfiguracji DNS 

Przy użyciu elementu zawartości testowej Sprawdź, czy zdefiniowane ustawienia działają prawidłowo:

1. Włącz przełącznik **wyszukiwania DNS** .

2. Kliknij przycisk **Testuj**.

3. W polu **adres wyszukiwania** wprowadź adres dla okna dialogowego **test wyszukiwania wstecznego DNS dla serwera** .

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Zrzut ekranu pokazujący obszar adresów wyszukiwania.":::

4. Kliknij przycisk **Testuj**.

## <a name="configure-windows-endpoint-monitoring"></a>Konfigurowanie monitorowania punktów końcowych systemu Windows

Korzystając z funkcji monitorowania punktów końcowych systemu Windows, można skonfigurować usługę Azure Defender for IoT, aby wybiórczo sondować systemy Windows. Zapewnia to bardziej ukierunkowane i dokładne informacje o urządzeniach, takich jak poziomy dodatku Service Pack.

Można skonfigurować badanie z określonymi zakresami i hostami, a następnie skonfigurować je do wykonania tylko tak często, jak to konieczne. Przeprowadzasz wybiórczą sondowanie przy użyciu Instrumentacja zarządzania Windows (WMI), czyli standardowego języka skryptów firmy Microsoft do zarządzania systemami Windows.

> [!NOTE]
> - Można uruchomić tylko jedno skanowanie w danym momencie.
> - Uzyskujesz najlepsze wyniki dla użytkowników, którzy mają uprawnienia do domeny lub administratora lokalnego.
> - Przed rozpoczęciem konfigurowania usługi WMI Skonfiguruj regułę zapory, która otwiera ruch wychodzący z czujnika do zeskanowanej podsieci przy użyciu portu UDP 135 i wszystkich portów TCP powyżej 1024.

Po zakończeniu sondowania plik dziennika ze wszystkimi próbami sondowania jest dostępny z poziomu opcji eksportu dziennika. Dziennik zawiera wszystkie adresy IP, które były sondowane. Dla każdego adresu IP dziennik zawiera informacje o powodzeniu i niepowodzeniu. Istnieje również kod błędu, który jest bezpłatnym ciągiem pochodzącym od wyjątku. Skanowanie ostatniego dziennika jest przechowywane w systemie.

Można przeprowadzać zaplanowane skanowanie lub skanowanie ręczne. Po zakończeniu skanowania można wyświetlić wyniki w pliku CSV.

**Wymagania wstępne**

Skonfiguruj regułę zapory, która otwiera ruch wychodzący z czujnika do zeskanowanej podsieci przy użyciu portu UDP 135 i wszystkich portów TCP powyżej 1024.

Aby skonfigurować automatyczne skanowanie:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. Wybierz pozycję **monitorowanie punktów końcowych systemu Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Zrzut ekranu pokazujący wybór monitorowania punktów końcowych systemu Windows.":::

3. W okienku **harmonogram skanowania** Skonfiguruj opcje w następujący sposób:

      - **Według stałych interwałów (w godzinach)**: Ustaw harmonogram skanowania zgodnie z interwałami w godzinach.

      - **Według określonych godzin**: Ustaw harmonogram skanowania zgodnie z określonymi godzinami i wybierz pozycję **Zapisz skanowanie**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Zrzut ekranu pokazujący przycisk Zapisz skanowanie.":::

4. Aby zdefiniować zakres skanowania, wybierz pozycję **Ustaw zakresy skanowania**.

5. Ustaw zakres adresów IP i Dodaj użytkownika i hasło.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Zrzut ekranu pokazujący Dodawanie użytkownika i hasła.":::

6. Aby wykluczyć zakres adresów IP z skanowania, wybierz pozycję **Wyłącz** obok zakresu.

7. Aby usunąć zakres, wybierz pozycję :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: obok zakresu.

8. Wybierz pozycję **Zapisz**. Okno dialogowe **Edytowanie konfiguracji zakresów skanowania** zostanie zamknięte, a w okienku **zakresy skanowania** zostanie wyświetlona liczba zakresów.

Aby przeprowadzić skanowanie ręczne:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. Wybierz pozycję **monitorowanie punktów końcowych systemu Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Zrzut ekranu przedstawiający ekran Instalatora monitorowania punktu końcowego systemu Windows.":::

3. W okienku **Akcje** wybierz pozycję **Rozpocznij skanowanie**. W okienku **Akcje** zostanie wyświetlony pasek stanu zawierający postęp procesu skanowania.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Zrzut ekranu pokazujący przycisk Rozpocznij skanowanie.":::

Aby wyświetlić wyniki skanowania:

1. Po zakończeniu skanowania w okienku **Akcje** wybierz pozycję **Wyświetl wyniki skanowania**. Plik CSV z wynikami skanowania zostanie pobrany na komputer.

## <a name="see-also"></a>Zobacz też

[Badanie wykrywania czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Badanie wykrywania czujników na mapie urządzeń](how-to-work-with-the-sensor-device-map.md)
