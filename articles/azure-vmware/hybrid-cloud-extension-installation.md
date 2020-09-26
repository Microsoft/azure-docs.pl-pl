---
title: Zainstaluj program VMware HCX
description: Skonfiguruj rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware
ms.topic: how-to
ms.date: 09/24/2020
ms.openlocfilehash: cdeffa41db5aac597d8dfcf3a735cbeb7f0d8a8e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370848"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Zainstaluj program HCX for Azure VMware Solution

W tym artykule omówiono procedury konfigurowania rozwiązania VMWare HCX dla chmury prywatnej rozwiązania Azure VMWare. HCX umożliwia migrowanie obciążeń oprogramowania VMware do chmury oraz inne połączone Lokacje za pomocą różnych wbudowanych typów migracji obsługiwanych przez program HCX.

HCX Advanced — instalacja domyślna obsługuje maksymalnie trzy połączenia z lokacją (lokalnie lub w chmurze do chmury). Jeśli wymagane są więcej niż trzy połączenia witryny lub HCX [funkcje przedsiębiorstwa](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) , klienci mogą korzystać z opcji włączenia dodatku Enterprise HCX w wersji zapoznawczej. Program HCX EE jest dostępny z funkcją automatycznej synchronizacji jako funkcji/usługi w wersji zapoznawczej. Podczas gdy HCX EE for replika jest w wersji zapoznawczej, jest to bezpłatna funkcja/usługa, która podlega zapewnieniu warunków i postanowień usługi. Po rozpoczęciu usługi HCX EE uzyskasz 30-dniowy okres, w którym rozliczenia zostaną przełączone. Dostępna jest również opcja przełączania/rezygnacji z usługi.


