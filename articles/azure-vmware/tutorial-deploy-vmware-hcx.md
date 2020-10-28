---
title: Samouczek — wdrażanie i Konfigurowanie programu VMware HCX
description: Dowiedz się, jak wdrożyć i skonfigurować rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 424abeef567d88f7de37f7a7a4ab7a7a8b6ef3bc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791413"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Wdrażanie i konfigurowanie usługi VMware HCX

W tym artykule omówiono procedury wdrażania i konfigurowania lokalnego łącznika VMware HCX dla chmury prywatnej rozwiązania Azure VMware. Dzięki oprogramowaniu VMware HCX można migrować obciążenia oprogramowania VMware do rozwiązania VMware platformy Azure i innych połączonych lokacji za pomocą różnych typów migracji. Ponieważ rozwiązanie VMware platformy Azure wdraża i konfiguruje program HCX Cloud Manager, należy pobrać, aktywować i skonfigurować łącznik HCX w lokalnym centrum danych VMware.

Łącznik programu VMware HCX Advanced został wstępnie wdrożony w rozwiązaniu VMware platformy Azure. Obsługuje ona maksymalnie trzy połączenia z lokacją (lokalnie do chmury lub w chmurze do chmury). Jeśli potrzebujesz więcej niż trzech połączeń witryny, Prześlij [żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support) , aby włączyć dodatek [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) . Dodatek jest obecnie w wersji zapoznawczej. 

>[!NOTE]
>Program VMware HCX Enterprise Edition (EE) jest dostępny w ramach rozwiązania Azure VMware jako usługi w wersji zapoznawczej. Jest ona bezpłatna i podlega postanowieniom dotyczącym usługi w wersji zapoznawczej. Po ogólnym udostępnieniu usługi VMware HCX EE uzyskasz 30-dniowy okres, w którym rozliczenia zostaną przełączone. Będziesz również mieć możliwość wyłączenia lub rezygnacji z usługi. Pease uwaga nie ma obecnie żadnej prostej ścieżki obniżenia poziomu od HCX Enterprise do HCX Advanced i Klienci, którzy zdecydują się na obniżenie wersji, będą musieli ponownie wdrożyć naliczanie przestojów.

