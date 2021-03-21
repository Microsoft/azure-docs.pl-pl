---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — używanie lokacji chmury prywatnej do hostowania infrastruktury pulpitów wirtualnych przy użyciu usługi VMware Horizon
description: Opisuje, w jaki sposób można używać lokacji chmury prywatnej CloudSimple do hostowania infrastruktury pulpitu wirtualnego przy użyciu usługi VMware Horizon
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 513af1ba2e354412278fb88af4ee6527c236fae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895601"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Używanie lokacji chmury prywatnej CloudSimple do hostowania infrastruktury pulpitów wirtualnych przy użyciu usługi VMware Horizon

Do hostowania infrastruktury pulpitów wirtualnych (VDI) przy użyciu programu VMware Horizon 7. x można użyć witryny chmury prywatnej CloudSimple. Na poniższej ilustracji przedstawiono architekturę rozwiązania logicznego infrastruktury VDI.

![Wdrożenie horyzontu](media/horizon-deployment.png)

W tym rozwiązaniu masz pełną kontrolę nad menedżerem widoku horyzontu i woluminem aplikacji. Znane interfejs użytkownika, interfejs API i interfejsy interfejsu wiersza polecenia umożliwiają korzystanie z istniejących skryptów i narzędzi.

Rozwiązanie CloudSimple wymaga wykonania następujących czynności:

* Instalowanie, Konfigurowanie i zarządzanie programem VMware Horizon 7. x w chmurze prywatnej.
* Udostępnianie własnych licencji dla zakresu.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W poniższych sekcjach opisano sposób wdrażania rozwiązania VDI przy użyciu horyzontu w chmurze prywatnej.

