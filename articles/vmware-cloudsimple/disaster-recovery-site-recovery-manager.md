---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Skonfiguruj chmurę prywatną jako lokację odzyskiwania po awarii za pomocą programu VMware Site Recovery Manager
description: Opis sposobu konfigurowania prywatnej chmury CloudSimple jako lokacji odzyskiwania po awarii przy użyciu programu VMware Site Recovery Manager.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d71f8aec1f35514ac6c10b17b6f7b69b79b05bd
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897913"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Skonfiguruj chmurę prywatną jako miejsce docelowe odzyskiwania po awarii za pomocą programu VMware Site Recovery Manager

Chmury prywatnej CloudSimple można używać jako lokacji odzyskiwania po awarii (DR) dla lokalnych obciążeń programu VMware.

Rozwiązanie DR bazuje na replikacji vSphere i oprogramowaniu VMware Site Recovery Manager (SRM). Podobne podejście można zastosować, aby włączyć chmurę prywatną jako lokację główną, która jest chroniona przez lokalną lokację odzyskiwania.

Rozwiązanie CloudSimple:

* Eliminuje konieczność skonfigurowania centrum danych przeznaczonego dla programu DR.
* Umożliwia korzystanie z lokalizacji platformy Azure, w których wdrożono CloudSimple na całym świecie.
* Zapewnia możliwość zmniejszenia kosztów wdrożenia i łącznego kosztu posiadania do ustanowienia odzyskiwania po awarii.

Rozwiązanie CloudSimple wymaga wykonania następujących czynności:

* Zainstaluj i skonfiguruj replikację vSphere oraz SRM w chmurze prywatnej oraz zarządzaj nią.
* Udostępnianie własnych licencji dla usługi SRM, gdy Chmura prywatna jest chroniona. Nie są potrzebne żadne dodatkowe licencje SRM dla witryny CloudSimple, gdy jest ona używana jako lokacja odzyskiwania.

Dzięki temu rozwiązaniu masz pełną kontrolę nad replikacją vSphere i SRM. Znane interfejs użytkownika, interfejs API i interfejsy interfejsu wiersza polecenia umożliwiają korzystanie z istniejących skryptów i narzędzi.

![Wdrożenie programu Site Recovery Manager](media/srm-deployment.png)

Możesz użyć dowolnej wersji vRA i SRM, które są zgodne z chmurą prywatną i środowiskami lokalnymi. W przykładach w tym przewodniku użyto vRA 6,5 i SRM 6,5. Te wersje są zgodne z vSphere 6,5, który jest obsługiwany przez CloudSimple.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W poniższych sekcjach opisano, jak wdrożyć rozwiązanie DR przy użyciu SRM w chmurze prywatnej.

