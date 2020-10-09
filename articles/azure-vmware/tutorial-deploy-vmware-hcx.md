---
title: Samouczek — wdrażanie i Konfigurowanie programu VMware HCX
description: Dowiedz się, jak wdrażać i konfigurować rozwiązanie VMware HCX w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583346"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Wdrażanie i konfigurowanie usługi VMware HCX

W tym artykule omówiono procedury wdrażania i konfigurowania programu VMware HCX dla chmury prywatnej rozwiązania Azure VMWare. Program VMware HCX umożliwia migrację obciążeń oprogramowania VMware do rozwiązania VMware platformy Azure i innych połączonych lokacji przy użyciu różnych typów migracji.

Program VMware HCX Advanced (wstępnie wdrożony w ramach rozwiązania Azure VMware) obsługuje maksymalnie trzy połączenia z lokacją (lokalnie do chmury lub w chmurze). Jeśli wymagane są więcej niż trzy połączenia z witryną VMware HCX Enterprise, można włączyć dodatek [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) (który jest obecnie w *wersji zapoznawczej*), przesyłając [żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support). Program VMware HCX Enterprise Edition (EE) jest dostępny w ramach rozwiązania Azure VMware jako funkcji/usługi w *wersji zapoznawczej* . Chociaż rozwiązanie VMware HCX EE for Azure VMware jest w *wersji zapoznawczej*, jest to bezpłatna funkcja/usługa i podlega podglądowi warunków i postanowień usługi. Gdy usługa VMware HCX EE stanie się dostępna, otrzymasz 30-dniowy komunikat, że rozliczenie zostanie przełączone. Dostępna jest również opcja przełączania/rezygnacji z usługi.