[Przed rozpoczęciem](#before-you-begin)należy dokładnie zapoznać się z [wymaganiami dotyczącymi wersji oprogramowania](#software-version-requirements)i [wymaganiami wstępnymi](#prerequisites) . 

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"]
> * Wdróż lokalne HCX komórki jajowe (łącznik)
> * Aktywuj i Konfiguruj HCX
> * Skonfiguruj pasmo sieci i siatkę usług
> * Ukończ instalację, sprawdzając stan urządzenia

Po zakończeniu instalacji możesz wykonać czynności opisane w kolejnych krokach przedstawionych na końcu tego artykułu.  

## <a name="before-you-begin"></a>Przed rozpoczęciem
    
* Zapoznaj się z podstawową [serią samouczków](tutorial-network-checklist.md)dotyczących oprogramowania VMware (SDDC).
* Zapoznaj się z [dokumentacją programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , w tym podręczniku użytkownika HCX.
* Przejrzyj dokumentację oprogramowania VMware [migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Zapoznaj się z [zaleceniami dotyczącymi wdrażania programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Zapoznaj VMware vSphere się z tematem "HCX", na przykład z [serii blogów](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) w witrynie HCX. 
* Zażądaj aktywacji rozwiązania Azure VMware HCX Enterprise za pomocą kanałów obsługi rozwiązań VMware platformy Azure.

Ustalanie rozmiarów obciążeń związanych z zasobami obliczeniowymi i magazynem jest ważnym etapem planowania podczas przygotowywania do korzystania z rozwiązania HCX chmury prywatnej w chmurze platformy Azure. Zaadresuj krok zmiany wielkości w ramach wstępnego planowania środowiska chmury prywatnej. 

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

* ExpressRoute Global Reach należy skonfigurować między środowiskiem lokalnym i rozwiązaniem Azure VMware SDDC ExpressRoute.

* Wszystkie wymagane porty powinny być otwarte między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure SDDC (zobacz [dokumentację oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Jeden adres IP dla programu HCX Manager w środowisku lokalnym i co najmniej dwa adresy IP dla urządzenia Interconnect (IX) i rozszerzenia sieci (NE).

* Lokalne HCX IX i NE urządzenia powinny być w stanie dotrzeć do infrastruktury vCenter i ESXi.

* Aby można było wdrożyć urządzenie WAN Interconnect, określone zakresy CIDR są już przydzieleni od klienta dostarczonego przez \ 22 dla tworzenia chmury prywatnej.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Wdrażanie programu VMware HCX komórki jajowe w środowisku lokalnym

1. Zaloguj się do usługi Azure VMware Solution HCX Manager na `https://x.x.x.9` porcie 443 przy użyciu poświadczeń użytkownika **cloudadmin** , a następnie przejdź do **pomocy technicznej**.

1. Wybierz link pobierania dla pliku komórek jajowych programu VMware HCX, który ma zostać wdrożony w programie vCenter.

1. Przejdź do lokalnego programu vCenter i wybierz nowo pobrany szablon OVF do wdrożenia w lokalnym programie vCenter.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="Następnie przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.":::

1. Wybierz nazwę i lokalizację, a następnie wybierz zasób/klaster, w którym należy wdrożyć HCX. Następnie przejrzyj szczegóły i wymagane zasoby.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text=" Wybierz nazwę i lokalizację, a następnie wybierz zasób/klaster, w którym należy wdrożyć HCX. Następnie przejrzyj szczegóły i wymagane zasoby.":::

1. Zapoznaj się z postanowieniami licencyjnymi, a jeśli zgadzasz się, wybierz pozycję wymagane magazyn i sieć. Następnie wybierz pozycję **Dalej**.

1. W obszarze **Dostosowywanie szablonu**wprowadź wszystkie wymagane informacje. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="W obszarze Dostosowywanie szablonu wprowadź wszystkie wymagane informacje.":::

1. Wybierz pozycję **dalej**, sprawdź konfigurację i wybierz pozycję **Zakończ** , aby wdrożyć HCX komórki jajowe.

## <a name="activate-hcx"></a>Aktywuj HCX

Po zakończeniu instalacji wykonaj następujące czynności.

1. Zaloguj się do lokalnego programu HCX Manager pod adresem `https://HCXManagerIP:9443` i zaloguj się przy użyciu poświadczeń nazwy użytkownika **administratora** . 

   > [!IMPORTANT]
   > Upewnij się, że numer portu został uwzględniony w `9443` adresie IP HCX Manager.

1. W obszarze **Licencjonowanie**wprowadź **klucz zaawansowany HCX**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="W obszarze Licencjonowanie wprowadź klucz zaawansowany HCX.":::
    
    > [!NOTE]
    > HCX musi mieć skonfigurowany otwarty dostęp do Internetu lub serwer proxy.

1. W programie **vCenter**, w razie konieczności, Edytuj informacje programu vCenter.

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="W programie vCenter, w razie konieczności, Edytuj informacje programu vCenter.":::

1. W polu **Lokalizacja centrum**danych, w razie konieczności, Edytuj lokalizację centrum danych.

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="W polu Lokalizacja centrum danych, w razie konieczności, Edytuj lokalizację centrum danych.":::

## <a name="configure-hcx"></a>Konfigurowanie HCX 

1. Zaloguj się do lokalnego programu vCenter i w obszarze **Strona główna**wybierz pozycję **HCX**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="Zaloguj się do lokalnego programu vCenter i w obszarze Strona główna wybierz pozycję HCX.":::

1. W obszarze **infrastruktura**wybierz pozycję **parowanie lokacji**  >  **Dodaj parowanie lokacji**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="W obszarze infrastruktura wybierz pozycję parowanie lokacji > Dodaj parowanie lokacji.":::

1. Wprowadź adres URL zdalnego HCX lub adres IP, rozwiązanie VMware platformy Azure cloudadmin nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz**.

   System pokazuje połączoną lokację.

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="System pokazuje połączoną lokację.":::

1. W obszarze **infrastruktura**wybierz kolejno pozycje **połączenie**  >  sieć sieci**siatkowa usługi**połączenia  >  **Network Profiles**  >  **Tworzenie profilu sieciowego**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="W obszarze infrastruktura wybierz pozycję Interconnect > sieć usługi wiele witryn > Profile sieciowe > Utwórz profil sieciowy.":::

1. Dla nowego profilu sieciowego wprowadź zakresy HCX IX i NE adresy IP (co najmniej dwa adresy IP są wymagane w przypadku urządzeń z systemem IX i NE).

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="Dla nowego profilu sieciowego wprowadź zakresy HCX IX i NE adresy IP (co najmniej dwa adresy IP są wymagane w przypadku urządzeń z systemem IX i NE).":::
  
   > [!NOTE]
   > Urządzenie rozszerzenia sieci (HCX-NE) zawiera relację "jeden do jednego" z rozproszonym przełącznikiem wirtualnym (DVS).  

1. Teraz wybierz pozycję **COMPUTE profile**  >  **Utwórz profil obliczeniowy**.

1. Wprowadź nazwę profilu obliczeniowego i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="Wprowadź nazwę profilu obliczeniowego i wybierz pozycję Kontynuuj.":::

1. Wybierz usługi do włączenia, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii, a następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="Wybierz usługi do włączenia, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii, a następnie wybierz pozycję Kontynuuj.":::

1. W obszarze **Wybierz zasoby usługi**wybierz co najmniej jeden zasób usługi, dla którego powinny zostać włączone wybrane usługi HCX. Wybierz pozycję **Continue** (Kontynuuj).

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="W obszarze Wybierz zasoby usługi wybierz co najmniej jeden zasób usługi, dla którego powinny zostać włączone wybrane usługi HCX. Wybierz pozycję Kontynuuj.":::
  
   > [!NOTE]
   > Wybierz konkretne klastry, w których źródłowe maszyny wirtualne są przeznaczone do migracji przy użyciu HCX.

1. Wybierz pozycję **Magazyn** danych i wybierz pozycję **Kontynuuj**. 
      
   Wybierz każdy zasób obliczeniowy i magazyn do wdrożenia urządzeń HCX Interconnect. W przypadku wybrania wielu zasobów HCX używa pierwszego wybranego zasobu do wyczerpania pojemności.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="Wybierz każdy zasób obliczeniowy i magazyn do wdrożenia urządzeń HCX Interconnect. W przypadku wybrania wielu zasobów HCX używa pierwszego wybranego zasobu do wyczerpania pojemności.":::

1. Wybierz profil sieci zarządzania utworzony w obszarze **Profile sieciowe** i wybierz pozycję **Kontynuuj**.  
      
   Wybierz profil sieciowy, za pomocą którego można uzyskać dostęp do interfejsu zarządzania programu vCenter i hostów ESXi. Jeśli ten profil sieciowy nie został jeszcze zdefiniowany, możesz go utworzyć w tym miejscu.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="Wybierz profil sieciowy, za pomocą którego można uzyskać dostęp do interfejsu zarządzania programu vCenter i hostów ESXi. Jeśli ten profil sieciowy nie został jeszcze zdefiniowany, możesz go utworzyć w tym miejscu.":::

1. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Kontynuuj**.
      
   Wybierz co najmniej jeden profil sieciowy, aby mieć jeden z następujących warunków:  
   * Urządzenia do łączenia w zdalnej lokacji można połączyć za pośrednictwem tej sieci  
   * Urządzenia zdalne mogą łączyć się z lokalnymi urządzeniami programu InterConnect za pośrednictwem tej sieci.  
    
   Jeśli istnieją sieci typu punkt-punktowe, takie jak bezpośrednie łączenie, które nie są współużytkowane przez wiele lokacji, można pominąć ten krok, ponieważ profile obliczeniowe są udostępniane wielu lokacjom. W takich przypadkach profile sieci pasma można przesłonić i określić podczas tworzenia siatki usługi połączenia.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="Wybierz pozycję Sieć, a następnie wybierz pozycję Kontynuuj.":::

1. Wybierz pozycję **profil sieci vMotion** i wybierz pozycję **Kontynuuj**.
      
   Wybierz profil sieciowy, za pomocą którego można uzyskać dostęp do interfejsu vMotion hostów ESXi. Jeśli ten profil sieciowy nie został jeszcze zdefiniowany, możesz go utworzyć w tym miejscu. Jeśli nie masz sieci vMotion, wybierz pozycję **Zarządzanie profilem sieciowym**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="Wybierz pozycję profil sieci vMotion i wybierz pozycję Kontynuuj.":::

1. W obszarze **Wybierz profil sieci replikacji vSphere**wybierz profil sieci interfejs VSphere replikacji ESXi hosty, a następnie wybierz pozycję **Kontynuuj**.
      
   W większości przypadków ten profil jest taki sam jak profil sieci zarządzania.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="W obszarze Wybierz profil sieci replikacji vSphere wybierz profil sieci interfejs vSphere replikacji ESXi hosty, a następnie wybierz pozycję Kontynuuj.":::

1. W obszarze **Wybierz przełączniki rozproszone dla rozszerzeń sieci**wybierz usługę DVS, w której znajdują się sieci maszyn wirtualnych, które będą zintegrowane i są połączone.  Wybierz pozycję **Continue** (Kontynuuj).  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="W obszarze Wybierz przełączniki rozproszone dla rozszerzeń sieci wybierz usługę DVS, w której znajdują się sieci maszyn wirtualnych, które będą zintegrowane i są połączone.  Wybierz pozycję Kontynuuj.":::

1. Przejrzyj reguły połączeń i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="Przejrzyj reguły połączeń i wybierz pozycję Kontynuuj.":::

1.  Wybierz pozycję **Zakończ** , aby utworzyć profil obliczeń.

## <a name="configure-network-uplink"></a>Konfiguruj pasmo sieci

Teraz Skonfiguruj zmianę profilu sieciowego w SDDC rozwiązania Azure VMware dla pasma sieci.

1. Zaloguj się do SDDC NSX-T, aby utworzyć nowy przełącznik logiczny, lub Użyj istniejącego przełącznika logicznego, który może być używany dla pasma sieciowego między środowiskiem lokalnym i platformą Azure VMware SDDC.

1. Utwórz profil sieciowy dla pasma HCX w rozwiązaniu Azure VMware SDDC, którego można użyć do komunikacji z platformą Azure VMware SDDC Communications.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="Utwórz profil sieciowy dla pasma HCX w rozwiązaniu Azure VMware SDDC, którego można użyć do komunikacji z platformą Azure VMware SDDC Communications.":::

1. Wprowadź nazwę profilu sieciowego oraz co najmniej 4-5 bezpłatnych adresów IP na podstawie wymaganego rozszerzenia sieci L2.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="Wprowadź nazwę profilu sieciowego oraz co najmniej 4-5 bezpłatnych adresów IP na podstawie wymaganego rozszerzenia sieci L2.":::

1. Wybierz pozycję **Utwórz** , aby ukończyć konfigurację SDDC rozwiązania Azure VMware

## <a name="configure-service-mesh"></a>Konfiguruj siatkę usług

Teraz należy skonfigurować siatkę usługi między środowiskiem lokalnym i rozwiązaniem Azure VMware SDDC.

1. Zaloguj się do rozwiązania Azure VMware SDDC vCenter i wybierz pozycję **HCX**.

2. W obszarze **infrastruktura**wybierz **Interconnect**pozycję  >  **Sieć usługi**Interconnect  >  **Utwórz** sieć, aby skonfigurować profile sieci i obliczeń utworzone w poprzednich krokach.    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="W obszarze infrastruktura wybierz pozycję Interconnect > usługi siatka > Utwórz siatkę usług, aby skonfigurować profile sieci i obliczeń utworzone w poprzednich krokach.":::

3. Wybierz sparowane lokacje, aby włączyć możliwość hybrydowej, a następnie wybierz pozycję **Kontynuuj**.   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="Wybierz sparowane lokacje, aby włączyć możliwość hybrydowej, a następnie wybierz pozycję Kontynuuj.":::

4. Wybierz źródłowe i zdalne profile obliczeniowe, aby włączyć usługi hybrydowe, a następnie wybierz pozycję **Kontynuuj**.
      
   Wybrane opcje określają zasoby, w których maszyny wirtualne mogą korzystać z usług HCX Services.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="Wybrane opcje określają zasoby, w których maszyny wirtualne mogą korzystać z usług HCX Services.":::

5. Wybierz pozycję usługi do włączenia i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="Wybierz pozycję usługi do włączenia i wybierz pozycję Kontynuuj.":::

6. W obszarze **Konfiguracja zaawansowana — Przesłoń profile sieci pasma** wybierz pozycję **Kontynuuj**.  
      
   Profile sieci pasma są używane do łączenia się z siecią za pośrednictwem programu, w którym można uzyskać dostęp do urządzeń łączących się z lokacją zdalną.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="Profile sieci pasma są używane do łączenia się z siecią za pośrednictwem programu, w którym można uzyskać dostęp do urządzeń łączących się z lokacją zdalną.":::

7. Wybierz pozycję **Konfiguruj urządzenie rozszerzenia sieci w poziomie**. 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="Wybierz pozycję Konfiguruj urządzenie rozszerzenia sieci w poziomie.":::

8. Wprowadź liczbę urządzeń odpowiadającą liczbie przełączników programu DVS.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="Wprowadź liczbę urządzeń odpowiadającą liczbie przełączników programu DVS.":::

9. Wybierz pozycję **Kontynuuj** , aby pominąć.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="Wybierz pozycję Kontynuuj, aby pominąć.":::

10. Przejrzyj Podgląd topologii i wybierz pozycję **Kontynuuj**. 

11. Wprowadź nazwę przyjazną dla użytkownika dla tej sieci usługi i wybierz pozycję **Zakończ** , aby zakończyć.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="Ukończ siatkę usług":::

   Zostanie wdrożona i skonfigurowana siatka usług.  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="Wdrożona siatka usługi":::

## <a name="check-appliance-status"></a>Sprawdź stan urządzenia
Aby sprawdzić stan urządzenia, wybierz pozycję urządzenia do **łączenia**  >  **Appliances**. 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="Sprawdź stan urządzenia.":::

## <a name="next-steps"></a>Następne kroki

Gdy **tunel** połączenia urządzenia jest w stanie **up** i zielonym, możesz przystąpić do migracji i ochrony maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu HCX. Zobacz [dokumentację oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) i [Migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) w dokumentacji technicznej programu VMware.