1. [Sprawdź, czy wersje produktów VMware są zgodne](#verify-that-vmware-product-versions-are-compatible)
2. [Oszacowanie rozmiaru środowiska odzyskiwania po awarii](#estimate-the-size-of-your-dr-environment)
3. [Tworzenie chmury prywatnej dla środowiska](#create-a-private-cloud-for-your-environment)
4. [Konfigurowanie sieci prywatnej chmury dla rozwiązania SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między siecią lokalną a chmurą prywatną i Otwórz wymagane porty](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Konfigurowanie usług infrastruktury w chmurze prywatnej](#set-up-infrastructure-services-in-your-private-cloud)
7. [Instalowanie urządzenia replikacji vSphere w środowisku lokalnym](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Instalowanie urządzenia replikacji vSphere w środowisku chmury prywatnej](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Instalowanie serwera SRM w środowisku lokalnym](#install-srm-server-in-your-on-premises-environment)
10. [Zainstaluj serwer SRM w chmurze prywatnej](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Sprawdź, czy wersje produktów VMware są zgodne

Konfiguracje w tym przewodniku podlegają następującym wymaganiom dotyczącym zgodności:

* Ta sama wersja programu SRM musi zostać wdrożona w chmurze prywatnej i w środowisku lokalnym.
* Tę samą wersję replikacji vSphere należy wdrożyć w chmurze prywatnej i w środowisku lokalnym.
* Wersje kontrolera usług platformy (PSC) w chmurze prywatnej i środowisko lokalne muszą być zgodne.
* Wersje programu vCenter w chmurze prywatnej i środowisko lokalne muszą być zgodne.
* Wersje SRM i replikacja vSphere muszą być zgodne ze sobą oraz z wersjami systemów PSC i vCenter.

Aby uzyskać linki do odpowiedniej dokumentacji programu VMware i informacji o zgodności, przejdź do dokumentacji programu [vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Aby sprawdzić wersje programu vCenter i PSC w chmurze prywatnej, Otwórz Portal CloudSimple. Przejdź do obszaru **zasoby**, wybierz chmurę prywatną, a następnie kliknij kartę **vSphere Management Network** .

![wersje programu vCenter & PSC w chmurze prywatnej](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Oszacowanie rozmiaru środowiska odzyskiwania po awarii

1. Sprawdź, czy zidentyfikowana konfiguracja lokalna jest objęta obsługiwanymi limitami. W przypadku SRM 6,5 limity są udokumentowane w artykule bazy wiedzy VMware dotyczącej [limitów operacyjnych dla programu Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Upewnij się, że masz wystarczającą przepustowość sieci, aby spełnić wymagania dotyczące rozmiaru obciążenia i punktu odzyskiwania. Artykuł bazy wiedzy VMware dotyczący [obliczania wymagań dotyczących przepustowości dla replikacji vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) zapewnia wskazówki dotyczące limitów przepustowości.
3. Użyj narzędzia CloudSimple size, aby oszacować zasoby, które są konieczne w witrynie DR, aby chronić środowisko lokalne.

### <a name="create-a-private-cloud-for-your-environment"></a>Tworzenie chmury prywatnej dla środowiska

Utwórz chmurę prywatną z poziomu portalu CloudSimple, postępując zgodnie z instrukcjami i zaleceniami dotyczącymi rozmiarów w temacie [Tworzenie chmury prywatnej](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Konfigurowanie sieci prywatnej chmury dla rozwiązania SRM

Aby skonfigurować chmurę prywatną dla rozwiązania SRM, uzyskaj dostęp do portalu CloudSimple.

Utwórz sieć VLAN dla sieci rozwiązania SRM i przypisz ją do podsieci CIDR. Aby uzyskać instrukcje, zobacz [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między siecią lokalną a chmurą prywatną i Otwórz wymagane porty

Aby skonfigurować łączność między lokacjami sieci lokalnej i chmurą prywatną. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia sieci VPN z chmurą prywatną CloudSimple](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Konfigurowanie usług infrastruktury w chmurze prywatnej

Skonfiguruj usługi infrastruktury w chmurze prywatnej, aby ułatwić zarządzanie obciążeniami i narzędziami.

Możesz dodać zewnętrznego dostawcę tożsamości, zgodnie z opisem w temacie [Korzystanie z usługi Azure AD jako dostawcy tożsamości dla programu vCenter w chmurze prywatnej CloudSimple](azure-ad.md) , jeśli chcesz wykonać jedną z następujących czynności:

* Zidentyfikuj użytkowników z lokalnej Active Directory (AD) w chmurze prywatnej.
* Skonfiguruj usługi AD w chmurze prywatnej dla wszystkich użytkowników.
* Użyj usługi Azure AD.

Aby zapewnić wyszukiwanie adresów IP, zarządzanie adresami IP i usługi rozpoznawania nazw dla obciążeń w chmurze prywatnej, należy skonfigurować serwer DHCP i DNS zgodnie z opisem w temacie [Konfigurowanie aplikacji DNS i DHCP oraz obciążeń w prywatnej chmurze CloudSimple](dns-dhcp-setup.md).

Domena *. cloudsimple.io jest używana przez maszyny wirtualne i hosty zarządzania w chmurze prywatnej. Aby rozwiązać żądania do tej domeny, skonfiguruj przekazywanie DNS na serwerze DNS zgodnie z opisem w temacie [Tworzenie usługi przesyłania dalej warunkowego](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Instalowanie urządzenia replikacji vSphere w środowisku lokalnym

Zainstaluj urządzenie replikacji vSphere (vRA) w środowisku lokalnym, postępując zgodnie z dokumentacją programu VMware. Instalacja składa się z następujących ogólnych kroków:

1. Przygotuj środowisko lokalne na potrzeby instalacji vRA.

2. Wdróż vRA w środowisku lokalnym przy użyciu OVF w pliku VR ISO from vmware.com. W przypadku vRA 6,5 [ten blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) dotyczący oprogramowania VMware zawiera odpowiednie informacje.

3. Zarejestruj lokalne vRA za pomocą jednego Sign-On vCenter w lokacji lokalnej. Aby uzyskać szczegółowe instrukcje dotyczące replikacji vSphere 6,5, zobacz dokument VMware [VMware vSphere 6,5 replikacja i konfiguracja](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Instalowanie urządzenia replikacji vSphere w środowisku chmury prywatnej

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Dostępność adresów IP z podsieci w środowisku lokalnym do podsieci zarządzania w chmurze prywatnej
* Dostępność adresów IP z podsieci replikacji w lokalnym środowisku vSphere do podsieci rozwiązania SRM w chmurze prywatnej

Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia sieci VPN z chmurą prywatną CloudSimple](set-up-vpn.md). Kroki są podobne do tych w przypadku instalacji lokalnej.

CloudSimple zaleca używanie nazw FQDN zamiast adresów IP podczas instalacji vRA i SRM. Aby sprawdzić nazwę FQDN programu vCenter i PSC w chmurze prywatnej, Otwórz Portal CloudSimple. Przejdź do obszaru **zasoby**, wybierz chmurę prywatną, a następnie kliknij kartę **vSphere Management Network** .

![Znajdowanie nazwy FQDN serwera vCenter/PSC w chmurze prywatnej](media/srm-resources.png)

CloudSimple wymaga, aby nie instalować vRA i SRM przy użyciu domyślnego użytkownika "cloudowner", ale zamiast tego utworzyć nowego użytkownika. Ma to na celu zapewnienie wysokiego czasu pracy i dostępności dla środowiska programu vCenter w chmurze prywatnej. Jednak domyślny użytkownik cloudowner w prywatnej chmurze vCenter does't ma odpowiednie uprawnienia do tworzenia nowego użytkownika z uprawnieniami administracyjnymi.

Przed zainstalowaniem vRA i SRM należy eskalować uprawnienia programu vCenter do użytkownika cloudowner, a następnie utworzyć użytkownika z uprawnieniami administracyjnymi w domenie vCenter SSO. Aby uzyskać szczegółowe informacje dotyczące domyślnego modelu użytkownika i dostępu chmury prywatnej, zobacz [Omówienie modelu uprawnień chmury prywatnej](learn-private-cloud-permissions.md).

Instalacja składa się z następujących ogólnych kroków:

1. [Eskalacja uprawnień](escalate-private-cloud-privileges.md).
2. Utwórz użytkownika w chmurze prywatnej na potrzeby instalacji replikacji vSphere i SRM. Wyjaśniono poniżej w [interfejsie użytkownika programu vCenter: Utwórz użytkownika w chmurze prywatnej na potrzeby instalacji vRA & SRM](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Przygotuj środowisko chmury prywatnej na potrzeby instalacji vRA.
4. Wdróż vRA w chmurze prywatnej przy użyciu OVF w pliku VR ISO from vmware.com. W przypadku vRA 6,5 [ten blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) ma odpowiednie informacje.
5. Skonfiguruj reguły zapory dla vRA. Wyjaśniono poniżej w [portalu CloudSimple: Konfigurowanie reguł zapory dla vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Zarejestruj chmurę prywatną vRA za pomocą jednego Sign-On vCenter w lokacji w chmurze prywatnej.
7. Skonfiguruj połączenia replikacji vSphere między dwoma urządzeniami. Upewnij się, że wymagane porty są otwierane przez zapory. W [tym artykule z bazy wiedzy VMware](https://kb.vmware.com/s/article/2087769) znajduje się lista numerów portów, które muszą być otwarte dla replikacji vSphere 6,5.

Aby uzyskać szczegółowe instrukcje dotyczące instalacji replikacji vSphere 6,5, zobacz dokument VMware [VMware vSphere 6,5 replikacja i konfiguracja](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>Interfejs użytkownika programu vCenter: Tworzenie użytkownika w chmurze prywatnej na potrzeby instalacji vRA i SRM

Zaloguj się do programu vCenter przy użyciu poświadczeń użytkownika cloudowner po eskalacji uprawnień z portalu CloudSimple.

Utwórz nowego użytkownika, `srm-soln-admin` w programie vCenter i dodaj go do grupy Administratorzy w programie vCenter.
Wyloguj się z programu vCenter jako użytkownik cloudowner i zaloguj się jako użytkownik *SRM-Soln-administrator* .

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>Portal CloudSimple: Konfigurowanie reguł zapory dla vRA

Skonfiguruj reguły zapory zgodnie z opisem w temacie [Konfigurowanie tabel zapory i reguł](firewall.md) , aby otworzyć porty, aby umożliwić komunikację między:

* vRA na hostach sieci SRM i vCenter i ESXi w sieci zarządzania.
* urządzenia vRA w dwóch lokacjach.

W tym [artykule z bazy wiedzy VMware](https://kb.vmware.com/s/article/2087769) znajduje się lista numerów portów, które muszą być otwarte dla replikacji vSphere 6,5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Instalowanie serwera SRM w środowisku lokalnym

Przed rozpoczęciem sprawdź następujące kwestie:

* Urządzenie replikacji vSphere jest instalowane w środowiskach lokalnych i prywatnych w chmurze.
* Urządzenia replikacji vSphere w obu lokacjach są połączone ze sobą.
* Sprawdzono informacje o oprogramowaniu VMware dotyczące wymagań wstępnych i najlepszych rozwiązań. W przypadku SRM 6,5 można zapoznać się z wymaganiami wstępnymi dotyczącymi dokumentu VMware [i najlepszymi rozwiązaniami dla SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Postępuj zgodnie z dokumentacją VMware, aby przeprowadzić instalację serwera SRM w topologii dwóch lokacji modelu wdrożenia z jednym wystąpieniem programu vCenter na kontroler usług platformy, zgodnie z opisem w tym [dokumencie oprogramowania VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Instrukcje dotyczące instalacji programu SRM 6,5 są dostępne w dokumencie programu VMware [instalującym Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Zainstaluj serwer SRM w chmurze prywatnej

Przed rozpoczęciem sprawdź następujące kwestie:

* Urządzenie replikacji vSphere jest instalowane w środowiskach lokalnych i prywatnych w chmurze.
* Urządzenia replikacji vSphere w obu lokacjach są połączone ze sobą.
* Sprawdzono informacje o oprogramowaniu VMware dotyczące wymagań wstępnych i najlepszych rozwiązań. W przypadku SRM 6,5 można zapoznać się z [wymaganiami wstępnymi i najlepszymi rozwiązaniami dotyczącymi instalacji serwera Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

W poniższych krokach opisano instalację SRM chmury prywatnej.

1. [Interfejs użytkownika vCenter: Instalowanie SRM](#vcenter-ui-install-srm)
2. [Portal CloudSimple: Konfigurowanie reguł zapory dla SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [Interfejs użytkownika vCenter: Konfigurowanie SRM](#vcenter-ui-configure-srm)
4. [Portal CloudSimple: anulowanie eskalacji uprawnień](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>Interfejs użytkownika vCenter: Instalowanie SRM

Po zarejestrowaniu się do programu vCenter przy użyciu poświadczeń administratora SRM-Soln — należy skorzystać z dokumentacji programu VMware w celu przeprowadzenia instalacji programu SRM Server w topologii dwóch lokacji modelu wdrożenia z jednym wystąpieniem programu vCenter na kontroler usług platformy, zgodnie z opisem w tym [dokumencie](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Instrukcje dotyczące instalacji programu SRM 6,5 są dostępne w dokumencie programu VMware [instalującym Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portal CloudSimple: Konfigurowanie reguł zapory dla SRM

Skonfiguruj reguły zapory zgodnie z opisem w temacie [Konfigurowanie tabel i reguł zapory](firewall.md) , aby umożliwić komunikację między:

Serwer SRM i vCenter/PSC w chmurze prywatnej.
Serwery SRM w obu lokacjach

W [tym artykule z bazy wiedzy VMware](https://kb.vmware.com/s/article/2087769) znajduje się lista numerów portów, które muszą być otwarte dla replikacji vSphere 6,5.

#### <a name="vcenter-ui-configure-srm"></a>Interfejs użytkownika vCenter: Konfigurowanie SRM

Po zainstalowaniu usługi SRM w chmurze prywatnej wykonaj następujące zadania zgodnie z opisem w sekcji podręcznika instalacji i konfiguracji programu VMware Site Recovery Manager. W przypadku SRM 6,5 instrukcje są dostępne w dokumencie programu VMware [instalującym program Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Połącz wystąpienia serwera programu Site Recovery Manager w witrynach chronionych i odzyskiwania.
2. Nawiąż połączenie z klientem z wystąpieniem serwera zdalnego Site Recovery Manager.
3. Zainstaluj klucz licencji programu Site Recovery Manager.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portal CloudSimple: anulowanie eskalacji uprawnień

Aby cofnąć eskalację uprawnień, zobacz Cofanie [eskalacji uprawnień](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Ciągłe zarządzanie rozwiązaniem SRM

Masz pełną kontrolę nad replikacją vSphere i oprogramowaniem SRM w środowisku chmury prywatnej i oczekujesz, że wymagane jest zarządzanie cyklem życia oprogramowania. Upewnij się, że nowa wersja oprogramowania jest zgodna z chmurą prywatną vCenter i PSC przed aktualizacją lub uaktualnieniem replikacji vSphere lub SRM.

> [!NOTE]
> CloudSimple obecnie eksploruje opcje umożliwiające oferowanie zarządzanej usługi DR. 

## <a name="multiple-replication-configuration"></a>Konfiguracja wielu replikacji

 W tym samym czasie [można jednocześnie używać zarówno technologii replikacji opartej na tablicy, jak i vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) . Jednak muszą być stosowane do oddzielnych zestawów maszyn wirtualnych (dana maszyna wirtualna może być chroniona przez replikację opartą na tablicy lub replikację vSphere, ale nie oba te elementy). Ponadto lokację CloudSimple można skonfigurować jako lokację odzyskiwania dla wielu chronionych witryn. Zobacz [SRM opcje Wielolokacjowe](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) , aby uzyskać informacje na temat konfiguracji z konfiguracją wiele lokacji.

## <a name="references"></a>Dokumentacja

* [Dokumentacja programu VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limity operacyjne dla Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Obliczanie wymagań dotyczących przepustowości dla replikacji vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF opcje podczas wdrażania replikacji vSphere 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [Instalacja i konfiguracja VMware vSphere replikacji 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Wymagania wstępne i najlepsze rozwiązania dotyczące SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager w topologii Two-Site z jednym wystąpieniem vCenter Server na kontroler usług platformy](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Podręcznik instalacji i konfiguracji programu VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware w witrynie SRM z replikacją opartą na platformie Array a replikacją vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware dotyczący opcji SRM wiele lokacji](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Numery portów, które muszą być otwarte dla replikacji vSphere 5.8. x, 6. x i 8](https://kb.vmware.com/s/article/2147112)
