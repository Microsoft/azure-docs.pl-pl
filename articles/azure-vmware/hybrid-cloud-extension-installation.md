---
title: Zainstaluj rozszerzenie chmury hybrydowej (HCX)
description: Skonfiguruj rozwiązanie VMware hybrydowego Extension (HCX) dla chmury prywatnej dla rozwiązania Azure VMware (Automatyczna synchronizacja)
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 3037d12ebbb036098cfc00a42521513bc2df6170
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367550"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Zainstaluj program HCX for Azure VMware Solution

W tym artykule omówiono procedury konfigurowania rozwiązania VMWare hybrydowego Extension (HCX) dla chmury prywatnej (Automatyczna synchronizacja) rozwiązania VMWare. HCX umożliwia migrowanie obciążeń oprogramowania VMware do chmury oraz inne połączone Lokacje za pomocą różnych wbudowanych typów migracji obsługiwanych przez program HCX.

HCX Advanced, instalacja domyślna obsługuje maksymalnie trzy Lokacje zewnętrzne. Jeśli wymagane są więcej niż trzy lokacje, klienci mają możliwość włączenia dodatku HCX Enterprise przez pomoc techniczną. Instalacja przedsiębiorstwa HCX obejmuje dodatkowe opłaty dla klientów po ogólnym udostępnieniu usługi, ale udostępnia [dodatkowe funkcje](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


[Przed rozpoczęciem](#before-you-begin)należy dokładnie zapoznać się z [wymaganiami dotyczącymi wersji oprogramowania](#software-version-requirements)i [wymaganiami wstępnymi](#prerequisites) . 

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"]
> * Wdróż lokalne komórki jajowe HCX
> * Aktywuj i Konfiguruj HCX
> * Skonfiguruj pasmo sieci i siatkę usług
> * Ukończ instalację, sprawdzając stan urządzenia

Po zakończeniu instalacji możesz wykonać czynności opisane w kolejnych krokach przedstawionych na końcu tego artykułu.  

## <a name="before-you-begin"></a>Przed rozpoczęciem
    
* Zapoznaj się z [serią samouczka](tutorial-network-checklist.md) podstawowe oprogramowanie do automatycznej synchronizacji (SDDC)
* Zapoznaj się z [dokumentacją programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) , w tym podręczniku użytkownika HCX
* Przejrzyj dokumentację oprogramowania VMware [migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Opcjonalnie Przejrzyj [zagadnienia dotyczące wdrażania oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Zapoznaj VMware vSphere się z tematem "HCX", na przykład z [serii blogów](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) w witrynie HCX. 
* Zamów aktywację automatycznej synchronizacji HCX Enterprise za pomocą kanałów pomocy technicznej dotyczącej automatycznej synchronizacji.

Ustalanie rozmiarów obciążeń związanych z zasobami obliczeniowymi i magazynem jest ważnym etapem planowania podczas przygotowywania do korzystania z rozwiązania do automatycznej synchronizacji w chmurze prywatnej HCX. Zaadresuj krok zmiany wielkości w ramach wstępnego planowania środowiska chmury prywatnej.   

## <a name="software-version-requirements"></a>Wymagania dotyczące wersji oprogramowania
Składniki infrastruktury muszą mieć uruchomioną wymaganą wersję minimalną. 
                                                         
| Typ składnika    | Wymagania dotyczące środowiska źródłowego    | Wymagania środowiska docelowego   |
| --- | --- | --- |
| Program vCenter Server   | 5.1<br/><br/>W przypadku używania 5,5 U1 lub wcześniejszych należy użyć autonomicznego interfejsu użytkownika HCX dla operacji HCX.  | 6,0 U2 i więcej   |
| ESXi   | 5.0    | ESXi 6,0 i nowsze   |
| NSX    | W przypadku rozszerzenia sieci HCX przełączników logicznych w źródle: NSXv 6.2 + lub NSX-T 2.4 +   | NSXv 6.2 + lub NSX-T 2.4 +<br/><br/>W przypadku routingu HCX zbliżeniowe: NSXv 6.4 + (Routing bliskości nie jest obsługiwany w przypadku NSX-T) |
| Dyrektor vCloud   | Niewymagana — brak współdziałania z vCloud Director w lokacji źródłowej | W przypadku integracji środowiska docelowego z vCloud dyrektorem minimalnym jest 9.1.0.2.  |

## <a name="prerequisites"></a>Wymagania wstępne

* Globalne zasięgi należy skonfigurować między obwodami SDDC lokalnego i automatyczna synchronizacja.

* Wszystkie wymagane porty powinny być otwarte między środowiskiem lokalnym i SDDC (zobacz [dokumentację programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Jeden adres IP dla programu HCX Manager w środowisku lokalnym i co najmniej dwa adresy IP dla urządzenia Interconnect (IX) i rozszerzenia sieci (NE).

* Lokalne HCX IX i NE urządzenia powinny być w stanie dotrzeć do infrastruktury vCenter i ESXi.

* Aby można było wdrożyć urządzenie WAN Interconnect (oprócz bloku adresów sieciowych/22) używanego do wdrażania SDDC w Azure Portal, HCX wymaga bloku/29. Upewnij się, że to wymaganie należy wziąć pod uwagę w planowaniu sieci.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Wdrażanie programu VMware HCX komórki jajowe w środowisku lokalnym

1. Zaloguj się do wersji automatyczna synchronizacja SDDC vCenter i wybierz pozycję **HCX**.

    ![Wybierz HCX w programie, aby przeprowadzić Autosynchronizacja programu vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. W obszarze **Administracja**wybierz pozycję **aktualizacje systemu** , a następnie wybierz pozycję **Żądaj pobrania linku** , aby pobrać plik komórek jajowych programu VMware HCX.

    ![Pobierz aktualizacje systemu](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Następnie przejdź do lokalnego programu vCenter i wybierz szablon OVF do wdrożenia w lokalnym programie vCenter.  

    ![Wybierz szablon OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Wybierz nazwę i lokalizację, a następnie wybierz zasób/klaster, w którym należy wdrożyć HCX. Następnie przejrzyj szczegóły i wymagane zasoby.  

    ![Przejrzyj szczegóły szablonu](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Zapoznaj się z postanowieniami licencyjnymi, a jeśli zgadzasz się, wybierz pozycję wymagane magazyn i sieć. Następnie wybierz pozycję **Dalej**.

1. W obszarze **Dostosowywanie szablonu**wprowadź wszystkie wymagane informacje. 

    ![Dostosuj szablon](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Wybierz pozycję **dalej**, sprawdź konfigurację i wybierz pozycję **Zakończ** , aby wdrożyć HCX komórki jajowe.

## <a name="activate-hcx"></a>Aktywuj HCX

Po zakończeniu instalacji wykonaj następujące czynności.

1. Zaloguj się do lokalnego programu HCX Manager pod adresem `https://HCXManagerIP:9443` i zaloguj się przy użyciu nazwy użytkownika i hasła. 

   > [!IMPORTANT]
   > Upewnij się, że numer portu został uwzględniony w `9443` adresie IP HCX Manager.

1. W obszarze **Licencjonowanie**wprowadź **klucz zaawansowany HCX**.  

    ![Wprowadź klucz HCX](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX musi mieć skonfigurowany otwarty dostęp do Internetu lub serwer proxy.

1. W programie **vCenter**, w razie konieczności, Edytuj informacje programu vCenter.

    ![Konfigurowanie serwera VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. W polu **Lokalizacja centrum**danych, w razie konieczności, Edytuj lokalizację centrum danych.

    ![Lokalizacja bazy danych](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Konfigurowanie HCX 

1. Zaloguj się do lokalnego programu vCenter i w obszarze **Strona główna**wybierz pozycję **HCX**.

    ![HCX w programie VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. W obszarze **infrastruktura**wybierz pozycję **parowanie lokacji**  >  **Dodaj parowanie lokacji**.

    ![Dodaj parowanie lokacji](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Wprowadź adres URL zdalnego HCX lub adres IP, automatyczna synchronizacja cloudadmin nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz**.

   System pokazuje połączoną lokację.
   
    ![Połączenie z lokacją](./media/hybrid-cloud-extension-installation/site-connection.png)

1. W obszarze **infrastruktura**wybierz kolejno pozycje **połączenie**  >  sieć sieci**siatkowa usługi**połączenia  >  **Network Profiles**  >  **Tworzenie profilu sieciowego**.

    ![Utwórz profil sieciowy](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Dla nowego profilu sieciowego wprowadź zakresy HCX IX i NE adresy IP (co najmniej dwa adresy IP są wymagane w przypadku urządzeń z systemem IX i NE).
    
   ![Wprowadź zakresy adresów IP](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > Urządzenie rozszerzenia sieci (HCX-NE) zawiera relację "jeden do jednego" z rozproszonym przełącznikiem wirtualnym (DVS).  

1. Teraz wybierz pozycję **COMPUTE profile**  >  **Utwórz profil obliczeniowy**.

1. Wprowadź nazwę profilu obliczeniowego i wybierz pozycję **Kontynuuj**.  

    ![Utwórz profil obliczeń](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Wybierz usługi do włączenia, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii, a następnie wybierz pozycję **Kontynuuj**.

    ![Wybierz usługi](./media/hybrid-cloud-extension-installation/select-services.png)

1. W obszarze **Wybierz zasoby usługi**wybierz co najmniej jeden zasób usługi, dla którego powinny zostać włączone wybrane usługi HCX. Wybierz pozycję **Continue** (Kontynuuj).
    
   ![Wybieranie zasobów usługi](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Wybierz konkretne klastry, w których źródłowe maszyny wirtualne są przeznaczone do migracji przy użyciu HCX.

1. Wybierz pozycję **Magazyn** danych i wybierz pozycję **Kontynuuj**. 
      
    Wybierz każdy zasób obliczeniowy i magazyn do wdrożenia urządzeń HCX Interconnect. W przypadku wybrania wielu zasobów HCX używa pierwszego wybranego zasobu do wyczerpania pojemności.  
    
    ![Wybieranie zasobów wdrożenia](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Wybierz profil sieci zarządzania utworzony w obszarze **Profile sieciowe** i wybierz pozycję **Kontynuuj**.  
      
    Wybierz profil sieciowy, za pomocą którego można uzyskać dostęp do interfejsu zarządzania programu vCenter i hostów ESXi. Jeśli ten profil sieciowy nie został jeszcze zdefiniowany, możesz go utworzyć w tym miejscu.  
    
    ![Wybierz pozycję Zarządzanie profilem sieciowym](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Kontynuuj**.
      
    Wybierz co najmniej jeden profil sieciowy, aby mieć jeden z następujących warunków:  
    * Urządzenia do łączenia w zdalnej lokacji można połączyć za pośrednictwem tej sieci  
    * Urządzenia zdalne mogą łączyć się z lokalnymi urządzeniami programu InterConnect za pośrednictwem tej sieci.  
    
    Jeśli istnieją sieci typu punkt-punktowe, takie jak bezpośrednie łączenie, które nie są współużytkowane przez wiele lokacji, można pominąć ten krok, ponieważ profile obliczeniowe są udostępniane wielu lokacjom. W takich przypadkach profile sieci pasma można przesłonić i określić podczas tworzenia siatki usługi połączenia.  
    
    ![Wybierz profil sieciowy pasma](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Wybierz pozycję **profil sieci vMotion** i wybierz pozycję **Kontynuuj**.
      
   Wybierz profil sieciowy, za pomocą którego można uzyskać dostęp do interfejsu vMotion hostów ESXi. Jeśli ten profil sieciowy nie został jeszcze zdefiniowany, możesz go utworzyć w tym miejscu. Jeśli nie masz sieci vMotion, wybierz pozycję **Zarządzanie profilem sieciowym**.  
    
   ![Wybierz profil sieci vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. W obszarze **Wybierz profil sieci replikacji vSphere**wybierz profil sieci interfejs VSphere replikacji ESXi hosty, a następnie wybierz pozycję **Kontynuuj**.
      
   W większości przypadków ten profil jest taki sam jak profil sieci zarządzania.  
    
   ![Wybierz profil sieciowy replikacji vSphere](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. W obszarze **Wybierz przełączniki rozproszone dla rozszerzeń sieci**wybierz usługę DVS, w której znajdują się sieci maszyn wirtualnych, które będą zintegrowane i są połączone.  Wybierz pozycję **Continue** (Kontynuuj).  
      
    ![Wybierz rozproszone przełączniki wirtualne](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Przejrzyj reguły połączeń i wybierz pozycję **Kontynuuj**.  

    ![Utwórz profil obliczeń](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Wybierz pozycję **Zakończ** , aby utworzyć profil obliczeń.

## <a name="configure-network-uplink"></a>Konfiguruj pasmo sieci

Teraz Skonfiguruj zmianę profilu sieciowego w obszarze automatyczna synchronizacja SDDC dla pasma sieci.

1. Zaloguj się do SDDC NSX-T, aby utworzyć nowy przełącznik logiczny, lub Użyj istniejącego przełącznika logicznego, który może być używany na potrzeby pasma sieciowego między środowiskiem lokalnym a SDDC.

1. Utwórz profil sieciowy dla pasma HCX w obszarze automatyczna synchronizacja SDDC, który może być używany w przypadku lokalnego programu do automatycznej synchronizacji informacji.  
    
   ![Utwórz profil sieciowy dla pasma](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Wprowadź nazwę profilu sieciowego oraz co najmniej 4-5 bezpłatnych adresów IP na podstawie wymaganego rozszerzenia sieci L2.  
    
   ![Konfiguruj profil sieciowy dla pasma](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Wybierz pozycję **Utwórz** , aby ukończyć konfigurację SDDC synchronizacji

## <a name="configure-service-mesh"></a>Konfiguruj siatkę usług

Teraz należy skonfigurować siatkę usługi między środowiskiem lokalnym i SDDC.

1. Zaloguj się do wersji automatyczna synchronizacja SDDC vCenter i wybierz pozycję **HCX**.

2. W obszarze **infrastruktura**wybierz **Interconnect**pozycję  >  **Sieć usługi**Interconnect  >  **Utwórz** sieć, aby skonfigurować profile sieci i obliczeń utworzone w poprzednich krokach.    
      
    ![Konfiguruj siatkę usług](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Wybierz sparowane lokacje, aby włączyć możliwość hybrydowej, a następnie wybierz pozycję **Kontynuuj**.   
    
    ![Wybieranie witryn sparowanych](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Wybierz źródłowe i zdalne profile obliczeniowe, aby włączyć usługi hybrydowe, a następnie wybierz pozycję **Kontynuuj**.
      
    Wybrane opcje określają zasoby, w których maszyny wirtualne mogą korzystać z usług HCX Services.  
      
    ![Włącz usługi hybrydowe](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Wybierz pozycję usługi do włączenia i wybierz pozycję **Kontynuuj**.  
      
    ![Wybierz usługi HCX Services](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. W obszarze **Konfiguracja zaawansowana — Przesłoń profile sieci pasma** wybierz pozycję **Kontynuuj**.  
      
    Profile sieci pasma są używane do łączenia się z siecią za pośrednictwem programu, w którym można uzyskać dostęp do urządzeń łączących się z lokacją zdalną.  
      
    ![Przesłoń profile pasma](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Wybierz pozycję **Konfiguruj urządzenie rozszerzenia sieci w poziomie**. 
      
    ![Skalowanie sieciowe w poziomie](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Wprowadź liczbę urządzeń odpowiadającą liczbie przełączników programu DVS.  
      
    ![Konfiguruj liczbę urządzeń](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Wybierz pozycję **Kontynuuj** , aby pominąć.  
      
    ![Konfigurowanie inżynierii ruchu](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Przejrzyj Podgląd topologii i wybierz pozycję **Kontynuuj**. 

11. Wprowadź nazwę przyjazną dla użytkownika dla tej sieci usługi i wybierz pozycję **Zakończ** , aby zakończyć.  
      
    ![Ukończ siatkę usług](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Zostanie wdrożona i skonfigurowana siatka usług.  
      
    ![Wdrożona siatka usługi](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Sprawdź stan urządzenia
Aby sprawdzić stan urządzenia, wybierz pozycję urządzenia do **łączenia**  >  **Appliances**. 
      
![Stan urządzenia](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Następne kroki

Gdy **tunel** połączenia urządzenia jest w stanie **up** i zielonym, możesz przystąpić do migracji i ochrony maszyn wirtualnych automatycznej synchronizacji przy użyciu programu HCX. Zobacz [dokumentację oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html) i [Migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) w dokumentacji technicznej programu VMware.