Przed rozpoczęciem należy dokładnie zapoznać się [przed rozpoczęciem](#before-you-begin), [wymagania dotyczące wersji oprogramowania](#software-version-requirements)i [wymagania wstępne](#prerequisites). 

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"]
> * Wdrażanie lokalnych komórek jajowych programu VMware HCX (łącznik)
> * Aktywuj oprogramowanie VMware HCX
> * Parowanie środowiska lokalnego z Twoim środowiskiem rozwiązań VMware platformy Azure.
> * Konfigurowanie połączenia (profil obliczeniowy, Profile sieciowe i sieć usługi)
> * Ukończ instalację, sprawdzając stan urządzenia.

Po zakończeniu możesz wykonać czynności opisane w sekcji zalecane na końcu tego artykułu.  

## <a name="before-you-begin"></a>Przed rozpoczęciem
   
* Zapoznaj się z podstawową [serią samouczków](tutorial-network-checklist.md)dotyczących oprogramowania VMware (SDDC).
* Zapoznaj się z [dokumentacją programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), w tym podręczniku użytkownika HCX.
* Przejrzyj dokumentację oprogramowania VMware [migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Zapoznaj się z [zaleceniami dotyczącymi wdrażania programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Zapoznaj VMware vSphere się z tematem "HCX", na przykład z [serii blogów](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) w witrynie HCX. 
* Opcjonalnie możesz zażądać aktywacji rozwiązania Azure VMware HCX Enterprise za pomocą kanałów obsługi rozwiązań VMware platformy Azure.
* Aby można było wdrożyć urządzenie WAN Interconnect, określone zakresy CIDR są już przydzieleni z klienta dostarczonego `\22` do tworzenia chmury prywatnej.

Ustalanie rozmiarów obciążeń związanych z zasobami obliczeniowymi i magazynem jest ważnym etapem planowania. Zaadresuj krok zmiany wielkości w ramach wstępnego planowania środowiska chmury prywatnej. 

Możesz również zmienić rozmiar obciążeń, wykonując [ocenę rozwiązań VMware platformy Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) w portalu Azure Migrate.

## <a name="software-version-requirements"></a>Wymagania dotyczące wersji oprogramowania

Składniki infrastruktury muszą mieć uruchomioną wymaganą wersję minimalną. 
                                                         
| Typ składnika    | Wymagania dotyczące środowiska źródłowego    | Wymagania środowiska docelowego   |
| --- | --- | --- |
| Program vCenter Server   | 5,1<br/><br/>W przypadku używania 5,5 U1 lub wcześniejszych należy użyć autonomicznego interfejsu użytkownika HCX dla operacji HCX.  | 6,0 U2 i więcej   |
| ESXi   | 5,0    | ESXi 6,0 i nowsze   |
| NSX    | W przypadku rozszerzenia sieci HCX przełączników logicznych w źródle: NSXv 6.2 + lub NSX-T 2.4 +   | NSXv 6.2 + lub NSX-T 2.4 +<br/><br/>W przypadku routingu HCX zbliżeniowe: NSXv 6.4 + (Routing bliskości nie jest obsługiwany w przypadku NSX-T) |
| Dyrektor vCloud   | Niewymagana — brak współdziałania z vCloud Director w lokacji źródłowej | W przypadku integracji środowiska docelowego z vCloud dyrektorem minimalnym jest 9.1.0.2.  |

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="network-and-ports"></a>Sieć i porty

* [ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) skonfigurowane między środowiskiem lokalnym i rozwiązaniem Azure VMware SDDC ExpressRoute.

* Wszystkie wymagane porty powinny być otwarte do komunikacji między składnikami lokalnymi i lokalnymi z rozwiązaniem VMware platformy Azure SDDC (zobacz [dokumentację programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Lokalne HCX IX i NE urządzenia powinny być w stanie dotrzeć do infrastruktury vCenter i ESXi.

### <a name="ip-addresses"></a>Adresy IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Wdrażanie programu VMware HCX komórki jajowe w środowisku lokalnym

>[!NOTE]
>Przed wdrożeniem urządzenia wirtualnego w lokalnym programie vCenter należy pobrać komórki jajowe programu VMware HCX. 

1. Zaloguj się do usługi Azure VMware Solution HCX Manager na `https://x.x.x.9` porcie 443 przy użyciu poświadczeń użytkownika **cloudadmin** , a następnie przejdź do **pomocy technicznej**.

   >[!TIP]
   >Aby zidentyfikować adres IP programu HCX Manager, w bloku rozwiązania Azure VMware przejdź do obszaru **Zarządzanie**  >  **łącznością** , a następnie wybierz kartę **HCX** . 
   >
   >Hasło vCenter zostało zdefiniowane podczas konfigurowania chmury prywatnej.

1. Wybierz link **pobierania** , aby pobrać plik komórek jajowych programu VMware HCX.

1. Przejdź do lokalnego programu vCenter i wybierz szablon OVF, który jest pobranym plikiem komórki jajowe do wdrożenia w lokalnym programie vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Wybierz nazwę i lokalizację, zasób/klaster, w którym są wdrażane HCX, a następnie przejrzyj szczegóły i wymagane zasoby.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Zapoznaj się z postanowieniami licencyjnymi, a jeśli zgadzasz się, wybierz wymagany magazyn i sieć, a następnie wybierz przycisk **dalej**.

1. W obszarze **Dostosowywanie szablonu**wprowadź wszystkie wymagane informacje. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Wybierz pozycję **dalej**, sprawdź konfigurację, a następnie wybierz pozycję **Zakończ** , aby wdrożyć HCX komórki jajowe.
     
   >[!NOTE]
   >Ogólnie wdrażanie programu VMware HCX Manager jest wdrażane w sieci zarządzania klastrami.  
   
   > [!IMPORTANT]
   > Po zakończeniu wdrożenia może być konieczne ręczne wyłączenie urządzenia wirtualnego.
   > Poczekaj 10-15 minut po włączeniu urządzenia HCX, aby przejść do następnego kroku.

Aby zapoznać się z kompleksowym omówieniem tego kroku, zapoznaj się z tematem wideo dotyczące [wdrażania urządzeń VMware w programie Azure HCX](https://www.youtube.com/embed/BwSnQeefnso) . 


## <a name="activate-vmware-hcx"></a>Aktywuj oprogramowanie VMware HCX

Po wdrożeniu komórek jajowych programu VMware HCX w środowisku lokalnym można aktywować program VMware HCX. Aby można było aktywować program VMware HCX, konieczne będzie pobranie licencji.

1. W rozwiązaniu VMware platformy Azure przejdź do pozycji **Zarządzanie**  >  **łącznością**, wybierz kartę **HCX** , a następnie wybierz pozycję **Dodaj**.

1. Zaloguj się do lokalnego programu VMware HCX Manager pod adresem i zaloguj się przy użyciu `https://HCXManagerIP:9443` poświadczeń użytkownika **administratora** . 

   > [!IMPORTANT]
   > Upewnij się, że numer portu został uwzględniony w `9443` adresie IP programu VMware HCX Manager.

1. W obszarze **Licencjonowanie**wprowadź **klucz zaawansowany HCX**.  
   
    > [!NOTE]
    > Program VMware HCX Manager musi mieć skonfigurowany otwarty dostęp do Internetu lub serwer proxy.

1. W obszarze **Lokalizacja centrum**danych podaj najbliższą lokalizację, w której jest instalowany lokalny program VMware HCX Manager.

1. Zmodyfikuj **nazwę systemu** lub Zaakceptuj wartość domyślną.
   
1. Możesz zakończyć pracę później lub kontynuować, wybierz opcję **tak, Kontynuuj,** aby kontynuować.
    
1. W obszarze **Połącz swój program vCenter**Podaj nazwę FQDN lub adres IP serwera vCenter oraz odpowiednie poświadczenia, a następnie wybierz pozycję **Kontynuuj**.
   
1. W obszarze **Konfigurowanie logowania jednokrotnego/PSC**Podaj nazwę FQDN lub adres IP komputera PSC, a następnie wybierz pozycję **Kontynuuj**.
   
   >[!NOTE]
   >Zwykle taka sama jak nazwa FQDN/adres IP programu vCenter.

1. Sprawdź, czy wszystkie dane wejściowe są poprawne, a następnie wybierz pozycję **Uruchom ponownie**.
    
   > [!NOTE]
   > Przed wyświetleniem monitu o następny krok nastąpi opóźnienie po ponownym uruchomieniu.
   >
   >Po ponownym uruchomieniu usług najważniejsze jest, że na wyświetlonym ekranie jest wyświetlany element vCenter wyświetlany jako zielony. Serwery vCenter i SSO mają odpowiednie parametry konfiguracji, które powinny być takie same jak w poprzednim ekranie.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz [rozwiązanie Azure VMware — wideo z aktywacją VMware HCX](https://www.youtube.com/embed/BkAV_TNYxdE) .


## <a name="configure-vmware-hcx"></a>Konfigurowanie programu VMware HCX

Teraz możesz dodać parowanie lokacji, utworzyć sieć i profil obliczeniowy oraz włączyć usługi, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii. 

### <a name="add-a-site-pairing"></a>Dodawanie parowania lokacji

Za pomocą programu VMware HCX Manager w centrum danych możesz połączyć się z usługą VMware HCX Manager w rozwiązaniu VMware. 

1. Zaloguj się do lokalnego programu vCenter i w obszarze **Strona główna**wybierz pozycję **HCX**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. W obszarze **infrastruktura**wybierz pozycję **parowanie lokacji**, a następnie wybierz opcję **Połącz z lokacją zdalną** (w środku ekranu). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Wprowadź **adres URL lub adres IP zdalnego HCX**, cloudadmin@vsphere.local nazwę użytkownika i **hasło**rozwiązania VMware platformy Azure, a następnie wybierz pozycję **Połącz**.

   > [!NOTE]
   > **Zdalny adres URL HCX** to rozwiązanie Azure VMware Private Cloud HCX Manager, zazwyczaj adres ". 9" sieci zarządzania.  Na przykład jeśli serwer vCenter to 192.168.4.2, adres URL HCX będzie 192.168.4.9.
   >
   > **Hasło** będzie używane do logowania się do programu vCenter. To hasło zostało zdefiniowane na ekranie wdrożenia początkowego.

   Zobaczysz ekran z HCX Manager w rozwiązaniu VMware platformy Azure, a Menedżer HCX jest połączony lokalnie (sparowany).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.":::

Aby zapoznać się z kompleksowym omówieniem tego kroku, zapoznaj się z tematem wideo dotyczące [rozwiązań VMware dla programu VMware HCX](https://www.youtube.com/embed/sKizDCRHOko) .



### <a name="create-network-profiles"></a>Tworzenie profilów sieciowych

Oprogramowanie VMware HCX wdraża kilka urządzeń wirtualnych (zautomatyzowany), ale wymaga wielu segmentów IP.  Podczas tworzenia profilów sieci należy zdefiniować segmenty adresów IP, które zostały zidentyfikowane podczas [przygotowywania segmentów sieci VMware HCX przed wdrożeniem i planowania](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Utworzysz cztery Profile sieciowe:

   - Zarządzanie
   - vMotion
   - Replikacja
   - Łączy

1. W obszarze **infrastruktura**wybierz kolejno pozycje **połączenie**  >  sieć sieci**siatkowa usługi**połączenia  >  **Network Profiles**  >  **Tworzenie profilu sieciowego**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Dla każdego profilu sieciowego wybierz sieć, grupę portów, podaj nazwę, Utwórz pulę adresów IP dla danego segmentu, a następnie wybierz pozycję **Utwórz**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.":::

Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz [rozwiązanie Azure VMware — HCX Utwórz profil sieciowy](https://www.youtube.com/embed/NhyEcLco4JY) wideo.


### <a name="create-compute-profile"></a>Utwórz profil obliczeń

1. Wybierz kolejno pozycje **obliczenia profile**  >  **Utwórz profil obliczeniowy**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Wprowadź nazwę profilu i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Wybierz usługi do włączenia, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii, a następnie wybierz pozycję **Kontynuuj**.
  
   > [!NOTE]
   > Na ogół nic nie zostanie zmienione w tym miejscu.

1. W obszarze **Wybierz zasoby usługi**wybierz co najmniej jeden zasób usługi (klastra) do włączenia dla wybranych usług VMware HCX.  

1. Gdy widzisz klastry w lokalnym centrum danych, wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. W obszarze **Wybieranie magazynu**danych wybierz zasób magazynu danych do wdrożenia urządzeń programu VMware HCX Interconnect, a następnie wybierz pozycję **Kontynuuj**.

   W przypadku wybrania wielu zasobów program VMware HCX używa pierwszego wybranego zasobu do momentu wyczerpania jego wydajności.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Wybierz pozycję **Zarządzanie profilem sieci**, profil sieci zarządzania utworzony w poprzednich krokach, a następnie wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Profil sieci zarządzania umożliwia komunikację między urządzeniami VMware HCX z programem vCenter oraz hosty ESXi.

1. Wybierz pozycję **profil sieciowy pasma**, profil sieci pasma, który został utworzony w poprzednich krokach, a następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Wybierz pozycję **profil sieci vMotion**, profil sieci vMotion, który został utworzony w poprzednich krokach, a następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Wybierz pozycję **profil sieci replikacji vSphere**, profil sieci replikacji utworzony w poprzednich krokach, a następnie wybierz pozycję **Kontynuuj**.

   W większości przypadków profil sieci replikacji jest taki sam jak profil sieci zarządzania.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Wybierz pozycję **przełączniki rozproszone dla rozszerzeń sieci**, rozproszone przełączniki wirtualne zawierające maszyny wirtualne do migracji do rozwiązania VMware platformy Azure w sieci rozszerzonej warstwy 2, a następnie wybierz pozycję **Kontynuuj**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Przejrzyj reguły połączeń i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Wybierz pozycję **Zakończ** , aby utworzyć profil obliczeń.

   Zobaczysz ekran podobny do przedstawionego poniżej.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz [rozwiązanie Azure VMware — wideo VMware HCX Create COMPUTE profile](https://www.youtube.com/embed/qASXi5xrFzM) .




### <a name="create-service-mesh"></a>Utwórz siatkę usług

Teraz można skonfigurować siatkę usługi między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure SDDC.

1. W obszarze **infrastruktura**wybierz **Interconnect**pozycję  >  **Sieć usługi**Interconnect  >  **Utwórz siatkę usług**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Przejrzyj wstępnie wypełnione witryny, a następnie wybierz pozycję **Kontynuuj**. 

   >[!NOTE]
   >Jeśli jest to pierwsza konfiguracja sieci usługi, nie trzeba modyfikować tego ekranu.  

1. Wybierz listę rozwijaną dla profilów obliczeniowych źródła i zdalnego, a następnie wybierz pozycję **Kontynuuj**.  

   Wybrane opcje określają zasoby, w których maszyny wirtualne mogą korzystać z usług VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Przejrzyj usługi, które zostaną włączone, a następnie wybierz pozycję **Kontynuuj**.  

1. W obszarze **Konfiguracja zaawansowana — Przesłoń profile sieci pasma** wybierz pozycję **Kontynuuj**.  Profile sieci pasma łączą się z siecią, za pomocą której można uzyskać dostęp do urządzeń łączących się z lokacją zdalną.  
  
1. W obszarze **Konfiguracja zaawansowana — Przeskaluj w poziomie urządzenie rozszerzenia sieci**, przejrzyj i wybierz pozycję **Kontynuuj**. 

1. W **zaawansowanej konfiguracji — Inżynieria ruchu**, przejrzyj i wprowadź wszelkie wymagane zmiany, a następnie wybierz pozycję **Kontynuuj**.

1. Przejrzyj Podgląd topologii i wybierz pozycję **Kontynuuj**.

1. Wprowadź nazwę przyjazną dla użytkownika dla tej sieci usługi i wybierz pozycję **Zakończ** , aby zakończyć.  

1. Wybierz pozycję **Wyświetl zadania** w celu monitorowania wdrożenia. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.":::

   Po pomyślnym zakończeniu wdrożenia usługi siatka usług zobaczysz usługi jako zielone.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Sprawdź kondycję siatki usługi, sprawdzając stan urządzenia, a następnie wybierz pozycję urządzenia do **łączenia**  >  **Appliances**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz [rozwiązanie Azure VMware — wideo VMware HCX Create Service](https://www.youtube.com/embed/FyZ0d3P_T24) .



### <a name="optional-create-a-network-extension"></a>Obowiązkowe Tworzenie rozszerzenia sieci

Jeśli chcesz rozciągnąć wszystkie sieci z środowiska lokalnego do rozwiązania Azure VMware, wykonaj następujące kroki, aby zwiększyć te sieci.

1. W obszarze **usługi**wybierz pozycję **rozszerzenie sieci**, a następnie wybierz pozycję **Utwórz rozszerzenie sieciowe**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Wybierz wszystkie sieci, które chcesz rozłożyć na rozwiązanie VMware platformy Azure, a następnie wybierz przycisk **dalej**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.":::

1. Wprowadź adres IP bramy lokalnej dla każdej z rozszerzanych sieci, a następnie wybierz pozycję **Prześlij**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.":::

   Ukończenie rozszerzenia sieci trwa kilka minut. Gdy to nastąpi, zobaczysz zmianę stanu na **rozszerzenie**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Aby zapoznać się z kompleksowym omówieniem tego kroku, zapoznaj się z tematem wideo dotyczące [rozwiązania Azure VMware — VMware HCX Network Extension](https://www.youtube.com/embed/cNlp0f_tTr0) .


## <a name="next-steps"></a>Następne kroki

Jeśli ten punkt został osiągnięty, a stan tunelu łączenia urządzeń jest **ustawiony** na zielony, można migrować i chronić maszyny wirtualne rozwiązań VMware platformy Azure przy użyciu programu VMware HCX.  Rozwiązanie VMware platformy Azure obsługuje migracje obciążeń (z lub z rozszerzeniem sieci).  W środowisku vSphere można nadal wykonywać migracje obciążeń, a także tworzyć sieci i wdrażać maszyny wirtualne w tych sieciach.  Aby uzyskać więcej informacji, zobacz [dokumentację oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) i [Migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) w dokumentacji technicznej programu VMware, aby uzyskać szczegółowe informacje na temat korzystania z usługi HCX.
