---
title: Samouczek — wdrażanie i Konfigurowanie programu VMware HCX
description: Dowiedz się, jak wdrożyć i skonfigurować rozwiązanie VMware HCX dla chmury prywatnej rozwiązania Azure VMware.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ab59b4a29f8ba372c1892613d5e0461bf3fdc512
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869093"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Wdrażanie i konfigurowanie usługi VMware HCX

W tym artykule opisano sposób wdrażania i konfigurowania lokalnego łącznika VMware HCX dla chmury prywatnej rozwiązania Azure VMware. Dzięki oprogramowaniu VMware HCX można migrować obciążenia oprogramowania VMware do rozwiązania VMware platformy Azure i innych połączonych lokacji za pomocą różnych typów migracji. Ponieważ rozwiązanie VMware platformy Azure wdraża i konfiguruje program HCX Cloud Manager, należy pobrać, aktywować i skonfigurować łącznik HCX w lokalnym centrum danych VMware.

Łącznik programu VMware HCX Advanced został wstępnie wdrożony w rozwiązaniu VMware platformy Azure. Obsługuje ona maksymalnie trzy połączenia z lokacją (lokalnie do chmury lub w chmurze do chmury). Jeśli potrzebujesz więcej niż trzech połączeń witryny, Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#create/Microsoft.Support) , aby włączyć dodatek [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) .  

>[!TIP]
>Chociaż maksymalne narzędzie konfiguracji oprogramowania VMware zawiera opis par witryn, które są maksymalnie 25 między łącznikiem lokalnym a programem Cloud Manager, licencje te ograniczają się do trzech dla HCX Advanced i 10 dla HCX Enterprise Edition.

>[!NOTE]
>Program VMware HCX Enterprise jest dostępny w ramach rozwiązania Azure VMware jako usługi w wersji zapoznawczej. Jest ona bezpłatna i podlega postanowieniom dotyczącym usługi w wersji zapoznawczej. Po ogólnym udostępnieniu usługi VMware HCX Enterprise uzyskasz 30-dniową informację o tym, że opłaty zostaną przełączone. Będziesz również mieć możliwość wyłączenia lub rezygnacji z usługi. Nie ma żadnej prostej ścieżki obniżenia poziomu oprogramowania VMware HCX Enterprise do programu VMware HCX Advanced. W przypadku podjęcia decyzji o obniżeniu poziomu należy przeprowadzić ponowne wdrożenie, co potrwa przestoje.

