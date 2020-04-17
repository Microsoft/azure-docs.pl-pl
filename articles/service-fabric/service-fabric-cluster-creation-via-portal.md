---
title: Tworzenie klastra usługi Service Fabric w witrynie Azure Portal
description: Dowiedz się, jak skonfigurować bezpieczny klaster sieci szkieletowej usług na platformie Azure przy użyciu witryny Azure portal i usługi Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: e0cd3d5e5a37720134a5bce596bba211b375f19d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458321"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Tworzenie klastra sieci szkieletowej usług na platformie Azure przy użyciu portalu Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Jest to przewodnik krok po kroku, który przeprowadzi Cię przez kroki konfigurowania klastra sieci szkieletowej usług (Linux lub Windows) na platformie Azure przy użyciu witryny Azure portal. W tym przewodniku przedstawiono następujące kroki:

* Tworzenie klastra na platformie Azure za pośrednictwem witryny Azure Portal.
* Uwierzytelnij administratorów przy użyciu certyfikatów.

> [!NOTE]
> Aby uzyskać bardziej zaawansowane opcje zabezpieczeń, takie jak uwierzytelnianie użytkowników za pomocą usługi Azure Active Directory i konfigurowanie certyfikatów dla zabezpieczeń aplikacji, [utwórz klaster przy użyciu usługi Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpieczenia klastra 
W usłudze Service Fabric używa się certyfikatów, aby zapewniać uwierzytelnianie i szyfrowanie w celu zabezpieczania różnych aspektów klastra i jego aplikacji. Aby uzyskać więcej informacji o sposobie wykorzystania certyfikatów w usłudze Service Fabric, zobacz [Scenariusze zabezpieczeń klastra usługi Service Fabric][service-fabric-cluster-security].

Jeśli jest to pierwszy raz, gdy tworzysz klaster sieci szkieletowej usług lub wdrażasz klaster dla obciążeń testowych, możesz przejść do następnej sekcji **(Utwórz klaster w witrynie Azure Portal)** i mieć system generuje certyfikaty potrzebne dla klastrów, które uruchamiają obciążenia testowe. Jeśli konfigurujesz klaster dla obciążeń produkcyjnych, kontynuuj czytanie.

#### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagany)
Ten certyfikat jest wymagany do zabezpieczenia klastra i zapobiegania nieautoryzowanemu dostępowi do niego. Zapewnia bezpieczeństwo klastra na kilka sposobów:

* **Uwierzytelnianie klastra:** Uwierzytelnia komunikację węzeł-węzeł dla federacji klastra. Tylko węzły, które mogą udowodnić swoją tożsamość za pomocą tego certyfikatu, mogą dołączyć do klastra.
* **Uwierzytelnianie serwera:** Uwierzytelnia punkty końcowe zarządzania klastrem do klienta zarządzania, dzięki czemu klient zarządzania wie, że rozmawia z prawdziwym klastrem. Ten certyfikat zapewnia również protokół TLS dla interfejsu API zarządzania HTTPS i Eksploratora sieci szkieletowej usług za pośrednictwem protokołu HTTPS.

Aby służyć tym celom, świadectwo musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzony w celu wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* Nazwa podmiotu certyfikatu **musi być zgodna z domeną** używaną do uzyskiwania dostępu do klastra sieci szkieletowej usług. Jest to wymagane do zapewnienia protokołu TLS dla punktów końcowych zarządzania HTTPS klastra i Eksploratora sieci szkieletowej usług. Nie można uzyskać certyfikatu TLS/SSL od urzędu `.cloudapp.azure.com` certyfikacji (CA) dla domeny. Uzyskaj niestandardową nazwę domeny dla klastra. Podczas żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla klastra.

#### <a name="client-authentication-certificates"></a>Certyfikaty uwierzytelniania klienta
Dodatkowe certyfikaty klientów uwierzytelniają administratorów dla zadań zarządzania klastrami. Sieci szkieletowej usług ma dwa poziomy dostępu: **administrator** i **tylko do odczytu użytkownika**. Należy co najmniej użyć jednego certyfikatu dostępu administracyjnego. Aby uzyskać dodatkowy dostęp na poziomie użytkownika, należy dostarczyć oddzielny certyfikat. Aby uzyskać więcej informacji na temat ról dostępu, zobacz [kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług][service-fabric-cluster-security-roles].

Nie trzeba przekazywać certyfikatów uwierzytelniania klienta do magazynu kluczy, aby pracować z siecią szkieletową usług. Te certyfikaty muszą być dostarczane tylko użytkownikom, którzy są autoryzowani do zarządzania klastrem. 

> [!NOTE]
> Usługa Azure Active Directory to zalecany sposób uwierzytelniania klientów dla operacji zarządzania klastrami. Aby korzystać z usługi Azure Active Directory, należy [utworzyć klaster przy użyciu usługi Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)
Dowolna liczba dodatkowych certyfikatów może być zainstalowana w klastrze ze względów bezpieczeństwa aplikacji. Przed utworzeniem klastra należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają zainstalowania certyfikatu w węzłach, takie jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji
* Szyfrowanie danych między węzłami podczas replikacji 

Nie można skonfigurować certyfikatów aplikacji podczas [tworzenia klastra za pośrednictwem witryny Azure Portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Aby skonfigurować certyfikaty aplikacji w czasie konfiguracji [klastra,][create-cluster-arm]należy utworzyć klaster przy użyciu usługi Azure Resource Manager . Certyfikaty aplikacji można również dodać do klastra po jego utworzeniu.

## <a name="create-cluster-in-the-azure-portal"></a>Tworzenie klastra w witrynie Azure portal

Tworzenie klastra produkcyjnego w celu zaspokojenia potrzeb aplikacji wymaga pewnego planowania, aby pomóc w tym, zdecydowanie zaleca się, aby przeczytać i zrozumieć dokument [zagadnienia dotyczące planowania klastra sieci szkieletowej usług.][service-fabric-cluster-capacity] 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Wyszukiwanie zasobu klastra sieci szkieletowej usług

Zaloguj się w witrynie [Azure Portal][azure-portal].
Kliknij **pozycję Utwórz zasób,** aby dodać nowy szablon zasobu. Wyszukaj szablon Klastra sieci szkieletowej usług w **portalu Marketplace** w obszarze **Wszystko**.
Z listy **wybierz klaster sieci szkieletowej** usług.

![wyszukaj szablon klastra sieci szkieletowej usług w witrynie Azure portal.][SearchforServiceFabricClusterTemplate]

Przejdź do bloku **Klastra sieci szkieletowej usług** i kliknij przycisk **Utwórz**.

Blok **klastra sieci szkieletowej create service** fabric ma następujące cztery kroki:

### <a name="1-basics"></a>1. Podstawy
![Zrzut ekranu przedstawiający tworzenie nowej grupy zasobów.][CreateRG]

W podstaw bloku, należy podać podstawowe szczegóły dla klastra.

1. Wprowadź nazwę klastra.
2. Wprowadź **nazwę użytkownika** i **hasło** dla pulpitu zdalnego dla maszyn wirtualnych.
3. Upewnij się, aby wybrać **subskrypcję,** w której ma zostać wdrożony klaster, zwłaszcza jeśli masz wiele subskrypcji.
4. Utwórz nową **grupę zasobów**. Najlepiej jest nadać mu taką samą nazwę jak klaster, ponieważ pomaga w znalezieniu ich później, szczególnie podczas próby wniesienia zmian do wdrożenia lub usunięcia klastra.
   
   > [!NOTE]
   > Chociaż można zdecydować się na użycie istniejącej grupy zasobów, dobrym rozwiązaniem jest utworzenie nowej grupy zasobów. Ułatwia to usuwanie klastrów i wszystkich zasobów, których używa.
   > 
   > 
5. Wybierz **lokalizację,** w której chcesz utworzyć klaster. Jeśli planujesz użyć istniejącego certyfikatu, który został już przekazany do magazynu kluczy, musisz użyć tego samego regionu, w którego znajduje się magazyn kluczy. 

### <a name="2-cluster-configuration"></a>2. Konfiguracja klastra
![Tworzenie typu węzła][CreateNodeType]

Skonfiguruj węzły klastra. Typy węzłów definiują rozmiary maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Klaster może mieć więcej niż jeden typ węzła, ale typ węzła podstawowego (pierwszy zdefiniowany w portalu) musi mieć co najmniej pięć maszyn wirtualnych, ponieważ jest to typ węzła, w którym są umieszczane usługi systemu sieci szkieletowej usług. Nie należy konfigurować **właściwości umieszczania,** ponieważ domyślna właściwość umieszczania "NodeTypeName" jest dodawana automatycznie.

> [!NOTE]
> Typowym scenariuszem dla wielu typów węzłów jest aplikacja, która zawiera usługę front-end i usługę zaplecza. Chcesz umieścić usługę front-end na mniejszych maszynach wirtualnych (rozmiarach maszyn wirtualnych, takich jak D2_V2) z portami otwartymi w Internecie i umieścić usługę zaplecza na większych maszynach wirtualnych (z rozmiarami maszyn wirtualnych, takimi jak D3_V2, D6_V2, D15_V2 i tak dalej) bez otwartych portów internetowych.
> 

1. Wybierz nazwę typu węzła (od 1 do 12 znaków zawierających tylko litery i cyfry).
2. Minimalny **rozmiar** maszyn wirtualnych dla typu węzła podstawowego jest napędzany przez **warstwę trwałości,** którą wybierzesz dla klastra. Wartość domyślna dla warstwy trwałości to brąz. Aby uzyskać więcej informacji na temat trwałości, zobacz [jak wybrać trwałość klastra sieci szkieletowej usług.][service-fabric-cluster-durability]
3. Wybierz **rozmiar maszyny wirtualnej**. Maszyny wirtualne z serii D mają dyski SSD i są wysoce zalecane w aplikacjach stanowych. Nie należy używać żadnej jednostki SKU maszyny Wirtualnej, która ma częściowe rdzenie lub ma mniej niż 10 GB dostępnej pojemności dysku. Aby uzyskać pomoc dotyczącą wybierania rozmiaru maszyny Wirtualnej, zapoznaj się z [dokumentem rozważania dotyczących planowania klastra sieci szkieletowej usług.][service-fabric-cluster-capacity]
4.  **Klaster pojedynczego węzła i klastry trzech węzłów** są przeznaczone tylko do użytku testowego. Nie są one obsługiwane dla żadnych uruchomionych obciążeń produkcyjnych.
5. Wybierz **początkową pojemność zestawu skalowania maszyny Wirtualnej** dla typu węzła. Można skalować w górę lub w dół liczbę maszyn wirtualnych w typie węzła później, ale na typ węzła podstawowego, minimum jest pięć dla obciążeń produkcyjnych. Inne typy węzłów może mieć co najmniej jedną maszynę wirtualną. Minimalna **liczba** maszyn wirtualnych dla typu węzła podstawowego zwiększa **niezawodność** klastra.  
6. Konfigurowanie **niestandardowych punktów końcowych**. To pole umożliwia wprowadzenie listy portów oddzielonych przecinkami, które mają zostać udostępnione za pośrednictwem modułu Równoważenia obciążenia platformy Azure w publicznym Internecie dla aplikacji. Na przykład jeśli planujesz wdrożyć aplikację sieci web w klastrze, wprowadź "80" w tym miejscu, aby zezwolić na ruch na porcie 80 do klastra. Aby uzyskać więcej informacji na temat punktów końcowych, zobacz [komunikowanie się z aplikacjami][service-fabric-connect-and-communicate-with-services]
7. **Włącz odwrotny serwer proxy**.  Odwrotny [serwer proxy sieci szkieletowej usług](service-fabric-reverseproxy.md) pomaga mikrousług uruchomionych w klastrze sieci szkieletowej usług odnajdywania i komunikowania się z innymi usługami, które mają punkty końcowe http.
8. Powrót do bloku **konfiguracji klastra** w obszarze **+Pokaż ustawienia opcjonalne**, skonfiguruj **diagnostykę klastra**. Domyślnie diagnostyka jest włączona w klastrze, aby pomóc w rozwiązywaniu problemów. Jeśli chcesz wyłączyć diagnostykę, zmień przełącznik **Stan** na **Wyłączone**. Wyłączenie diagnostyki **nie** jest zalecane. Jeśli masz już utworzony projekt usługi Application Insights, a następnie podaj jego klucz, aby ślady aplikacji były kierowane do niego.
9. **Uwzględnij usługę DNS**.  [Usługa DNS](service-fabric-dnsservice.md) jest usługą opcjonalną, która umożliwia znajdowanie innych usług przy użyciu protokołu DNS.
10. Wybierz **tryb uaktualniania sieci szkieletowej,** na który chcesz ustawić klaster. Wybierz **opcję Automatycznie**, jeśli chcesz, aby system automatycznie odebrał najnowszą dostępną wersję i próbował uaktualnić do niego klaster. Ustaw tryb **ręczny**, jeśli chcesz wybrać obsługiwaną wersję. Aby uzyskać więcej informacji na temat trybu uaktualniania sieci szkieletowej, zobacz [dokument uaktualniania klastra sieci szkieletowej usług.][service-fabric-cluster-upgrade]

> [!NOTE]
> Obsługujemy tylko klastry, które są uruchomione obsługiwane wersje sieci szkieletowej usług. Wybierając tryb **ręczny,** bierzesz na siebie odpowiedzialność za uaktualnienie klastra do obsługiwanej wersji.
> 

### <a name="3-security"></a>3. Bezpieczeństwo
![Zrzut ekranu przedstawiający konfiguracje zabezpieczeń w witrynie Azure portal.][BasicSecurityConfigs]

Aby ułatwić Ci konfigurowanie bezpiecznego klastra testowego, udostępniliśmy opcję **Podstawowa.** Jeśli masz już certyfikat i prześlesz go do [magazynu kluczy](/azure/key-vault/) (i włączono magazyn kluczy do wdrożenia), użyj opcji **Niestandardowe**

#### <a name="basic-option"></a>Opcja podstawowa
Postępuj zgodnie z ekranami, aby dodać lub ponownie użyć istniejącego magazynu kluczy i dodać certyfikat. Dodanie certyfikatu jest procesem synchroniczowym, więc trzeba będzie poczekać na utworzenie certyfikatu.

Oprzeć się pokusie poruszania się z dala od ekranu, aż do zakończenia poprzedniego procesu.

![Utwórz klawiszEVault]

Teraz, gdy magazyn kluczy jest tworzony, edytuj zasady dostępu do magazynu kluczy. 

![Utwórz klawiszEVault2]

Kliknij przycisk **Edytuj zasady dostępu**, a następnie pokaż zaawansowane zasady **dostępu** i włącz dostęp do maszyn wirtualnych platformy Azure do wdrożenia. Zaleca się również włączenie wdrożenia szablonu. Po dokonaniu wyboru nie zapomnij kliknąć przycisku **Zapisz** i zamknąć okienko **Zasady dostępu.**

![Utwórj klawiszEVault3]

Wprowadź nazwę certyfikatu i kliknij przycisk **OK**.

![Utwórz klawiszEVault4]

#### <a name="custom-option"></a>Opcja niestandardowa
Pomiń tę sekcję, jeśli wykonano już kroki opisane w opcji **podstawowej.**

![Ochrona zdańOpcja]

Aby ukończyć stronę zabezpieczeń, potrzebujesz informacji o magazynie kluczy źródłowych, adresie URL certyfikatu i odcisku palca certyfikatu. Jeśli nie masz go pod ręką, otwórz inne okno przeglądarki i w witrynie Azure portal wykonaj następujące czynności:

1. Przejdź do usługi przechowalni kluczy.
2. Wybierz kartę "Właściwości" i skopiuj identyfikator zasobu do "Skarbiec kluczy źródłowych" w innym oknie przeglądarki 

    ![CertInfo0 (Polski)]

3. Teraz wybierz kartę "Certyfikaty".
4. Kliknij odcisk palca certyfikatu, który przeniesie Cię do strony Wersje.
5. Kliknij identyfikatory GUID widoczne w bieżącej wersji.

    ![CertInfo1]

6. Powinieneś być teraz na ekranie, jak poniżej. Skopiuj szesnastkowy odcisk palca SHA-1 do "Odcisk palca certyfikatu" w innym oknie przeglądarki
7. Skopiuj "Tajny identyfikator" do "Adresu URL certyfikatu" w innym oknie przeglądarki.

    ![CertInfo2 (Polski)]

Zaznacz pole **Konfigurowanie ustawień zaawansowanych,** aby wprowadzić certyfikaty klienta dla **klienta administracyjnego** i klienta tylko do **odczytu**. W tych polach wprowadź odcisk palca certyfikatu klienta administratora i odcisk palca certyfikatu klienta tylko do odczytu, jeśli ma to zastosowanie. Gdy administratorzy próbują połączyć się z klastrem, są one przyznawane dostęp tylko wtedy, gdy mają certyfikat z odcisku palca, który pasuje do wartości odcisk palca wprowadzonych w tym miejscu.  

### <a name="4-summary"></a>4. Streszczenie

Teraz można przystąpić do wdrożenia klastra. Zanim to zrobisz, pobierz certyfikat, zajrzyj do dużego niebieskiego pola informacyjnego dla łącza. Upewnij się, że cert jest bezpieczny. do połączenia się z klastrem. Ponieważ pobrany certyfikat nie ma hasła, zaleca się dodanie jednego.

Aby zakończyć tworzenie klastra, kliknij przycisk **Utwórz**. Opcjonalnie można pobrać szablon.

![Podsumowanie]

Możesz zobaczyć postępy tworzenia w powiadomieniach. (Kliknij ikonę "Dzwonek" w pobliżu paska stanu w prawym górnym rogu ekranu). Jeśli podczas tworzenia klastra kliknięno **pozycję Przypnij do ekranu startowego,** zostanie wyświetlony tryb **Wdrażania klastra sieci szkieletowej usług** przypiętych do **tablicy startowej.** Ten proces zajmie trochę czasu. 

Aby wykonać operacje zarządzania w klastrze przy użyciu programu Powershell lub interfejsu wiersza polecenia, należy połączyć się z klastrem, przeczytaj więcej na temat [łączenia się z klastrem](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Wyświetlanie stanu klastra
![Zrzut ekranu przedstawiający szczegóły klastra na pulpicie nawigacyjnym.][ClusterDashboard]

Po utworzeniu klastra można sprawdzić klaster w portalu:

1. Przejdź do **strony Przeglądaj** i kliknij pozycję **Klastry sieci szkieletowej usług**.
2. Znajdź klaster i kliknij go.
3. Możesz teraz wyświetlić szczegóły klastra na pulpicie nawigacyjnym, w tym publiczny punkt końcowy klastra oraz link do narzędzia Service Fabric Explorer.

Sekcja **Monitor węzłów** w bloku pulpitu nawigacyjnego klastra wskazuje liczbę maszyn wirtualnych, które są w dobrej kondycji i nie są w dobrej kondycji. Więcej informacji na temat kondycji klastra można znaleźć we [wprowadzeniu modelu kondycji sieci szkieletowej usług.][service-fabric-health-introduction]

> [!NOTE]
> Klastry sieci szkieletowej usług wymagają określonej liczby węzłów, które mają być zawsze w górę, aby zachować dostępność i zachować stan — określane jako "utrzymanie kworum". W związku z tym zazwyczaj nie jest bezpieczne, aby zamknąć wszystkie maszyny w klastrze, chyba że po raz pierwszy wykonano [pełną kopię zapasową stanu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Zdalne łączenie się z wystąpieniem zestawu skalowania maszyny wirtualnej lub węzłem klastra
Każdy z typów węzłów określonych w klastrze powoduje, że zestaw skalowania maszyny wirtualnej jest ustawiany. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Następne kroki
W tym momencie masz bezpiecznego klastra przy użyciu certyfikatów do uwierzytelniania zarządzania. Następnie [połącz się z klastrem](service-fabric-connect-to-secure-cluster.md) i dowiedz się, jak [zarządzać wpisami tajnymi aplikacji.](service-fabric-application-secret-management.md)  Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
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
[Utwórz klawiszEVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[Utwórz klawiszEVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[Utwórj klawiszEVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[Utwórz klawiszEVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0 (Polski)]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2 (Polski)]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[Ochrona zdańOpcja]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Podsumowanie]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
