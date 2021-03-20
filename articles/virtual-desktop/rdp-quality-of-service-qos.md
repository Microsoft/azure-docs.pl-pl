---
title: Implementowanie Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza)
titleSuffix: Azure
description: Jak skonfigurować jakość usługi (wersja zapoznawcza) dla pulpitu wirtualnego systemu Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94639376"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementowanie Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza)

> [!IMPORTANT]
> Obsługa zasad Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Protokół RDP shortpath](./shortpath.md) zapewnia bezpośrednią transport oparty na protokole UDP między klientem pulpit zdalny a hostem sesji. Shortpath RDP włącza konfigurację zasad Quality of Service (QoS) dla danych RDP.
Jakość usług pulpitu wirtualnego systemu Windows umożliwia ruch RDP w czasie rzeczywistym, który jest wrażliwy na opóźnienia sieci na "wycinanie" przed ruchem, który jest mniej wrażliwy. Przykładem takiego mniejszego natężenia ruchu jest pobranie nowej aplikacji, gdzie bardzo druga część do pobrania nie jest znaczną. Funkcja QoS używa obiektów zasady grupy systemu Windows do identyfikowania i oznaczania wszystkich pakietów w strumieniach w czasie rzeczywistym i zapewnienia przez sieć ruchu RDP dedykowanej części przepustowości.

W przypadku obsługi dużej grupy użytkowników z problemami opisanymi w tym artykule prawdopodobnie trzeba będzie zaimplementować usługę QoS. Mała firma z kilkoma użytkownikami może nie potrzebować jakości usług (QoS), ale powinna być również przydatna.

Bez żadnej formy QoS mogą wystąpić następujące problemy:

* Wahanie — pakiety RDP docierające o różne stawki, co może skutkować błędami wizualizacji i dźwięku
* Utrata pakietów — liczba porzuconych pakietów, która powoduje ponowną transmisję wymagającą dodatkowego czasu
* Opóźniony czas błądzenia (RTT) — pakiety RDP zajmują dużo czasu, aby dotrzeć do miejsc docelowych, co powoduje zauważalne opóźnienia między danymi wejściowymi i odpowiedzią z aplikacji zdalnej.

Najmniej skomplikowanym sposobem rozwiązania tych problemów jest zwiększenie rozmiaru połączenia danych, zarówno wewnętrznie, jak i do Internetu. Ze względu na to, że jest to często niepotrzebne, Usługa QoS umożliwia zarządzanie zasobami, które nie zwiększają przepustowości. Aby rozwiązać problemy z jakością, zalecamy najpierw użyć funkcji QoS, a następnie dodać przepustowość tylko tam, gdzie to konieczne.

Aby jakość usług (QoS) była skuteczna, należy zastosować spójne ustawienia QoS w całej organizacji. Każda część ścieżki, która nie obsługuje priorytetów QoS, może obniżyć jakość sesji RDP.

## <a name="introduction-to-qos-queues"></a>Wprowadzenie do kolejek QoS

Aby zapewnić jakość usług (QoS), urządzenia sieciowe muszą mieć sposób klasyfikowania ruchu i muszą mieć możliwość rozróżnienia protokołu RDP od innego ruchu sieciowego.

Gdy ruch sieciowy przechodzi do routera, ruch jest umieszczany w kolejce. Jeśli nie skonfigurowano zasad QoS, istnieje tylko jedna kolejka, a wszystkie dane są traktowane jako pierwsze, a pierwszy z nich ma ten sam priorytet. Oznacza to, że ruch RDP może być zablokowany za ruchem, gdy kilka dodatkowych milisekund nie będzie problemem.