Najpierw dokładnie zapoznaj się z sekcją [przed rozpoczęciem](#before-you-begin), [wymagania dotyczące wersji oprogramowania](#software-version-requirements)i [wymagania wstępne](#prerequisites) w tym artykule. 

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"]
> * Wdróż lokalne komórki jajowe programu VMware HCX (łącznik HCX).
> * Aktywuj łącznik VMware HCX.
> * Parowanie lokalnego łącznika HCX z rozwiązaniem platformy Azure VMware HCX Cloud Manager.
> * Skonfiguruj połączenie (profil sieciowy, profil obliczeniowy i sieć usługi).
> * Ukończ instalację, sprawdzając stan urządzenia i sprawdzając, czy migracja jest możliwa.

Po zakończeniu możesz wykonać czynności opisane w sekcji zalecane na końcu tego artykułu.  

## <a name="before-you-begin"></a>Przed rozpoczęciem
   
* Zapoznaj się z podstawową [serią samouczka](tutorial-network-checklist.md)usługi Azure VMware Software-Defined Datacenter (SDDC).
* Zapoznaj się z [dokumentacją programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html), w tym podręczniku użytkownika HCX.
* Przejrzyj [migrowanie Virtual Machines z oprogramowaniem VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) w witrynie VMware.
* Zapoznaj się z [zaleceniami dotyczącymi wdrażania programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Zapoznaj się z tematem na HCX, na przykład na VMware vSphere [seriach blogów](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html). 
* Opcjonalnie możesz zażądać aktywacji rozwiązania Azure VMware HCX Enterprise za pomocą kanałów obsługi rozwiązań VMware platformy Azure.
* Opcjonalne [Sprawdzanie portów sieciowych wymaganych przez HCX](https://ports.vmware.com/home/VMware-HCX).
* Mimo że rozwiązanie Azure VMware HCX Cloud Manager jest wstępnie skonfigurowane z sieci/22 dostarczonej dla chmury prywatnej rozwiązania Azure VMware, lokalny łącznik HCX wymaga przydzielenia zakresów sieci z sieci lokalnej. Te sieci i zakresy zostały opisane w dalszej części tego artykułu.

Ustalanie rozmiarów obciążeń związanych z zasobami obliczeniowymi i magazynem jest ważnym etapem planowania. Zanotuj krok zmiany wielkości w ramach wstępnego planowania środowiska chmury prywatnej. 

Możesz zmienić rozmiar obciążeń, wykonując [ocenę rozwiązań VMware platformy Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md) w portalu Azure Migrate.

## <a name="software-version-requirements"></a>Wymagania dotyczące wersji oprogramowania

Składniki infrastruktury muszą mieć uruchomioną wymaganą wersję minimalną. 
                                                         
| Typ składnika    | Wymagania dotyczące środowiska źródłowego    | Wymagania środowiska docelowego   |
| --- | --- | --- |
| Program vCenter Server   | 5,1<br/><br/>Jeśli używasz 5,5 U1 lub starszej wersji, użyj autonomicznego interfejsu użytkownika HCX dla operacji HCX.  | 6,0 U2 i nowsze   |
| ESXi   | 5,0    | ESXi 6,0 i nowsze   |
| NSX    | W przypadku rozszerzenia sieci HCX przełączników logicznych w źródle: NSXv 6.2 + lub NSX-T 2.4 +.   | NSXv 6.2 + lub NSX-T 2.4 +<br/><br/>W przypadku routingu HCX zbliżeniowe: NSXv 6.4 +. (Routing bliskości nie jest obsługiwany w przypadku NSX-T). |
| Dyrektor vCloud   | Niewymagane. Brak współdziałania z vCloud Director w lokacji źródłowej. | W przypadku integracji środowiska docelowego z programem vCloud Director minimalna wartość to 9.1.0.2.  |

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="network-and-ports"></a>Sieć i porty

* Skonfiguruj [Global REACH Azure ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) między środowiskiem lokalnym i usługą Azure VMware SDDC ExpressRoute.

* [Wszystkie wymagane porty](https://ports.vmware.com/home/VMware-HCX) powinny być otwarte do komunikacji między składnikami lokalnymi i rozwiązaniem Azure VMware SDDC.

Aby uzyskać więcej informacji, zobacz [dokumentację programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>Adresy IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Wdrażanie lokalnych komórek jajowych łącznika VMware HCX

> [!NOTE]
> Przed wdrożeniem urządzenia wirtualnego w lokalnym programie vCenter należy pobrać komórki jajowe łącznika VMware HCX. 

1. Otwórz okno przeglądarki, zaloguj się do usługi Azure VMware Solution HCX Manager na `https://x.x.x.9` porcie 443 przy użyciu poświadczeń użytkownika **cloudadmin** , a następnie przejdź do **pomocy technicznej** .

   > [!TIP]
   > Zanotuj adres IP HCX Cloud Manager w rozwiązaniu Azure VMware. Aby zidentyfikować adres IP, w okienku rozwiązania Azure VMware przejdź do pozycji **Zarządzanie**  >  **łącznością** , a następnie wybierz kartę **HCX** . 
   >
   >Hasło vCenter zostało zdefiniowane podczas konfigurowania chmury prywatnej.

1. Wybierz link **pobierania** , aby pobrać plik komórek jajowych łącznika programu VMware HCX.

1. Przejdź do lokalnego serwera vCenter. Wybierz szablon OVF, który jest pobieranym plikiem komórki jajowe, aby wdrożyć łącznik HCX w lokalnym programie vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Wybierz nazwę i lokalizację, a następnie wybierz zasób/klaster, w którym jest wdrażany łącznik HCX. Następnie przejrzyj szczegóły i wymagane zasoby.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Zapoznaj się z postanowieniami licencyjnymi. Jeśli zgadzasz się, wybierz wymagany magazyn i sieć, a następnie wybierz przycisk **dalej** .

1. W obszarze **Dostosowywanie szablonu** wprowadź wszystkie wymagane informacje. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Wybierz pozycję **dalej** , sprawdź konfigurację, a następnie wybierz pozycję **Zakończ** , aby wdrożyć komórki jajowe łącznika HCX.
     
   > [!NOTE]
   > Zazwyczaj wdrożony łącznik VMware HCX jest wdrażany w sieci zarządzania klastrami.  
   
   > [!IMPORTANT]
   > Może być konieczne ręczne włączenie urządzenia wirtualnego.  W takim przypadku należy poczekać 10-15 minut przed przejściem do następnego kroku.

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware:](https://www.youtube.com/embed/BwSnQeefnso) film wideo dotyczący wdrażania urządzenia HCX. 


## <a name="activate-vmware-hcx"></a>Aktywuj oprogramowanie VMware HCX

Po wdrożeniu komórek jajowych łącznika VMware HCX w środowisku lokalnym i uruchomieniu urządzenia można rozpocząć aktywację. Najpierw należy uzyskać klucz licencji z portalu rozwiązań VMware platformy Azure.

1. W portalu rozwiązań VMware platformy Azure przejdź do pozycji **Zarządzanie**  >  **łącznością** , wybierz kartę **HCX** , a następnie wybierz pozycję **Dodaj** .

1. Zaloguj się do lokalnego programu VMware HCX Manager przy użyciu poświadczeń **administratora** `https://HCXManagerIP:9443` . 

   > [!IMPORTANT]
   > Upewnij się, że numer portu został uwzględniony w `9443` adresie IP programu VMware HCX Manager.

1. W obszarze **Licencjonowanie** wprowadź klucz dla **klucza zaawansowanego HCX** .  
   
    > [!NOTE]
    > Program VMware HCX Manager musi mieć otwarty dostęp do Internetu lub skonfigurowany serwer proxy.

1. W polu **Lokalizacja centrum** danych podaj najbliższą lokalizację instalacji programu VMware HCX Manager lokalnie.

1. W polu **Nazwa systemu** zmodyfikuj nazwę lub Zaakceptuj wartość domyślną.
   
1. Wybierz pozycję **tak, Kontynuuj** .
    
1. W obszarze **Połącz swój program vCenter** Podaj nazwę FQDN lub adres IP serwera vCenter oraz odpowiednie poświadczenia, a następnie wybierz pozycję **Kontynuuj** .
   
1. W obszarze **Konfigurowanie logowania jednokrotnego/PSC** Podaj nazwę FQDN lub adres IP kontrolera usług platformy, a następnie wybierz pozycję **Kontynuuj** .
   
   >[!NOTE]
   >Zazwyczaj ten wpis jest taki sam jak nazwa FQDN lub adres IP programu vCenter.

1. Sprawdź, czy wszystkie dane wejściowe są poprawne, a następnie wybierz pozycję **Uruchom ponownie** .
    
   > [!NOTE]
   > Przed wyświetleniem monitu o następny krok nastąpi opóźnienie po ponownym uruchomieniu.

Po ponownym uruchomieniu usług najważniejsze jest, że na wyświetlonym ekranie jest wyświetlany element vCenter wyświetlany jako zielony. Serwery vCenter i SSO muszą mieć odpowiednie parametry konfiguracji, które powinny być takie same jak w poprzednim ekranie.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: Activate HCX](https://www.youtube.com/embed/BkAV_TNYxdE) video.


## <a name="configure-the-vmware-hcx-connector"></a>Konfigurowanie łącznika VMware HCX

Teraz możesz dodać parowanie lokacji, utworzyć sieć i profil obliczeniowy oraz włączyć usługi, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii. 

### <a name="add-a-site-pairing"></a>Dodawanie parowania lokacji

Korzystając z łącznika VMware HCX w centrum danych, możesz połączyć się z programem VMware HCX Cloud Manager w rozwiązaniu VMware Azure. 

1. Zaloguj się do lokalnego programu vCenter i w obszarze **Strona główna** wybierz pozycję **HCX** .

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. W obszarze **infrastruktura** wybierz pozycję **parowanie lokacji** , a następnie wybierz opcję **Połącz z lokacją zdalną** (w środku ekranu). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Wprowadź zanotowany wcześniej adres URL lub adres IP HCX zdalnego, nazwę użytkownika rozwiązania VMware platformy Azure cloudadmin@vsphere.local oraz hasło. Następnie wybierz pozycję **Połącz** .

   > [!NOTE]
   > Aby pomyślnie nawiązać parę lokacji, łącznik HCX musi być w stanie kierować do usługi HCX Cloud Manager IP przez port 443.
   >
   > Hasło jest to samo hasło, które zostało użyte do zalogowania się do programu vCenter. To hasło zostało zdefiniowane na ekranie wdrożenia początkowego.

   Zobaczysz ekran pokazujący, że Twój program HCX Cloud Manager w rozwiązaniu VMware platformy Azure i Łącznik lokalnego HCX są połączone (sparowany).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF.":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: wideo z parowaniem HCX lokacji](https://www.youtube.com/embed/sKizDCRHOko) .



### <a name="create-network-profiles"></a>Tworzenie profilów sieciowych

VMware HCX wdraża podzestaw urządzeń wirtualnych (zautomatyzowany), które wymagają wielu segmentów IP. Podczas tworzenia profilów sieci należy zdefiniować segmenty adresów IP, które zostały zidentyfikowane podczas [przygotowywania segmentów sieci VMware HCX przed wdrożeniem i planowania](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Utworzysz cztery Profile sieciowe:

   - Zarządzanie
   - vMotion
   - Replikacja
   - Łączy

1. W obszarze **infrastruktura** wybierz kolejno pozycje **połączenie**  >  sieć sieci **siatkowa usługi** połączenia  >  **Network Profiles**  >  **Tworzenie profilu sieciowego** .

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Dla każdego profilu sieciowego wybierz sieć i grupę portów, podaj nazwę i Utwórz pulę adresów IP dla tego segmentu. Następnie wybierz przycisk **Utwórz** . 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF.":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz temat [rozwiązanie Azure VMware: wideo HCX profil sieciowy](https://www.youtube.com/embed/NhyEcLco4JY) .


### <a name="create-a-compute-profile"></a>Utwórz profil obliczeń

1. Wybierz kolejno pozycje **obliczenia profile**  >  **Utwórz profil obliczeniowy** .

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Wprowadź nazwę profilu i wybierz pozycję **Kontynuuj** .  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Wybierz usługi do włączenia, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii, a następnie wybierz pozycję **Kontynuuj** .
  
   > [!NOTE]
   > Ogólnie rzecz biorąc nie wprowadzono żadnych zmian.

1. W obszarze **Wybierz zasoby usługi** wybierz co najmniej jeden zasób usługi (klastrów), aby włączyć wybrane usługi VMware HCX.  

1. Gdy widzisz klastry w lokalnym centrum danych, wybierz pozycję **Kontynuuj** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. W obszarze **Wybieranie magazynu** danych wybierz zasób magazynu danych do wdrożenia urządzeń programu VMware HCX Interconnect. Następnie wybierz pozycję **Kontynuuj** .

   W przypadku wybrania wielu zasobów program VMware HCX używa pierwszego wybranego zasobu do momentu wyczerpania jego wydajności.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. W obszarze **Wybierz profil sieci zarządzania** wybierz profil sieci zarządzania, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj** .  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Profil sieci zarządzania pozwala oprogramowaniu VMware HCX na komunikowanie się z programem vCenter. Hosty ESXi można połączyć za pomocą tego profilu.

1. W obszarze **Wybierz profil sieciowy pasma** wybierz profil sieci pasma, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. W obszarze **Wybierz profil sieci vMotion** wybierz profil sieci vMotion, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. W obszarze **Wybierz profil sieci replikacji vSphere** wybierz profil sieci replikacji, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj** .

   W większości przypadków profil sieci replikacji jest taki sam jak profil sieci zarządzania.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. W obszarze **Wybierz przełączniki rozproszone dla rozszerzeń sieci** wybierz rozproszone przełączniki wirtualne, które zawierają maszyny wirtualne do migracji do rozwiązania Azure VMware w sieci rozszerzonej warstwy 2. Następnie wybierz pozycję **Kontynuuj** .

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Przejrzyj reguły połączeń i wybierz pozycję **Kontynuuj** .  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Wybierz pozycję **Zakończ** , aby utworzyć profil obliczeń.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz temat [rozwiązanie Azure VMware: wideo z profilem COMPUTE](https://www.youtube.com/embed/qASXi5xrFzM) .

### <a name="create-a-service-mesh"></a>Tworzenie siatki usługi

Teraz można skonfigurować siatkę usługi między środowiskiem lokalnym i rozwiązaniem Azure VMware SDDC.

   > [!NOTE]
   > Aby pomyślnie nawiązać siatkę usług z rozwiązaniem VMware platformy Azure:
   >
   > Porty UDP 500/4500 są otwarte między lokalnymi adresami profilu sieciowego (pasma) HCX i adresem profilu sieci "pasmem" w chmurze platformy Azure.
   >
   > Sprawdź, czy [HCX wymagane porty](https://ports.vmware.com/home/VMware-HCX).

1. W obszarze **infrastruktura** wybierz **Interconnect** pozycję  >  **Sieć usługi** Interconnect  >  **Utwórz siatkę usług** .    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Przejrzyj wstępnie wypełnione witryny, a następnie wybierz pozycję **Kontynuuj** . 

   >[!NOTE]
   >Jeśli jest to pierwsza konfiguracja sieci usługi, nie trzeba modyfikować tego ekranu.  

1. Wybierz źródłowe i zdalne profile obliczeniowe z listy rozwijanej, a następnie wybierz pozycję **Kontynuuj** .  

   Wybrane opcje określają zasoby, w których maszyny wirtualne mogą korzystać z usług VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Przejrzyj usługi, które zostaną włączone, a następnie wybierz pozycję **Kontynuuj** .  

1. W obszarze **Konfiguracja zaawansowana — zastąpienie profilów sieci pasma** wybierz pozycję **Kontynuuj** .  

   Profile sieci pasma łączą się z siecią, za pomocą której można uzyskać dostęp do urządzeń łączących się z lokacją zdalną.  
  
1. W obszarze **Konfiguracja zaawansowana — Przeskaluj w poziomie urządzenie rozszerzenia sieci** , przejrzyj i wybierz pozycję **Kontynuuj** . 

1. W **zaawansowanej konfiguracji — Inżynieria ruchu** , przejrzyj i wprowadź wszelkie wymagane zmiany, a następnie wybierz pozycję **Kontynuuj** .

1. Przejrzyj Podgląd topologii i wybierz pozycję **Kontynuuj** .

1. Wprowadź nazwę przyjazną dla użytkownika dla tej sieci usługi i wybierz pozycję **Zakończ** , aby zakończyć.  

1. Wybierz pozycję **Wyświetl zadania** w celu monitorowania wdrożenia. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF.":::

   Po pomyślnym zakończeniu wdrożenia usługi siatka usług zobaczysz usługi jako zielone.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Sprawdź kondycję siatki usługi, sprawdzając stan urządzenia. 
1. Wybierz pozycję urządzenia do **łączenia**  >  **Appliances** .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: wideo z usługi Service siatk](https://www.youtube.com/embed/FyZ0d3P_T24) .



### <a name="optional-create-a-network-extension"></a>Obowiązkowe Tworzenie rozszerzenia sieci

Jeśli chcesz rozciągnąć wszystkie sieci z środowiska lokalnego do rozwiązania Azure VMware, wykonaj następujące kroki:

1. W obszarze **usługi** wybierz pozycję **rozszerzenie sieci** , a następnie wybierz pozycję **Utwórz rozszerzenie sieciowe** .

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Wybierz wszystkie sieci, które chcesz rozłożyć na rozwiązanie VMware platformy Azure, a następnie wybierz przycisk **dalej** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF.":::

1. Wprowadź adres IP bramy lokalnej dla każdej z rozszerzanych sieci, a następnie wybierz pozycję **Prześlij** . 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF.":::

   Zakończenie rozszerzenia sieci trwa kilka minut. Gdy to nastąpi, zobaczysz zmianę stanu na **rozszerzenie** .

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Aby zapoznać się z kompleksowym omówieniem tego kroku, zobacz [rozwiązanie Azure VMware: film o rozszerzeniu sieci](https://www.youtube.com/embed/cNlp0f_tTr0) .


## <a name="next-steps"></a>Następne kroki

Jeśli ten punkt został osiągnięty, a stan tunelu łączenia urządzeń jest **ustawiony** na zielony, można migrować maszyny wirtualne rozwiązań VMware platformy Azure i chronić je za pomocą programu VMware HCX. Rozwiązanie VMware platformy Azure obsługuje migracje obciążeń (z rozszerzeniem sieci lub bez niego). Nadal można migrować obciążenia w środowisku vSphere, a także lokalne tworzenie sieci i wdrażanie maszyn wirtualnych w tych sieciach.  

Aby uzyskać więcej informacji na temat korzystania z HCX, przejdź do dokumentacji technicznej programu VMware:

* [Dokumentacja programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [HCX wymagane porty](https://ports.vmware.com/home/VMware-HCX)
