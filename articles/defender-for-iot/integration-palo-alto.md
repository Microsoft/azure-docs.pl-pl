---
title: Palo Alto — integracja
description: Usługa Defender for IoT integruje swoją ciągłą platformę monitorowania zagrożeń usługi ICS z zaporami nowej generacji Palo Alto, umożliwiając blokowanie krytycznych zagrożeń, szybsze i wydajniejsze.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783935"
---
# <a name="about-the-palo-alto-integration"></a>Informacje o integracji z programem Palo Alto

Usługa Defender for IoT integruje swoją ciągłą platformę monitorowania zagrożeń usługi ICS z zaporami nowej generacji Palo Alto, umożliwiając blokowanie krytycznych zagrożeń, szybsze i wydajniejsze.

Dostępne są następujące typy integracji:

- Opcja automatycznego blokowania: bezpośrednia usługa Defender dla IoT-Palo Alto Integration

- Wysyłanie zaleceń dotyczących blokowania do centralnego systemu zarządzania: usługa Defender dla integracji z usługą IoT-Panorama

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Konfigurowanie natychmiastowego blokowania przez określony Palo Alto firewall

W sytuacjach krytycznych, takich jak alerty związane z złośliwym oprogramowaniem, można włączyć automatyczne blokowanie. W tym celu należy skonfigurować regułę przekazywania w usłudze Defender for IoT, która wysyła polecenie blokowania bezpośrednio do określonej zapory Palo Alto.

Gdy usługa Defender for IoT identyfikuje krytyczne zagrożenie, wysyła alert zawierający opcję blokowania zainfekowanego źródła. Wybranie **źródła bloku** w szczegółach alertu aktywuje regułę przekazywania, która wysyła polecenie blokowania do określonej zapory Palo Alto.

Aby skonfigurować:

1. W lewym okienku wybierz pozycję **przekazywanie dalej**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="Ekran alertu przesyłania dalej.":::

1. Wybierz pozycję **Utwórz regułę przekazywania**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Utwórz regułę przekazywania":::

1. Aby skonfigurować regułę przekazywania Palo Alto zapory następnej generacji:  
 
   - Zdefiniuj parametry standardowej reguły i w polu listy rozwijanej **Akcje** wybierz pozycję **Wyślij do Palo Alto zapory następnej generacji**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Edytuj regułę przekazywania.":::

1. W okienku Akcje ustaw następujące parametry:

   - **Host**: wprowadź adres IP serwera zapory następnej generacji.
   - **Port**: Wprowadź port serwera zapory następnej generacji.
   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika serwera zapory następnej generacji.
   - **Hasło**: wprowadź hasło serwera zapory następnej generacji.
   - **Konfiguracja**: Skonfiguruj następujące opcje, aby umożliwić blokowanie podejrzanych źródeł przez zaporę Palo Alto:
     - **Blokuj niedozwolone kody funkcji**: naruszenia protokołu — niedozwolona wartość pola narusza specyfikację protokołu ICS (potencjalną lukę w zabezpieczeniach).
     - **Blokuj nieautoryzowane aktualizacje programów PLC/oprogramowania układowego**: nieautoryzowane zmiany PLC.
     - **Blokuj nieautoryzowane zatrzymywanie PLC**: PLC Stop (przestój).
     - **Blokuj alerty dotyczące złośliwego oprogramowania**: blokowanie prób działania w przemyśle (Triton, NotPetya itp.). Można wybrać opcję **automatycznego blokowania**. W takim przypadku blokowanie jest wykonywane automatycznie i od razu.
     - **Blokuj nieautoryzowane skanowanie**: nieautoryzowane skanowanie (potencjalna Rekonesans).
     
1. Wybierz pozycję **Prześlij**.

Aby zablokować podejrzane Źródło: 

1. W okienku **alerty** wybierz alert związany z integracją Palo Alto. Zostanie wyświetlone okno dialogowe **szczegóły alertu** .
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Szczegóły alertu":::

1. Aby automatycznie zablokować podejrzane źródło, wybierz opcję **Źródło bloku**. Zostanie wyświetlone okno dialogowe **Potwierdź** .

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Potwierdź zablokowanie na ekranie Potwierdź.":::

1. W oknie dialogowym **Potwierdź** , wybierz **przycisk OK**. Podejrzane źródło jest blokowane przez zaporę Palo Alto.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Wysyłanie zasad blokowania do Palo Alto panoramy

Usługa Defender dla sieci IoT i Palo Alto ma wbudowaną integrację, która automatycznie tworzy nowe zasady w Palo Alto Networks NMS, Panoramowanie. Ta integracja wymaga potwierdzenia przez administratora panoramy i nie zezwala na automatyczne blokowanie.