Podczas implementowania usługi QoS należy zdefiniować wiele kolejek przy użyciu jednej z kilku funkcji zarządzania przeciążeniami, takich jak kolejkowanie priorytetów firmy Cisco i [oparte na klasach](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) funkcje zawieszania (CBWFQ) i unikania przeciążenia, takie jak [ważone losowe wykrywanie wczesne (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Prostą analogiczną jest to, że QoS tworzy wirtualne "Carpool tory" w sieci danych. W związku z tym niektóre typy danych nigdy lub rzadko napotykają opóźnienia. Po utworzeniu tych ścieżek można dostosować ich rozmiar względny i znacznie bardziej efektywnie zarządzać przepustowością połączenia, która jest nadal dostępna dla użytkowników w organizacji.

## <a name="qos-implementation-checklist"></a>Lista kontrolna implementacji QoS

Na wysokim poziomie należy wykonać następujące czynności w celu zaimplementowania funkcji QoS:

1. [Upewnij się, że sieć jest gotowa](#make-sure-your-network-is-ready)
2. [Upewnij się, że SHORTPATH RDP jest włączona](./shortpath.md) — zasady QoS nie są obsługiwane w przypadku transportu odwrotnego
3. [Zaimplementuj Wstawianie ZNACZNIKÓW DSCP](#insert-dscp-markers) na hostach sesji

Podczas przygotowywania do implementacji usługi QoS należy pamiętać o następujących kwestiach:

* Najkrótsza ścieżka hosta sesji jest Najlepsza
* Nie zaleca się żadnych przeszkód między, takimi jak serwery proxy lub urządzenia do inspekcji pakietów

## <a name="make-sure-your-network-is-ready"></a>Upewnij się, że sieć jest gotowa

Jeśli rozważasz implementację QoS, należy określić wymagania dotyczące przepustowości i inne [wymagania dotyczące sieci](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
Przeciążenie ruchu w sieci znacznie wpłynie na jakość multimediów. Brak przepustowości prowadzi do obniżenia wydajności i słabego środowiska użytkownika. Jako że wdrażanie i użycie pulpitu wirtualnego systemu Windows rośnie, użyj [log Analytics](./diagnostics-log-analytics.md) , aby identyfikować problemy, a następnie wprowadzać zmiany przy użyciu opcji QoS i selektywne Dodawanie przepustowości.

### <a name="vpn-considerations"></a>Zagadnienia dotyczące sieci VPN

Jakość usług (QoS) działa zgodnie z oczekiwaniami w przypadku wdrożenia na wszystkich łączach między klientami a hostami sesji. W przypadku korzystania z funkcji QoS w sieci wewnętrznej, gdy użytkownik loguje się z lokalizacji zdalnej, można tylko określić priorytety w wewnętrznej, zarządzanej sieci. Mimo że zdalne lokalizacje mogą odbierać zarządzane połączenie przez implementację wirtualnej sieci prywatnej (VPN), Sieć VPN z nieodłącznie dodaje obciążenie pakietu i tworzy opóźnienia w ruchu w czasie rzeczywistym.

W organizacji globalnej z łączami zarządzanymi obejmującymi kontynenty zdecydowanie zaleca się użycie funkcji QoS, ponieważ przepustowość dla tych linków jest ograniczona w porównaniu do sieci LAN.

## <a name="insert-dscp-markers"></a>Wstaw znaczniki DSCP

Można zaimplementować QoS przy użyciu obiektu zasady grupy (GPO), aby kierować hosty sesji w celu wstawienia znacznika DSCP w nagłówkach pakietów IP identyfikując go jako określony typ ruchu. Routery i inne urządzenia sieciowe można skonfigurować tak, aby rozpoznawać te oznaczenia i umieścić ruch w oddzielnym, wyższym priorytecie kolejki.

Znaczniki DSCP można porównać z sygnaturami pocztowymi, które wskazują pracownikom, jak pilne jest dostarczanie i jak najlepiej sortować je w celu przyspieszenia dostarczania. Po skonfigurowaniu sieci w celu zapewnienia priorytetu strumieniom RDP, utraconych pakietów i opóźnionych pakietów należy znacznie zmniejszyć.

Gdy wszystkie urządzenia sieciowe korzystają z tych samych klasyfikacji, oznaczeń i priorytetów, możliwe jest zmniejszenie lub wyeliminowanie opóźnień, porzucanie pakietów i wahania. W perspektywie protokołu RDP istotny krok konfiguracji to Klasyfikacja i oznaczenie pakietów. Aby jednak kompleksowe ustawienia QoS powiodło się, należy uważnie wyrównać konfigurację protokołu RDP z podstawową konfiguracją sieci.
Wartość DSCP informuje odpowiednio skonfigurowaną sieć, której priorytet ma być przydany do pakietu lub strumienia.

Zalecamy użycie wartości DSCP 46, która jest mapowana na **przyspieszone przekazanie dalej (EF)** klasy DSCP.

### <a name="implement-qos-on-session-host-using-group-policy"></a>Zaimplementuj QoS na hoście sesji przy użyciu zasady grupy

Aby ustawić wstępnie zdefiniowaną wartość DSCP, można użyć Quality of Service opartych na zasadach (QoS) w zasady grupy.

Aby utworzyć zasady QoS dla hostów sesji przyłączonych do domeny, należy najpierw zalogować się na komputerze, na którym zainstalowano zasady grupy zarządzaniem. Otwórz przystawkę Zarządzanie zasady grupyami (wybierz pozycję Start, wskaż pozycję Narzędzia administracyjne, a następnie wybierz pozycję zasady grupy Management), a następnie wykonaj następujące czynności:

1. W obszarze Zarządzanie zasady grupy zlokalizuj kontener, w którym mają zostać utworzone nowe zasady. Na przykład jeśli cała sesja zawiera komputery znajdujące się w jednostce organizacyjnej o nazwie **"hosty sesji"**, należy utworzyć nowe zasady w sesji HOSTY jednostki organizacyjnej.

2. Kliknij prawym przyciskiem myszy odpowiedni kontener, a następnie wybierz pozycję **Utwórz obiekt zasad grupy w tej domenie i umieść tu łącze**.

3. W oknie dialogowym **nowy obiekt zasad grupy** wpisz nazwę nowego obiektu zasady grupy w polu **Nazwa** , a następnie wybierz przycisk **OK**.

4. Kliknij prawym przyciskiem myszy nowo utworzone zasady, a następnie wybierz polecenie **Edytuj**.

5. W Edytor zarządzania zasadami grupy rozwiń węzeł **Konfiguracja komputera**, rozwiń węzeł **Ustawienia systemu Windows**, kliknij prawym przyciskiem myszy pozycję **QoS oparte na zasadach**, a następnie wybierz pozycję **Utwórz nowe zasady**.

6. W oknie dialogowym **ustawienia QoS oparte na zasadach** na stronie Otwieranie wpisz nazwę nowych zasad w polu **Nazwa** . Wybierz pozycję **Określ wartość DSCP** i ustaw wartość na **46**. Pozostaw pozycję Określ niewybraną **szybkość ograniczania ruchu wychodzącego** , a następnie wybierz pozycję **dalej**.

7. Na następnej stronie wybierz **tylko aplikacje o tej nazwie pliku wykonywalnego** i wprowadź nazwę **svchost.exe**, a następnie wybierz przycisk **dalej**. To ustawienie powoduje, że zasady umożliwiają tylko określanie priorytetów pasującego ruchu z usługi Pulpit zdalny.

8. Na trzeciej stronie upewnij się, że jest zaznaczony zarówno **Dowolny źródłowy adres IP** , jak i **dowolny docelowy adres IP** , a następnie wybierz przycisk **dalej**. Te dwa ustawienia zapewniają, że pakiety będą zarządzane niezależnie od tego, który komputer (adres IP) wysłał pakiety, a komputer (adres IP) otrzyma pakiety.

9. Na stronie cztery wybierz pozycję **UDP** z listy rozwijanej **Wybierz protokół, którego dotyczą te zasady QoS** .

10. Pod nagłówkiem **Określ numer portu źródłowego**, wybierz **z tego portu źródłowego lub zakresu**. W polu tekstowym towarzyszące wpisz **3390**. Wybierz pozycję **Zakończ**.

Nowo utworzone zasady nie zaczną obowiązywać przed odświeżeniem zasady grupy na komputerach hosta sesji. Mimo że zasady grupy okresowo odświeża własne, można wymusić natychmiastowe odświeżanie, wykonując następujące czynności:

1. Na każdym hoście sesji, dla którego chcesz odświeżyć zasady grupy, Otwórz wiersz polecenia jako administrator (*Uruchom jako administrator*).

1. W wierszu polecenia wprowadź

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementowanie QoS na hoście sesji przy użyciu programu PowerShell

Za pomocą polecenia cmdlet programu PowerShell poniżej można ustawić ustawienia QoS shortpath dla protokołu RDP:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Pokrewne artykuły:

* [Zasady Quality of Service (QoS)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wymaganiach dotyczących przepustowości dla pulpitu wirtualnego systemu Windows, zobacz [Opis wymagań dotyczących przepustowości Remote Desktop Protocol (RDP) dla pulpitu wirtualnego systemu Windows](rdp-bandwidth.md).
* Aby dowiedzieć się więcej o łączności z siecią pulpitu wirtualnego systemu Windows, zobacz [Opis łączności z siecią pulpitu wirtualnego systemu Windows](network-connectivity.md).
