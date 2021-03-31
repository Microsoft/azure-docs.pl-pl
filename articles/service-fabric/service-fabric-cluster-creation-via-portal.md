---
title: Tworzenie klastra usługi Service Fabric w witrynie Azure Portal
description: Dowiedz się, jak skonfigurować bezpieczny klaster Service Fabric na platformie Azure przy użyciu Azure Portal i Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563730"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Tworzenie klastra Service Fabric na platformie Azure przy użyciu Azure Portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Witryna Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Jest to przewodnik krok po kroku, który przeprowadzi Cię przez kroki konfigurowania klastra Service Fabric (Linux lub Windows) na platformie Azure przy użyciu Azure Portal. Ten przewodnik przeprowadzi Cię przez następujące kroki:

* Utwórz klaster na platformie Azure za pomocą Azure Portal.
* Uwierzytelnianie administratorów przy użyciu certyfikatów.

> [!NOTE]
> Aby uzyskać bardziej zaawansowane opcje zabezpieczeń, takie jak uwierzytelnianie użytkowników przy użyciu Azure Active Directory i konfigurowania certyfikatów dla zabezpieczeń aplikacji, należy [utworzyć klaster za pomocą Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpieczenia klastra 
W usłudze Service Fabric używa się certyfikatów, aby zapewniać uwierzytelnianie i szyfrowanie w celu zabezpieczania różnych aspektów klastra i jego aplikacji. Aby uzyskać więcej informacji o sposobie wykorzystania certyfikatów w usłudze Service Fabric, zobacz [Scenariusze zabezpieczeń klastra usługi Service Fabric][service-fabric-cluster-security].

Jeśli tworzysz klaster usługi Service Fabric lub wdrażasz klaster do obsługi obciążeń testowych, możesz przejść do następnej sekcji (**utworzyć klaster w Azure Portal**), a system generować certyfikaty potrzebne dla klastrów, które uruchamiają obciążenia testowe. W przypadku konfigurowania klastra dla obciążeń produkcyjnych, należy kontynuować odczytywanie.

#### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagany)
Ten certyfikat jest wymagany do zabezpieczenia klastra i uniemożliwia nieautoryzowany dostęp do niego. Zapewnia zabezpieczenia klastra na kilka sposobów:

* **Uwierzytelnianie klastra:** Uwierzytelnia komunikację między węzłami w ramach Federacji klastra. Do klastra można dołączyć tylko węzły, które mogą udowodnić swoją tożsamość za pomocą tego certyfikatu.
* **Uwierzytelnianie serwera:** Uwierzytelnia punkty końcowe zarządzania klastrami na kliencie zarządzania programu, dzięki czemu klient zarządzający wie o rzeczywistym klastrze. Ten certyfikat zapewnia również protokół TLS dla interfejsu API zarządzania HTTPS i dla Service Fabric Explorer za pośrednictwem protokołu HTTPS.

Aby można było zrealizować te cele, certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Należy utworzyć certyfikat do wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* **Nazwa podmiotu certyfikatu musi być zgodna z domeną** używaną do uzyskiwania dostępu do klastra Service Fabric. Jest to wymagane w celu udostępnienia protokołu TLS dla punktów końcowych zarządzania HTTPS klastra i Service Fabric Explorer. Nie można uzyskać certyfikatu TLS/SSL z urzędu certyfikacji dla `.cloudapp.azure.com` domeny. Uzyskaj niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu z urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną przez klaster.

#### <a name="client-authentication-certificates"></a>Certyfikaty uwierzytelniania klienta
Dodatkowe certyfikaty klienta uwierzytelniają administratorów do zadań zarządzania klastrami. Service Fabric ma dwa poziomy dostępu: **administrator** i **użytkownik tylko do odczytu**. Należy użyć co najmniej jednego certyfikatu na potrzeby dostępu administracyjnego. Aby uzyskać dodatkowy dostęp na poziomie użytkownika, należy podać oddzielny certyfikat. Aby uzyskać więcej informacji na temat ról dostępu, zobacz [kontroli dostępu opartej na rolach dla klientów Service Fabric][service-fabric-cluster-security-roles].

Nie ma potrzeby przekazywania certyfikatów uwierzytelniania klienta w celu Key Vault pracy z Service Fabric. Te certyfikaty należy dostarczyć tylko użytkownikom autoryzowanym do zarządzania klastrami. 

> [!NOTE]
> Azure Active Directory jest zalecanym sposobem uwierzytelniania klientów na potrzeby operacji zarządzania klastrami. Aby użyć Azure Active Directory, należy [utworzyć klaster przy użyciu Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów może być zainstalowana w klastrze na potrzeby zabezpieczeń aplikacji. Przed utworzeniem klastra należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają zainstalowania certyfikatu w węzłach, na przykład:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji
* Szyfrowanie danych między węzłami podczas replikacji 

Nie można skonfigurować certyfikatów aplikacji podczas [tworzenia klastra za pomocą Azure Portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Aby skonfigurować certyfikaty aplikacji w czasie instalacji klastra, należy [utworzyć klaster przy użyciu Azure Resource Manager][create-cluster-arm]. Możesz również dodać certyfikaty aplikacji do klastra po jego utworzeniu.

## <a name="create-cluster-in-the-azure-portal"></a>Utwórz klaster w Azure Portal

Utworzenie klastra produkcyjnego w celu spełnienia wymagań dotyczących aplikacji obejmuje pewne planowanie, dlatego zdecydowanie zalecamy zapoznanie się z dokumentem [zagadnienia dotyczące planowania Service Fabric klastra][service-fabric-cluster-capacity] i jego zrozumienie. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Wyszukaj zasób klastra Service Fabric

Zaloguj się w witrynie [Azure Portal][azure-portal].
Kliknij pozycję **Utwórz zasób** , aby dodać nowy szablon zasobu. Wyszukaj szablon klastra Service Fabric w **portalu Marketplace** w obszarze **wszystko**.
Wybierz z listy pozycję **klaster Service Fabric** .

![Wyszukaj Service Fabric szablon klastra na Azure Portal.][SearchforServiceFabricClusterTemplate]

Przejdź do bloku **klaster Service Fabric** i kliknij przycisk **Utwórz**.

Blok **Utwórz klaster Service Fabric** obejmuje następujące cztery kroki:

### <a name="1-basics"></a>1. podstawy
![Zrzut ekranu przedstawiający tworzenie nowej grupy zasobów.][CreateRG]

W bloku podstawowe należy podać podstawowe informacje o klastrze.

1. Wprowadź nazwę klastra.
2. Wprowadź **nazwę użytkownika** i **hasło** dla Pulpit zdalny maszyn wirtualnych.
3. Upewnij się, że wybrano **subskrypcję** , w której ma zostać wdrożony klaster, szczególnie jeśli masz wiele subskrypcji.
4. Utwórz nową **grupę zasobów**. Najlepszym rozwiązaniem jest podanie tej samej nazwy co klaster, ponieważ ułatwia ona znalezienie ich później, szczególnie w przypadku próby wprowadzenia zmian we wdrożeniu lub usunięciu klastra.
   
   > [!NOTE]
   > Chociaż można zdecydować się na korzystanie z istniejącej grupy zasobów, dobrym sposobem jest utworzenie nowej grupy zasobów. Dzięki temu można łatwo usuwać klastry i wszystkie zasoby, z których korzysta.
   > 
   > 
5. Wybierz **lokalizację** , w której chcesz utworzyć klaster. Jeśli planujesz użyć istniejącego certyfikatu, który został już przekazany do magazynu kluczy, musisz użyć tego samego regionu, w którym znajduje się Twój Magazyn kluczy. 

### <a name="2-cluster-configuration"></a>2. Konfiguracja klastra
![Tworzenie typu węzła][CreateNodeType]

Skonfiguruj węzły klastra. Typy węzłów definiują rozmiary maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Klaster może mieć więcej niż jeden typ węzła, ale typ węzła podstawowego (pierwszy zdefiniowany w portalu) musi mieć co najmniej pięć maszyn wirtualnych, ponieważ jest to typ węzła, w którym są umieszczane Service Fabric usługi systemowe. Nie Konfiguruj **Właściwości umieszczania** , ponieważ domyślna właściwość położenia "NodeTypeName" jest dodawana automatycznie.

> [!NOTE]
> Typowy scenariusz dla wielu typów węzłów to aplikacja, która zawiera usługę frontonu i usługę zaplecza. Chcesz umieścić usługę frontonu na mniejszych maszynach wirtualnych (takich jak D2_V2) z portami otwartymi w Internecie i umieścić usługę zaplecza na większych maszynach wirtualnych (z rozmiarami maszyn wirtualnych, takimi jak D3_V2, D6_V2, D15_V2 itd.) bez otwartych portów internetowych.
> 

1. Wybierz nazwę typu węzła (od 1 do 12 znaków zawierających tylko litery i cyfry).
2. Minimalny **rozmiar** maszyn wirtualnych dla typu węzła podstawowego jest uzależniony od **warstwy trwałości** wybranej dla klastra. Wartością domyślną dla warstwy trwałości jest brązowy. Aby uzyskać więcej informacji na temat trwałości, zobacz [jak wybrać Service Fabric trwałość klastra][service-fabric-cluster-durability].
3. Wybierz **rozmiar maszyny wirtualnej**. Maszyny wirtualne z serii D mają dyski SSD i są zdecydowanie zalecane w przypadku aplikacji stanowych. Nie należy używać żadnej jednostki SKU maszyny wirtualnej, która ma częściowe rdzenie lub nie może być mniejsza niż 10 GB dostępnego miejsca na dysku. Zapoznaj się z [dokumentem uwagi dotyczące planowania klastrów usługi Service Fabric][service-fabric-cluster-capacity] , aby uzyskać pomoc w wyborze rozmiaru maszyny wirtualnej.
4.  **Klaster z pojedynczym węzłem i trzy klastry węzłów** są przeznaczone tylko do użytku testowego. Nie są one obsługiwane w przypadku żadnych uruchomionych obciążeń produkcyjnych.
5. Wybierz **początkową pojemność zestawu skalowania maszyn wirtualnych** dla typu węzła. Możesz skalować w górę lub w dół liczbę maszyn wirtualnych w typie węzła w późniejszym czasie, ale na podstawowym typie węzła minimalnym jest pięć dla obciążeń produkcyjnych. Inne typy węzłów mogą mieć co najmniej jedną maszynę wirtualną. Minimalna **Liczba** maszyn wirtualnych dla typu węzła podstawowego jest **niezawodnością** klastra.  
6. Skonfiguruj **niestandardowe punkty końcowe**. W tym polu można wprowadzić rozdzieloną przecinkami listę portów, które mają zostać ujawnione za pomocą Azure Load Balancer do publicznego Internetu dla aplikacji. Na przykład jeśli planujesz wdrożyć aplikację sieci Web w klastrze, wprowadź "80" tutaj, aby zezwolić na ruch na porcie 80 do klastra. Aby uzyskać więcej informacji na temat punktów końcowych, zobacz [komunikowanie się z aplikacjami][service-fabric-connect-and-communicate-with-services]
7. **Włącz zwrotny serwer proxy**.  [Service Fabric odwrotny serwer proxy](service-fabric-reverseproxy.md) pomaga mikrousługi działające w klastrze Service Fabric odnajdywać i komunikować się z innymi usługami, które mają punkty końcowe http.
8. Z powrotem w bloku **Konfiguracja klastra** w obszarze **+ Pokaż ustawienia opcjonalne** Skonfiguruj **diagnostykę** klastra. Domyślnie Diagnostyka jest włączona w klastrze, aby pomóc w rozwiązywaniu problemów. Jeśli chcesz wyłączyć diagnostykę, Zmień **stan** na **wyłączony**. Wyłączenie diagnostyki **nie** jest zalecane. Jeśli utworzono już projekt Application Insights, podaj jego klucz, aby ślady aplikacji były kierowane do niej.
9. **Uwzględnij usługę DNS**.  [Usługa DNS](service-fabric-dnsservice.md) to opcjonalna usługa, która umożliwia znalezienie innych usług przy użyciu protokołu DNS.
10. Wybierz **tryb uaktualniania sieci szkieletowej** , na który ma zostać ustawiony klaster. Wybierz **opcję automatycznie,** Jeśli chcesz, aby system automatycznie pobierał najnowszą dostępną wersję, a następnie spróbuj uaktualnić do niej klaster. Jeśli chcesz wybrać obsługiwaną wersję, ustaw tryb na **ręczny**. Aby uzyskać więcej informacji na temat trybu uaktualniania sieci szkieletowej, zobacz [dokument Uaktualnij klaster Service Fabric.][service-fabric-cluster-upgrade]

> [!NOTE]
> Obsługiwane są tylko klastry z obsługiwanymi wersjami Service Fabric. Wybierając tryb **ręczny** , podejmujesz odpowiedzialność za uaktualnienie klastra do obsługiwanej wersji.
> 

### <a name="3-security"></a>3. zabezpieczenia
![Zrzut ekranu konfiguracji zabezpieczeń na Azure Portal.][BasicSecurityConfigs]

Aby łatwo skonfigurować bezpieczny klaster testowy dla Ciebie, udostępniamy opcję **podstawowa** . Jeśli masz już certyfikat i przekazano go do [magazynu kluczy](../key-vault/index.yml) (i włączono Magazyn kluczy na potrzeby wdrożenia), użyj opcji **niestandardowej**

#### <a name="basic-option"></a>Opcja podstawowa
Postępuj zgodnie z ekranami, aby dodać lub użyć istniejącego magazynu kluczy i dodać certyfikat. Dodanie certyfikatu jest procesem synchronicznym i dlatego trzeba będzie czekać na utworzenie certyfikatu.

Odporne na Temptation z ekranu do momentu ukończenia poprzedniego procesu.

![Zrzut ekranu przedstawia stronę zabezpieczenia z wybraną opcją podstawowa z panelem Magazyn kluczy i utworzeniem okienka Magazyn kluczy.][CreateKeyVault]

Po utworzeniu magazynu kluczy Edytuj zasady dostępu dla magazynu kluczy. 

![Zrzut ekranu przedstawia okienko Utwórz klaster Service Fabric z wybraną opcją 3 zabezpieczenia i wyjaśnienie, że magazyn kluczy nie jest włączony.][CreateKeyVault2]

Kliknij pozycję **Edytuj zasady dostępu**, a następnie **Pokaż zaawansowane zasady dostępu** i Włącz dostęp do usługi Azure Virtual Machines na potrzeby wdrożenia. Zalecane jest również włączenie wdrożenia szablonu. Po dokonaniu wyboru nie zapomnij kliknąć przycisk **Zapisz** i zamknąć okno **zasady dostępu** .

![Zrzut ekranu przedstawia okienko Utwórz klaster Service Fabric z otwartym okienkiem zabezpieczeń i otwartym okienkiem zasady dostępu.][CreateKeyVault3]

Wprowadź nazwę certyfikatu, a następnie kliknij przycisk **OK**.

![Zrzut ekranu przedstawia okienko Utwórz klaster Service Fabric z zabezpieczeniami wybranymi jak wcześniej, ale bez wyjaśnienia, że magazyn kluczy nie jest włączony.][CreateKeyVault4]

#### <a name="custom-option"></a>Opcja niestandardowa
Pomiń tę sekcję, jeśli wykonano już kroki z opcji **podstawowa** .

![Zrzut ekranu przedstawia okno dialogowe Konfiguracja zabezpieczeń klastra ustawienia zabezpieczeń.][SecurityCustomOption]

Do ukończenia strony zabezpieczenia potrzebny jest źródłowy Magazyn kluczy, adres URL certyfikatu i informacje o odcisku palca certyfikatu. Jeśli go nie masz, Otwórz inne okno przeglądarki i w Azure Portal wykonaj następujące czynności

1. Przejdź do usługi magazynu kluczy.
2. Wybierz kartę "właściwości" i skopiuj "Identyfikator zasobu" do "Magazyn kluczy źródłowych" w innym oknie przeglądarki 

    ![Zrzut ekranu przedstawia okno Właściwości magazynu kluczy.][CertInfo0]

3. Teraz wybierz kartę "certyfikaty".
4. Kliknij pozycję odcisk palca certyfikatu, co spowoduje przejście do strony wersje.
5. Kliknij identyfikatory GUID widoczne w bieżącej wersji.

    ![Zrzut ekranu przedstawia okno certyfikatu dla magazynu kluczy][CertInfo1]

6. Teraz powinieneś znajdować się na ekranie poniżej. Skopiuj wartość szesnastkowego odcisku palca SHA-1 do "odcisk palca certyfikatu" w innym oknie przeglądarki
7. Skopiuj wartość "Identyfikator wpisu tajnego" do "adresu URL certyfikatu" w innym oknie przeglądarki.

    ![Zrzut ekranu przedstawia okno dialogowe wersja certyfikatu z opcją kopiowania identyfikatora certyfikatu.][CertInfo2]

Zaznacz pole **Konfiguruj ustawienia zaawansowane** , aby wprowadzić certyfikaty klienta dla **klienta administratora** i **klienta tylko do odczytu**. W tych polach Wprowadź odcisk palca certyfikatu klienta administratora i odcisk palca certyfikatu klienta użytkownika tylko do odczytu, jeśli ma to zastosowanie. Gdy administratorzy podejmują próbę nawiązania połączenia z klastrem, uzyskują dostęp tylko wtedy, gdy mają certyfikat z odciskiem palca, który odpowiada wartościom odcisku palca wprowadzonym w tym miejscu.  

### <a name="4-summary"></a>4. Podsumowanie

Teraz możesz przystąpić do wdrożenia klastra. Przed wykonaniem tych czynności Pobierz certyfikat, a następnie poszukaj w obszarze dużego, niebieskiego pola informacyjnego linku. Pamiętaj, aby zachować certyfikat w bezpiecznym miejscu. musisz połączyć się z klastrem. Ponieważ pobrany certyfikat nie ma hasła, zaleca się dodanie go.

Aby ukończyć tworzenie klastra, kliknij przycisk **Utwórz**. Opcjonalnie możesz pobrać szablon.

![Zrzut ekranu przedstawia stronę tworzenia podsumowania klastra Service Fabric z linkiem umożliwiającym wyświetlanie i pobieranie certyfikatu.][Summary]

Możesz zobaczyć postępy tworzenia w powiadomieniach. (Kliknij ikonę "dzwonka" obok paska stanu w prawym górnym rogu ekranu). Jeśli kliknięto opcję **Przypnij do tablicy startowej** podczas tworzenia klastra, zobaczysz **wdrożenie Service Fabric klaster** przypięty do tablicy **startowej** . Ten proces zajmie trochę czasu. 

Aby wykonywać operacje zarządzania w klastrze przy użyciu programu PowerShell lub interfejsu wiersza polecenia, musisz nawiązać połączenie z klastrem, aby dowiedzieć się więcej na temat [nawiązywania połączenia z klastrem](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Wyświetlanie stanu klastra
![Zrzut ekranu przedstawiający szczegóły klastra na pulpicie nawigacyjnym.][ClusterDashboard]

Po utworzeniu klastra można sprawdzić klaster w portalu:

1. Przejdź do pozycji **Przeglądaj** i kliknij pozycję **Service Fabric klastrów**.
2. Znajdź klaster i kliknij go.
3. Możesz teraz wyświetlić szczegóły klastra na pulpicie nawigacyjnym, w tym publiczny punkt końcowy klastra oraz link do narzędzia Service Fabric Explorer.

Sekcja **monitor węzłów** w bloku pulpitu nawigacyjnego klastra wskazuje liczbę maszyn wirtualnych, które są w dobrej kondycji i nie są w dobrej kondycji. Więcej szczegółowych informacji o kondycji klastra można znaleźć na stronie [Service Fabric modelu kondycji][service-fabric-health-introduction].

> [!NOTE]
> Klastry Service Fabric wymagają, aby pewna liczba węzłów zawsze była w stanie zapewnić dostępność i zachować stan — nazywaną "konserwacją kworum". W związku z tym zazwyczaj nie jest bezpiecznie zamykać wszystkich maszyn w klastrze, chyba że najpierw wykonano [pełną kopię zapasową stanu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Połączenie zdalne z wystąpieniem zestawu skalowania maszyn wirtualnych lub węzłem klastra
Każdy elementów NodeType określony w klastrze powoduje pobranie konfiguracji zestawu skalowania maszyn wirtualnych. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Następne kroki
W tym momencie istnieje bezpieczny klaster używający certyfikatów do uwierzytelniania zarządzania. Następnie połącz się z [klastrem](service-fabric-connect-to-secure-cluster.md) i Dowiedz się, jak zarządzać wpisami [tajnymi aplikacji](service-fabric-application-secret-management.md).  Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