Integracja jest przeznaczona dla następujących zdarzeń:

- **Nieautoryzowane zmiany PLC:** Aktualizacja logiki lub oprogramowania układowego urządzenia. Może to reprezentować legalną aktywność lub próbować naruszyć bezpieczeństwo urządzenia. Naruszenie może wystąpić przez wstawienie złośliwego kodu, takiego jak koń trojański dostępu zdalnego (szczur) lub parametry powodujące proces fizyczny, taki jak wirujący turbina, do działania w sposób niebezpieczny.

- **Naruszenie protokołu:** Struktura pakietu lub wartość pola, która narusza specyfikację protokołu. Może to być błędnie skonfigurowana aplikacja lub złośliwa próba zabezpieczenia urządzenia. Na przykład, powodując warunek przepełnienia buforu na urządzeniu docelowym.

- **PLC zatrzymywanie:** Polecenie, które powoduje, że urządzenie przestanie działać, przez co grozi proces fizyczny, który jest kontrolowany przez PLC.

- **Znaleziono złośliwe oprogramowanie w sieci ICS:** Złośliwe oprogramowanie, które manipuluje urządzeniami ICS przy użyciu ich natywnych protokołów, takich jak TRITON i Industroyer. Usługa Defender for IoT wykrywa również złośliwe oprogramowanie IT, które zostało później przeniesione do środowiska ICS i SCADA, takiego jak robak, atak wannacry i NotPetya.

- **Skanowanie złośliwego oprogramowania:** Narzędzia Rekonesans, które zbierają dane dotyczące konfiguracji systemu w fazie przed atakiem. Na przykład koń trojański Havex skanuje sieci przemysłowe w przypadku urządzeń korzystających z usługi OPC, który jest standardowym protokołem używanym przez systemy SCADA opartych na systemie Windows do komunikowania się z urządzeniami ICS.

## <a name="the-process"></a>Proces

Gdy usługa Defender for IoT wykryje wstępnie skonfigurowane przypadki użycia, do alertu zostanie dodany przycisk **źródła bloku** . Następnie, gdy użytkownik **CyberX** wybierze przycisk **blokowania źródła** , usługa Defender for IoT tworzy zasady na panoramze, wysyłając wstępnie zdefiniowaną regułę przekazywania.

Zasady są stosowane tylko wtedy, gdy administrator panoramiczny wypchnięcie go do odpowiednich zapory następnej generacji w sieci.

W sieciach IT mogą istnieć dynamiczne adresy IP. W związku z tym, zasady muszą być oparte na nazwie FQDN (DNS), a nie w adresie IP. Usługa Defender for IoT wykonuje wyszukiwanie wsteczne i dopasowuje urządzenia z dynamicznym adresem IP do nazwy FQDN (nazwa DNS) o każdej skonfigurowanej liczbie godzin.

Ponadto usługa Defender for IoT wysyła wiadomość e-mail do odpowiedniego użytkownika panoramy, aby powiadomić, że nowe zasady utworzone przez usługę Defender for IoT oczekują na zatwierdzenie. Na poniższym rysunku przedstawiono architekturę integracji usługi IoT-Panorama.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Architektura integracji CyberX-panoramy":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Tworzenie zasad blokowania panoramowania w usłudze Defender dla konfiguracji IoT

### <a name="to-configure-dns-lookup"></a>Aby skonfigurować wyszukiwanie DNS

1. W okienku po lewej stronie wybierz pozycję **Ustawienia systemowe**.

1. W okienku **Ustawienia systemu** wybierz pozycję **Ustawienia DNS** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Skonfiguruj ustawienia DNS.":::

1. W oknie dialogowym **Edytowanie ustawień DNS** ustaw następujące parametry:

   - **Stan**: stan programu rozpoznawania nazw DNS.

   - **Adres serwera DNS**: wprowadź adres IP lub nazwę FQDN serwera DNS sieci.
   - **Port serwera DNS**: Wprowadź port używany do wysyłania zapytań do serwera DNS.
   - **Podsieci**: Ustaw zakres podsieci dynamicznego adresu IP. Zakres, którego usługa Defender for IoT odwraca, wyszukuje adres IP na serwerze DNS w celu dopasowania ich do bieżącej nazwy FQDN.
   - **Zaplanuj wyszukiwanie wsteczne**: Zdefiniuj opcje planowania w następujący sposób:
     - Według określonych godzin: Określ, kiedy mają być wykonywane codzienne wyszukiwanie wsteczne.
     - Według stałych interwałów (w godzinach): Ustaw częstotliwość wykonywania wyszukiwania wstecznego.
   - **Liczba etykiet**: poleca usłudze Defender for IoT automatyczne rozpoznawanie sieciowych adresów IP na potrzeby nazw FQDN urządzeń. <br />Aby skonfigurować rozpoznawanie nazw FQDN DNS, Dodaj liczbę wyświetlanych etykiet domeny. Do 30 znaków są wyświetlane od lewej do prawej.