1. [Sprawdź, czy wersje produktów VMware są zgodne](#verify-that-vmware-product-versions-are-compatible)
2. [Oszacowanie rozmiaru środowiska pulpitu](#estimate-the-size-of-your-desktop-environment)
3. [Tworzenie chmury prywatnej dla środowiska](#create-a-private-cloud-for-your-environment)
4. [Zainstaluj horyzont oprogramowania VMware w chmurze prywatnej](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Sprawdź, czy wersje produktów VMware są zgodne

* Sprawdź, czy bieżące i planowane wersje horyzontu, woluminów aplikacji, ujednoliconej bramy dostępu i Menedżera środowiska użytkownika są zgodne ze sobą oraz z usługami vCenter i PSC w chmurze prywatnej. Aby uzyskać informacje o zgodności, zobacz [macierz zgodności programu VMware dla zakresu 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Aby sprawdzić bieżące wersje programu vCenter i PSC w chmurze prywatnej, przejdź do obszaru **zasoby** w [portalu CloudSimple](access-cloudsimple-portal.md), wybierz swoją chmurę prywatną, a następnie kliknij kartę **sieć zarządzania vSphere** .

![wersje vCenter i PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Oszacowanie rozmiaru środowiska pulpitu

* Sprawdź, czy zidentyfikowana konfiguracja mieści się w ramach limitów operacyjnych programu VMware.
* Oszacuj zasoby, które są zbędne dla wszystkich pulpitów i składników zarządzania horyzontem.

### <a name="create-a-private-cloud-for-your-environment"></a>Tworzenie chmury prywatnej dla środowiska

1. Utwórz chmurę prywatną z poziomu portalu CloudSimple, postępując zgodnie z instrukcjami w temacie [Konfigurowanie środowiska chmury prywatnej](quickstart-create-private-cloud.md).  CloudSimple tworzy domyślnego użytkownika programu vCenter o nazwie "cloudowner" w każdej nowo utworzonej chmurze prywatnej. Aby uzyskać szczegółowe informacje dotyczące domyślnego modelu użytkownika i dostępu chmury prywatnej, zobacz [Informacje o modelu uprawnień chmury prywatnej](learn-private-cloud-permissions.md).
2. Utwórz sieć VLAN w chmurze prywatnej dla płaszczyzny zarządzania horyzontem i przypisz ją do podsieci CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md). Jest to sieć, w której zostaną zainstalowane wszystkie składniki rozwiązania (ujednolicona Brama dostępu, serwer połączenia, serwer woluminu aplikacji i serwery Menedżera środowiska użytkownika).
3. Zdecyduj, czy chcesz korzystać z zewnętrznego dostawcy tożsamości z programem vCenter w chmurze prywatnej. Jeśli tak, wybierz jedną z następujących opcji:
    * Użyj Active Directory lokalnego jako zewnętrzny dostawca tożsamości. Aby uzyskać instrukcje, zobacz [źródła tożsamości vCenter](set-vcenter-identity.md).
    * Skonfiguruj serwer Active Directory w chmurze prywatnej w płaszczyźnie zarządzania horyzontem VLAN do użycia jako zewnętrzny dostawca tożsamości. Aby uzyskać instrukcje, zobacz [źródła tożsamości vCenter](set-vcenter-identity.md).
    * Skonfiguruj serwer DHCP i DNS w płaszczyźnie zarządzania horyzontem sieci VLAN w chmurze prywatnej. Aby uzyskać instrukcje, zobacz [Konfigurowanie aplikacji DNS i DHCP oraz obciążeń w prywatnej chmurze CloudSimple](dns-dhcp-setup.md).
4. Skonfiguruj przekazywanie DNS na serwerze DNS zainstalowanym w chmurze prywatnej. Aby uzyskać instrukcje, zobacz [Tworzenie usługi przesyłania dalej warunkowej](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Zainstaluj horyzont oprogramowania VMware w chmurze prywatnej

Na poniższym diagramie wdrożenia przedstawiono rozwiązanie horyzontu wdrożone w chmurze prywatnej. Ujednolicona Brama dostępu, usługi AD/DC, widok i serwer woluminu aplikacji są instalowane w sieci VLAN utworzonej przez użytkownika 234. Ujednolicona Brama dostępu ma przypisany publiczny adres IP, który jest dostępny z Internetu. Maszyny wirtualne puli pulpitów są wdrażane w sieci VLAN 235, aby zapewnić dodatkową izolację i bezpieczeństwo.

![Wdrożenie horyzontu w chmurze prywatnej](media/horizon-private-cloud.png)

Poniższe sekcje zawierają instrukcje dotyczące konfigurowania wdrożenia podobnego do przedstawionego na rysunku. Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Chmura prywatna utworzona przy użyciu portalu CloudSimple o wystarczającej pojemności do uruchamiania pul komputerów.
* Wystarczająca przepustowość między środowiskiem lokalnym i środowiskiem chmury prywatnej do obsługi ruchu sieciowego dla komputerów stacjonarnych.
* Tunel sieci VPN typu lokacja-lokacja jest skonfigurowany między lokalnym centrum danych a chmurą prywatną.
* Dostępność adresów IP z podsieci użytkowników końcowych w środowisku lokalnym w podsieciach chmury prywatnej CloudSimple.
* Usługi AD/DHCP/DNS zainstalowane dla chmury prywatnej.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portal CloudSimple: Tworzenie dedykowanej sieci VLAN/podsieci dla pul pulpitów

Utwórz sieć VLAN dla pul stacjonarnych horyzontów i przypisz ją do podsieci CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md). Jest to sieć, w której będą uruchamiane wszystkie maszyny wirtualne.

Przestrzegaj standardowych najlepszych rozwiązań w zakresie zabezpieczeń w celu zabezpieczenia wdrożenia w ramach planu:

* Zezwalaj tylko na ruch RDP na pulpicie/ruch SSH do maszyn wirtualnych.
* Zezwalaj tylko na ruch związany z zarządzaniem między płaszczyzną zarządzania horyzontem VLAN a siecią VLAN puli pulpitów.
* Zezwalaj tylko na ruch związany z zarządzaniem z sieci lokalnej.

Te najlepsze rozwiązania można wymusić, konfigurując [reguły zapory](firewall.md) z poziomu portalu CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portal CloudSimple: Konfigurowanie reguł zapory na potrzeby ochrony płaszczyzny zarządzania

Skonfiguruj następujące reguły w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Konfigurowanie tabel i reguł zapory](firewall.md).

1. Skonfiguruj reguły zapory w zaporze CloudSimple N-S, aby umożliwić komunikację między podsieciami lokalnymi i siecią VLAN zarządzania horyzontem, dzięki czemu dozwolone są tylko porty sieciowe wymienione na [liście portów horyzontu](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) dokumentów programu VMware.

2. Utwórz reguły zapory elektronicznej między siecią VLAN zarządzania horyzontem a siecią VLAN w chmurze prywatnej.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portal CloudSimple: Tworzenie publicznego adresu IP dla usługi Unified Access Gateway

Utwórz publiczny adres IP dla urządzenia bramy dostępu ujednoliconego, aby włączyć połączenia klientów klasycznych z Internetu. Aby uzyskać instrukcje, zobacz [przydzielanie publicznych adresów IP](public-ips.md).

Po zakończeniu instalacji, publiczny adres IP jest przypisywany i wyświetlany na liście na stronie publiczne adresy IPv4.

#### <a name="cloudsimple-portal-escalate-privileges"></a>Portal CloudSimple: eskalacja uprawnień

Domyślny użytkownik "cloudowner" nie ma wystarczających uprawnień w chmurze prywatnej vCenter do zainstalowania horyzontu, więc uprawnienia użytkownika vCenter muszą zostać przeeskalacjne. Aby uzyskać więcej informacji, zobacz temat [Eskalacja uprawnień](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>Interfejs użytkownika vCenter: Tworzenie użytkownika w chmurze prywatnej na potrzeby instalacji z zakresu

1. Zaloguj się do programu vCenter przy użyciu poświadczeń użytkownika "cloudowner".
2. Utwórz nowego użytkownika "Horyzont-Soln-admin" w programie vCenter i Dodaj użytkownika do grupy Administratorzy w programie vCenter.
3. Wyloguj się z programu vCenter jako użytkownika "cloudowner" i zaloguj się jako użytkownik "Horizon-Soln-admin".

#### <a name="vcenter-ui-install-vmware-horizon"></a>Interfejs użytkownika vCenter: Instalowanie zakresu VMware

Jak wspomniano we wcześniejszej części architektury logicznej, rozwiązanie ma następujące składniki:

* Widok horyzontu VMware
* Ujednolicona Brama dostępu VMware
* Menedżer woluminów aplikacji VMware
* Menedżer środowiska użytkownika programu VMware

Zainstaluj składniki w następujący sposób:

1. Zainstaluj i skonfiguruj ujednoliconą bramę dostępu, postępując zgodnie z instrukcjami zawartymi w dokumencie VMware [wdrażanie i Konfigurowanie bramy VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Zainstaluj widok horyzontu w chmurze prywatnej, postępując zgodnie z instrukcjami wyświetlanymi w [przewodniku instalacji](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Zainstaluj program App Volume Manager, postępując zgodnie z instrukcjami w temacie [Instalowanie i Konfigurowanie woluminów aplikacji VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Zainstaluj i skonfiguruj Menedżera środowiska użytkownika, postępując zgodnie z instrukcjami w [temacie Informacje o instalowaniu i konfigurowaniu Menedżera środowiska użytkownika programu VMware](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Prześlij żądanie pomocy technicznej w celu przeładowania wstępnie spakowanych woluminów aplikacji programu VMware Horizon

W ramach procesu instalacji program App Volume Manager używa wstępnie spakowanych woluminów do udostępniania stosów aplikacji i zapisywalnych woluminów. Te woluminy służą jako szablony dla stosów aplikacji i zapisywalnych woluminów.

Przekazywanie woluminów do magazynu danych w chmurze prywatnej wymaga hasła głównego ESXi. Aby uzyskać pomoc, Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz pakiet Instalatora AppVolumes, aby umożliwić personelowi pomocy technicznej CloudSimple przekazywanie szablonów do środowiska chmury prywatnej.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portal CloudSimple: anulowanie eskalacji uprawnień

Teraz można [cofnąć eskalację uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges) użytkownika "cloudowner".

## <a name="ongoing-management-of-your-horizon-solution"></a>Ciągłe zarządzanie rozwiązaniem horyzontu

Użytkownik ma pełną kontrolę nad oprogramowaniem Menedżera woluminów w chmurze prywatnej i oczekuje na wykonanie niezbędnego zarządzania cyklem życia oprogramowania. Upewnij się, że wszystkie nowe wersje oprogramowania są zgodne z prywatnym chmurą vCenter i kontrolerem PSC przed aktualizacją lub uaktualnieniem poziomu lub woluminu aplikacji.