Najpierw [zapoznaj się z](#before-you-begin) [wymaganiami dotyczącymi wersji oprogramowania](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)i [wymaganiami wstępnymi](#prerequisites). 

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"]
> * Pobierz komórki jajowe łącznika VMware HCX.
> * Wdróż lokalne komórki jajowe VMware HCX (łącznik VMware HCX).
> * Aktywuj łącznik VMware HCX.
> * Parowanie lokalnego łącznika VMware HCX z rozwiązaniem platformy Azure VMware HCX Cloud Manager.
> * Skonfiguruj połączenie (profil sieciowy, profil obliczeniowy i sieć usługi).
> * Ukończ instalację, sprawdzając stan urządzenia i sprawdzając, czy migracja jest możliwa.

Po zakończeniu postępuj zgodnie z zalecanymi następnymi krokami na końcu tego artykułu.  

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Podczas przygotowywania wdrożenia zalecamy przejrzenie następującej dokumentacji programu VMware:

* [Podręcznik użytkownika programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Zagadnienia dotyczące wdrażania oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [Seria blogów VMware — migracja w chmurze](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Porty sieciowe wymagane przez oprogramowanie VMware HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli planujesz korzystanie z programu VMware HCX Enterprise, upewnij się, że zażądano aktywacji za pomocą kanałów obsługi rozwiązań VMware platformy Azure.


### <a name="on-premises-vsphere-environment"></a>Lokalne środowisko vSphere

Upewnij się, że lokalne środowisko vSphere (środowisko źródłowe) spełnia [wymagania minimalne](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Sieć i porty

* [Usługa Azure ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) jest konfigurowana między lokalnymi i opartymi na platformie Azure maszynami prywatnymi.

* [Wszystkie wymagane porty](https://ports.vmware.com/home/VMware-HCX) są otwarte do komunikacji między składnikami lokalnymi i rozwiązaniem Azure VMware Private.

### <a name="ip-addresses"></a>Adresy IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Pobierz komórki jajowe łącznika VMware HCX

Przed wdrożeniem urządzenia wirtualnego w lokalnym programie vCenter należy pobrać komórki jajowe łącznika VMware HCX.  

1. W Azure Portal Wybierz chmurę prywatną rozwiązania VMware platformy Azure. 

1. Wybierz pozycję **Zarządzaj**  >  **łącznością** i wybierz kartę **HCX** , aby zidentyfikować adres IP Menedżera HCX programu Azure VMware. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Zrzut ekranu przedstawiający adres IP programu VMware HCX." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Wybierz pozycję **Zarządzaj**  >  **tożsamością**. 

   Adresy URL i poświadczenia użytkownika dla usługi Private Cloud vCenter i NSX-T Menedżera.

   > [!TIP]
   > Hasło vCenter zostało zdefiniowane podczas konfigurowania chmury prywatnej. To samo hasło, którego będziesz używać do logowania się do usługi Azure VMware Solution HCX Manager. Możesz wybrać opcję **Generuj nowe hasło** , aby generować nowe hasła VCENTER i NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::


1. Otwórz okno przeglądarki, zaloguj się do usługi Azure VMware Solution HCX Manager na `https://x.x.x.9` porcie 443 z poświadczeniami **użytkownika \@ lokalnego cloudadmin vSphere.**

1. Wybierz pozycję  >  **aktualizacje systemu** administracyjnego, a następnie wybierz **link pobieranie żądania**.

1. Wybierz wybraną opcję, aby pobrać plik komórki jajowe łącznika VMware HCX.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Wdrażanie lokalnych komórek jajowych łącznika VMware HCX

1. W lokalnym programie vCenter wybierz [szablon OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) , aby wdrożyć łącznik programu VMware HCX do lokalnego serwera vCenter. 

   > [!TIP]
   > Wybierzesz plik komórki jajowe, który został pobrany w poprzedniej sekcji.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Zrzut ekranu przedstawiający przeglądanie szablonu OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Wybierz nazwę i lokalizację, a następnie wybierz zasób lub klaster, w którym jest wdrażany łącznik programu VMware HCX. Następnie przejrzyj szczegóły i wymagane zasoby i wybierz pozycję **dalej**.  

1. Zapoznaj się z postanowieniami licencyjnymi. Jeśli zgadzasz się, wybierz wymagany magazyn i sieć, a następnie wybierz przycisk **dalej**.

1. Wybierz pozycję Magazyn i wybierz pozycję **dalej**.

1. Wybierz segment sieci zarządzania programu VMware HCX, który został wcześniej zdefiniowany w sekcji [wymagania wstępne dotyczące adresów IP](#ip-addresses) .  Następnie wybierz pozycję **Dalej**.

1. W obszarze **Dostosowywanie szablonu** wprowadź wszystkie wymagane informacje, a następnie wybierz przycisk **dalej**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Zrzut ekranu przedstawiający pola umożliwiające dostosowanie szablonu." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Sprawdź konfigurację, a następnie wybierz pozycję **Zakończ** , aby wdrożyć komórki jajowe łącznika VMware HCX.
   
   > [!IMPORTANT]
   > Należy ręcznie włączyć urządzenie wirtualne.  Po włączeniu włączania poczekaj 10-15 minut przed przejściem do następnego kroku.

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware:](https://www.youtube.com/embed/UKmSTYrL6AY) film wideo dotyczący wdrażania urządzenia HCX. 


## <a name="activate-vmware-hcx"></a>Aktywuj oprogramowanie VMware HCX

Po wdrożeniu komórek jajowych łącznika VMware HCX w środowisku lokalnym i uruchomieniu urządzenia można rozpocząć aktywację. Najpierw należy uzyskać klucz licencji z portalu rozwiązań VMware platformy Azure.

1. W portalu rozwiązań VMware platformy Azure przejdź do pozycji **Zarządzanie**  >  **łącznością**, wybierz kartę **HCX** , a następnie wybierz pozycję **Dodaj**.

1. Zaloguj się do lokalnego programu VMware HCX Manager przy użyciu poświadczeń **administratora** `https://HCXManagerIP:9443` . 

   > [!TIP]
   > Hasło **administratora** zostało zdefiniowane podczas wdrażania pliku jajowe programu VMware HCX Manager.

   > [!IMPORTANT]
   > Upewnij się, że numer portu został uwzględniony w `9443` adresie IP programu VMware HCX Manager.

1. W obszarze **Licencjonowanie** wprowadź klucz **HCX Advanced Key** i wybierz pozycję **Activate (Aktywuj**).  
   
    > [!NOTE]
    > Program VMware HCX Manager musi mieć skonfigurowany otwarty dostęp do Internetu lub serwer proxy.

1. W polu **Lokalizacja centrum** danych podaj najbliższą lokalizację instalacji programu VMware HCX Manager lokalnie. Następnie wybierz pozycję **Kontynuuj**.

1. W polu **Nazwa systemu** zmodyfikuj nazwę lub Zaakceptuj wartość domyślną i wybierz pozycję **Kontynuuj**.
   
1. Wybierz pozycję **tak, Kontynuuj**.

1. W obszarze **Połącz swój program vCenter** Podaj nazwę FQDN lub adres IP serwera vCenter oraz odpowiednie poświadczenia, a następnie wybierz pozycję **Kontynuuj**.
   
   > [!TIP]
   > Serwer vCenter jest miejscem, w którym został wdrożony łącznik VMware HCX w centrum danych.

1. W obszarze **Konfigurowanie logowania jednokrotnego/PSC** Podaj nazwę FQDN lub adres IP kontrolera usług platformy, a następnie wybierz pozycję **Kontynuuj**.
   
   > [!NOTE]
   > Zwykle jest to taka sama jak nazwa FQDN lub adres IP programu vCenter.

1. Sprawdź, czy wprowadzone informacje są poprawne, a następnie wybierz pozycję **Uruchom ponownie**.
    
   > [!NOTE]
   > Przed wyświetleniem monitu o następny krok nastąpi opóźnienie po ponownym uruchomieniu.

Po ponownym uruchomieniu usług zobaczysz program vCenter wyświetlany jako zielony na wyświetlonym ekranie. Serwery vCenter i SSO muszą mieć odpowiednie parametry konfiguracji, które powinny być takie same jak w poprzednim ekranie.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Zrzut ekranu pulpitu nawigacyjnego z zielonym stanem programu vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: Activate HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) video.

   > [!IMPORTANT]
   > Bez względu na to, czy korzystasz z usługi HCX Advanced, czy HCX Enterprise, może być konieczne zainstalowanie poprawki z [artykułu KB 81558](https://kb.vmware.com/s/article/81558). 

## <a name="configure-the-vmware-hcx-connector"></a>Konfigurowanie łącznika VMware HCX

Teraz możesz dodać parowanie lokacji, utworzyć sieć i profil obliczeniowy oraz włączyć usługi, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii. 

### <a name="add-a-site-pairing"></a>Dodawanie parowania lokacji

Za pomocą łącznika VMware HCX w centrum danych można nawiązać połączenie z programem VMware HCX Cloud Manager i połączyć się z nim. 

1. Zaloguj się do lokalnego programu vCenter i w obszarze **Strona główna** wybierz pozycję **HCX**.

1. W obszarze **infrastruktura** wybierz pozycję **parowanie lokacji**, a następnie wybierz opcję **Połącz z lokacją zdalną** (w środku ekranu). 

1. Wprowadź zanotowany wcześniej adres URL lub adres IP rozwiązania Azure VMware HCX `https://x.x.x.9` , a także nazwę \@ i hasło. Następnie wybierz pozycję **Połącz**.

   > [!NOTE]
   > Aby pomyślnie ustanowić parę lokacji:
   > * Łącznik programu VMware HCX musi być w stanie kierować do swojego adresu IP HCX Cloud Manager przez port 443.
   >
   > * Użyj tego samego hasła, które zostało użyte do zalogowania się do programu vCenter. To hasło zostało zdefiniowane na ekranie wdrożenia początkowego.

   Zostanie wyświetlony ekran z informacją o tym, że Twoje rozwiązanie VMware HCX Cloud Manager w środowisku VMware platformy Azure i Łącznik lokalnego programu VMware HCX są połączone (sparowany).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Zrzut ekranu pokazujący parowanie Menedżera HCX w rozwiązaniu VMware firmy Azure i łączniku VMware HCX.":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: wideo z parowaniem HCX lokacji](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) .

### <a name="create-network-profiles"></a>Tworzenie profilów sieciowych

Łącznik VMware HCX wdraża podzbiór urządzeń wirtualnych (zautomatyzowany), które wymagają wielu segmentów IP. Podczas tworzenia profilów sieci należy używać segmentów IP zidentyfikowanych podczas [przygotowywania i planowania segmentów sieci VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Utworzysz cztery Profile sieciowe:

   - Zarządzanie
   - vMotion
   - Replikacja
   - Łączy

1. W obszarze **infrastruktura** wybierz kolejno pozycje **połączenie**  >  sieć sieci **siatkowa usługi** połączenia  >    >  **Tworzenie profilu sieciowego**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Zrzut ekranu przedstawiający opcje rozpoczęcia tworzenia profilu sieciowego." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Dla każdego profilu sieciowego wybierz sieć i grupę portów, podaj nazwę i Utwórz pulę adresów IP segmentu. Następnie wybierz pozycję **Utwórz**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Zrzut ekranu przedstawiający szczegóły nowego profilu sieciowego.":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz temat [rozwiązanie Azure VMware: wideo HCX profil sieciowy](https://www.youtube.com/embed/O0rU4jtXUxc) .


### <a name="create-a-compute-profile"></a>Utwórz profil obliczeń

1. W obszarze **infrastruktura** wybierz **pozycję**  >  Tworzenie **profilów obliczeń**  >  **Utwórz profil obliczeniowy**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Zrzut ekranu pokazujący wybrane opcje rozpoczęcia tworzenia profilu obliczeniowego." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Wprowadź nazwę profilu i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Zrzut ekranu pokazujący wpis nazwy profilu obliczeniowego i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Wybierz usługi do włączenia, takie jak migracja, rozszerzenie sieci lub odzyskiwanie po awarii, a następnie wybierz pozycję **Kontynuuj**.
  
   > [!NOTE]
   > Ogólnie rzecz biorąc nie wprowadzono żadnych zmian.

1. W obszarze **Wybierz zasoby usługi** wybierz co najmniej jeden zasób usługi (klastrów), aby włączyć wybrane usługi VMware HCX.  

1. Gdy widzisz klastry w lokalnym centrum danych, wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Zrzut ekranu pokazujący wybrane zasoby usługi i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. W obszarze **Wybieranie magazynu** danych wybierz zasób magazynu danych do wdrożenia urządzeń programu VMware HCX Interconnect. Następnie wybierz pozycję **Kontynuuj**.

   W przypadku wybrania wielu zasobów program VMware HCX używa pierwszego wybranego zasobu do momentu wyczerpania jego wydajności.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Zrzut ekranu pokazujący wybrany zasób magazynu danych i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. W obszarze **Wybierz profil sieci zarządzania** wybierz profil sieci zarządzania, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Zrzut ekranu pokazujący wybór profilu sieci zarządzania i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. W obszarze **Wybierz profil sieciowy pasma** wybierz profil sieci pasma utworzony w poprzedniej procedurze. Następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Zrzut ekranu pokazujący wybór profilu sieciowego pasma i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. W obszarze **Wybierz profil sieci vMotion** wybierz profil sieci vMotion, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Zrzut ekranu pokazujący wybór profilu sieci vMotion i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. W obszarze **Wybierz profil sieci replikacji vSphere** wybierz profil sieci replikacji, który został utworzony w poprzednich krokach. Następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Zrzut ekranu pokazujący wybór profilu sieci replikacji i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. W obszarze **Wybierz przełączniki rozproszone dla rozszerzeń sieci** wybierz przełączniki zawierające maszyny wirtualne, które mają zostać zmigrowane do rozwiązania Azure VMware w sieci rozszerzonej o warstwy 2. Następnie wybierz pozycję **Kontynuuj**.

   > [!NOTE]
   > W przypadku niemigrowania maszyn wirtualnych w sieciach rozszerzonych warstwy 2 (L2) można pominąć ten krok.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Zrzut ekranu pokazujący wybór rozproszonych przełączników wirtualnych i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Przejrzyj reguły połączeń i wybierz pozycję **Kontynuuj**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Zrzut ekranu pokazujący reguły połączeń i przycisk Kontynuuj." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Wybierz pozycję **Zakończ** , aby utworzyć profil obliczeń.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Zrzut ekranu pokazujący informacje o profilu obliczeniowym." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz temat [rozwiązanie Azure VMware: wideo z profilem COMPUTE](https://www.youtube.com/embed/e02hsChI3b8) .

### <a name="create-a-service-mesh"></a>Tworzenie siatki usługi

Teraz można skonfigurować siatkę usługi między środowiskiem lokalnym i chmurą prywatną rozwiązania VMware platformy Azure.



> [!NOTE]
> Aby pomyślnie nawiązać siatkę usług z rozwiązaniem VMware platformy Azure:
>
> * Porty UDP 500/4500 są otwarte między adresami profilu sieciowego lokalnego gniazda VMware HCX i adresem profilu sieci "pasmem" w chmurze platformy Azure.
>
> * Upewnij się, że zawarto przegląd [portów wymaganych przez oprogramowanie VMware HCX](https://ports.vmware.com/home/VMware-HCX).

1. W obszarze **infrastruktura** wybierz pozycję  >  **Sieć usługi** Interconnect  >  **Utwórz siatkę usług**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Zrzut ekranu przedstawiający wybór, aby rozpocząć tworzenie siatki usługi." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Przejrzyj wstępnie wypełnione witryny, a następnie wybierz pozycję **Kontynuuj**. 

   > [!NOTE]
   > Jeśli jest to pierwsza konfiguracja sieci usługi, nie trzeba modyfikować tego ekranu.  

1. Wybierz źródłowe i zdalne profile obliczeniowe z listy rozwijanej, a następnie wybierz pozycję **Kontynuuj**.  

   Wybrane opcje określają zasoby, w których maszyny wirtualne mogą korzystać z usług VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Zrzut ekranu pokazujący wybór źródłowego profilu obliczeniowego." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Zrzut ekranu pokazujący wybór profilu zdalnego obliczania." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Przejrzyj usługi, które zostaną włączone, a następnie wybierz pozycję **Kontynuuj**.  

1. W obszarze **Konfiguracja zaawansowana — zastąpienie profilów sieci pasma** wybierz pozycję **Kontynuuj**.  

   Profile sieci pasma łączą się z siecią, za pomocą której można uzyskać dostęp do urządzeń łączących się z lokacją zdalną.  
  
1. W obszarze **Konfiguracja zaawansowana — Przeskaluj w poziomie urządzenie rozszerzenia sieci**, przejrzyj i wybierz pozycję **Kontynuuj**. 

   Możesz mieć maksymalnie osiem sieci VLAN na urządzenie, ale można wdrożyć inne urządzenie, aby dodać kolejne osiem sieci VLAN. Musisz również mieć przestrzeń adresów IP dla dodatkowych urządzeń, a to jeden adres IP na urządzenie.  Aby uzyskać więcej informacji, zobacz [limity konfiguracji programu VMware HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Zrzut ekranu pokazujący, gdzie można zwiększyć liczbę sieci VLAN." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. W **zaawansowanej konfiguracji — Inżynieria ruchu**, przejrzyj i wprowadź wszelkie wymagane zmiany, a następnie wybierz pozycję **Kontynuuj**.

1. Przejrzyj Podgląd topologii i wybierz pozycję **Kontynuuj**.

1. Wprowadź nazwę przyjazną dla użytkownika dla tej sieci usługi i wybierz pozycję **Zakończ** , aby zakończyć.  

1. Wybierz pozycję **Wyświetl zadania** w celu monitorowania wdrożenia. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Zrzut ekranu pokazujący przycisk wyświetlania zadań.":::

   Po pomyślnym zakończeniu wdrożenia usługi siatka usług zobaczysz usługi jako zielone.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Zrzut ekranu przedstawiający zielony wskaźnik usług." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Sprawdź kondycję siatki usługi, sprawdzając stan urządzenia. 

1. Wybierz pozycję urządzenia do **łączenia**  >  .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Zrzut ekranu pokazujący opcje sprawdzania stanu urządzenia." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: wideo z usługi Service siatk](https://www.youtube.com/embed/COY3oIws108) .

### <a name="optional-create-a-network-extension"></a>Obowiązkowe Tworzenie rozszerzenia sieci

Jeśli chcesz rozciągnąć wszystkie sieci z środowiska lokalnego do rozwiązania Azure VMware, wykonaj następujące kroki:

1. W obszarze **usługi** wybierz pozycję **rozszerzenie sieci**  >  **Utwórz rozszerzenie sieciowe**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Zrzut ekranu, na którym są wyświetlane wybory dotyczące rozpoczynania tworzenia rozszerzenia sieci." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Wybierz wszystkie sieci, które chcesz rozłożyć na rozwiązanie VMware platformy Azure, a następnie wybierz przycisk **dalej**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Zrzut ekranu pokazujący wybór sieci.":::

1. Wprowadź adres IP bramy lokalnej dla każdej z rozszerzanych sieci, a następnie wybierz pozycję **Prześlij**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Zrzut ekranu pokazujący wpis adresu IP bramy.":::

   Zakończenie rozszerzenia sieci trwa kilka minut. Gdy to nastąpi, zobaczysz zmianę stanu na **rozszerzenie**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Zrzut ekranu przedstawiający stan ukończonego rozszerzenia." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Aby zapoznać się z kompleksowym omówieniem tej procedury, zobacz [rozwiązanie Azure VMware: film o rozszerzeniu sieci](https://www.youtube.com/embed/gYR0nftKui0) .


## <a name="next-steps"></a>Następne kroki

Jeśli stan tunelu programu HCX Interconnect jest **ustawiony** na wartość zielony, można migrować maszyny wirtualne rozwiązań VMware platformy Azure i chronić je za pomocą programu VMware HCX. Rozwiązanie VMware platformy Azure obsługuje migracje obciążeń (z rozszerzeniem sieci lub bez niego). Nadal można migrować obciążenia w środowisku vSphere, a także lokalne tworzenie sieci i wdrażanie maszyn wirtualnych w tych sieciach.  

Aby uzyskać więcej informacji na temat korzystania z HCX, przejdź do dokumentacji technicznej programu VMware:

* [Dokumentacja programu VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrowanie Virtual Machines przy użyciu oprogramowania VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX wymagane porty](https://ports.vmware.com/home/VMware-HCX)
* [Skonfiguruj serwer proxy HCX przed zatwierdzeniem klucza licencji](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