1. Wybierz pozycję **Zapisz**.
1. Aby upewnić się, że ustawienia DNS są poprawne, wybierz pozycję **test wyszukiwania**. Test gwarantuje, że adres IP serwera DNS i port serwera DNS są poprawnie skonfigurowane.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Aby skonfigurować regułę przekazywania, aby blokować podejrzany ruch za pomocą zapory Palo Alto

1. W lewym okienku wybierz pozycję **przekazywanie dalej**. Zostanie wyświetlone okienko przekazywanie.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="Ekran przekazujący.":::

1. W okienku **przekazywanie** wybierz pozycję **Utwórz regułę przekazywania**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Utwórz regułę przekazywania":::

1. Aby skonfigurować regułę przekazywania Palo Alto:

   Zdefiniuj parametry standardowej reguły i w polu listy rozwijanej **Akcje** wybierz pozycję **Wyślij do Palo Alto panoramy**. Zostanie wyświetlone okienko Szczegóły akcji.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Wybierz akcję":::

1. W okienku Akcje ustaw następujące parametry:

   - **Host**: wprowadź adres IP serwera panoramowania.

   - **Port**: Wprowadź port serwera panoramowania.
   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika serwera panoramowania.
   - **Hasło**: wprowadź hasło serwera panoramowania.
   - **Adres raportu**: Zdefiniuj sposób wykonywania blokowania w następujący sposób:
   
     - **Według adresów IP**: zawsze tworzy zasady blokowania na panoramze na podstawie adresu IP.
     
     - **Według nazwy FQDN lub adresu IP**: tworzy zasady blokowania na panoramze na podstawie nazwy FQDN, jeśli istnieje, w przeciwnym razie według adresu IP.
     
   - **Poczta e-mail**: Ustaw adres e-mail powiadomienia e-mail dla zasad
     > [!NOTE]
     > Upewnij się, że skonfigurowano serwer poczty w usłudze Defender for IoT. Jeśli adres e-mail nie zostanie wprowadzony, usługa Defender dla IoT nie wyśle wiadomości e-mail z powiadomieniem.
   - **Wykonaj wyszukiwanie DNS po wykryciu alertu (CheckBox)**: gdy w polu Nazwa FQDN lub adres IP jest ustawiona opcja w adresie w raporcie. To pole wyboru jest domyślnie zaznaczone. Jeśli ustawiono tylko adres IP, ta opcja jest wyłączona.
   - **Konfiguracja**: Skonfiguruj następujące opcje, aby umożliwić blokowanie podejrzanych źródeł przez Palo Alto:
   
     - **Blokuj niedozwolone kody funkcji**: naruszenia protokołu — niedozwolona wartość pola narusza udostępnianie połączenia internetowego, specyfikacja protokołu (potencjalne wykorzystanie).
     
     - **Blokuj nieautoryzowane aktualizacje programów PLC/oprogramowania układowego**: nieautoryzowane zmiany PLC.
     
     - **Blokuj nieautoryzowane zatrzymywanie PLC**: PLC Stop (przestój).
     
     - **Blokuj alerty dotyczące złośliwego oprogramowania**: blokowanie prób działania w przemyśle (Triton, NotPetya itp.). Można wybrać opcję **automatycznego blokowania**. W takim przypadku blokowanie jest wykonywane automatycznie i od razu.
     
     - **Blokuj nieautoryzowane skanowanie**: nieautoryzowane skanowanie (potencjalna Rekonesans).
     
1. Wybierz pozycję **Prześlij**.

### <a name="to-block-the-suspicious-source"></a>Aby zablokować podejrzane Źródło

1. W okienku **alerty** wybierz alert związany z integracją Palo Alto. Zostanie wyświetlone okno dialogowe **szczegóły alertu** .

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Szczegóły alertu":::        

1. Aby automatycznie zablokować podejrzane źródło, wybierz opcję **Źródło bloku**.

1. W oknie dialogowym **Potwierdź** , wybierz **przycisk OK.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirm":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazywać informacje o alertach](how-to-forward-alert-information-to-partners.md).
