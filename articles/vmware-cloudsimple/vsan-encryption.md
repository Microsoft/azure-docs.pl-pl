---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Skonfiguruj szyfrowanie sieci vSAN dla chmury prywatnej
description: Opisuje sposób konfigurowania funkcji szyfrowania sieci vSAN oprogramowania, dzięki czemu Chmura prywatna CloudSimple może działać z serwerem zarządzania kluczami uruchomionymi w sieci wirtualnej platformy Azure.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5da05c7f3c6878b0804799360e512676b9002d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899052"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Skonfiguruj szyfrowanie sieci vSAN dla chmury prywatnej CloudSimple

Funkcję szyfrowania oprogramowania sieci vSAN można skonfigurować tak, aby Chmura prywatna CloudSimple mogła działać z serwerem zarządzania kluczami uruchomionymi w sieci wirtualnej platformy Azure.

W przypadku korzystania z szyfrowania sieci vSAN oprogramowanie VMware wymaga użycia zewnętrznego KMIP 1,1 zgodnego z serwerem zarządzania kluczami (KMS) innej firmy. Możesz wykorzystać wszelkie obsługiwane usługi KMS certyfikowane przez program VMware i dostępne dla platformy Azure.

W tym przewodniku opisano, jak używać HyTrust kontroli usługi KMS uruchomionej w sieci wirtualnej platformy Azure. Podobne podejście może być używane w przypadku wszelkich innych certyfikowanych rozwiązań usługi KMS innych firm dla sieci vSAN.

To rozwiązanie usługi KMS wymaga:

* Zainstaluj, skonfiguruj i Zarządzaj certyfikowanym narzędziem usługi KMS firmy VMware w sieci wirtualnej platformy Azure.
* Udostępnianie własnych licencji dla narzędzia usługi KMS.
* Skonfiguruj i Zarządzaj szyfrowaniem sieci vSAN w chmurze prywatnej przy użyciu narzędzia KMS innej firmy działającego w sieci wirtualnej platformy Azure.

## <a name="kms-deployment-scenario"></a>Scenariusz wdrażania usługi KMS

Klaster serwerów usługi KMS działa w sieci wirtualnej platformy Azure i jest dostępny za pośrednictwem adresu IP w chmurze prywatnej programu vCenter przez skonfigurowane połączenie usługi Azure ExpressRoute.

![.. klaster/media/KMS w usłudze Azure Virtual Network](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Jak wdrożyć rozwiązanie

Proces wdrażania obejmuje następujące kroki:

1. [Sprawdź, czy wymagania wstępne są spełnione](#verify-prerequisites-are-met)
2. [Portal CloudSimple: uzyskiwanie informacji o komunikacji równorzędnej ExpressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure Portal: łączenie sieci wirtualnej z chmurą prywatną](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure Portal: Wdrażanie klastra kontrolki HyTrust w sieci wirtualnej](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Skonfiguruj serwer KMIP](#hytrust-webui-configure-the-kmip-server)
6. [Interfejs użytkownika vCenter: Skonfiguruj szyfrowanie sieci vSAN, aby używać klastra usługi KMS w sieci wirtualnej platformy Azure](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Sprawdź, czy wymagania wstępne są spełnione

Przed wdrożeniem sprawdź następujące kwestie:

* Wybrany dostawca usługi KMS, narzędzie i wersja znajdują się na liście zgodności sieci vSAN.
* Wybrany dostawca obsługuje wersję narzędzia do uruchomienia na platformie Azure.
* Wersja platformy Azure dla narzędzia KMS jest zgodna z KMIP 1,1.
* Azure Resource Manager i Sieć wirtualna zostały już utworzone.
* Chmura prywatna CloudSimple została już utworzona.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>Portal CloudSimple: uzyskiwanie informacji o komunikacji równorzędnej ExpressRoute

Aby kontynuować instalację, musisz dysponować kluczem autoryzacji i identyfikatorem URI obwodu równorzędnego dla ExpressRoute Plus dostęp do subskrypcji platformy Azure. Te informacje są dostępne na stronie połączenie Virtual Network w portalu CloudSimple. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia sieci wirtualnej z chmurą prywatną](virtual-network-connection.md). Jeśli masz problemy z uzyskaniem informacji, Otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure Portal: łączenie sieci wirtualnej z chmurą prywatną

1. Utwórz bramę sieci wirtualnej dla sieci wirtualnej, postępując zgodnie z instrukcjami podanymi w temacie [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Połącz sieć wirtualną z obwodem usługi CloudSimple ExpressRoute, postępując zgodnie z instrukcjami podanymi w temacie [łączenie sieci wirtualnej z obwodem usługi ExpressRoute przy użyciu portalu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Użyj informacji o obwodzie usługi CloudSimple ExpressRoute otrzymanych w powitalnej wiadomości e-mail z usługi CloudSimple, aby połączyć sieć wirtualną z obwodem CloudSimple ExpressRoute na platformie Azure.
4. Wprowadź klucz autoryzacji i identyfikator URI obwodu równorzędnego, nadaj połączeniu nazwę, a następnie kliknij przycisk **OK**.

![Podaj identyfikator URI obwodu równorzędnego CS podczas tworzenia sieci wirtualnej](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure Portal: Wdróż klaster kontrolki HyTrust w Azure Resource Manager w sieci wirtualnej

Aby wdrożyć klaster kontroli HyTrust Azure Resource Manager w sieci wirtualnej, należy wykonać następujące zadania. Szczegółowe informacje znajdują się w [dokumentacji HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) .

1. Utwórz sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń-hytrust) platformy Azure z określonymi regułami ruchu przychodzącego, postępując zgodnie z instrukcjami w dokumentacji HyTrust.
2. Generuj parę kluczy SSH na platformie Azure.
3. Wdróż początkowy węzeł kontrolki z obrazu w portalu Azure Marketplace.  Użyj klucza publicznego pary kluczy, która została wygenerowana, i wybierz **sieciowej grupy zabezpieczeń-hytrust** jako grupę zabezpieczeń sieci dla węzła kontrolki.
4. Przekonwertuj prywatny adres IP kontrolki na statyczny adres IP.
5. SSH do sterowania maszyną wirtualną przy użyciu jego publicznego adresu IP i klucza prywatnego wcześniej wymienionej pary kluczy.
6. Po wyświetleniu monitu w powłoce SSH zaznacz, `No` Aby ustawić węzeł jako początkowy węzeł kontrolki.
7. Dodaj dodatkowe węzły kontroli, powtarzając kroki 3-5 tej procedury i wybierając opcję `Yes` gdy zostanie wyświetlony monit o dodanie do istniejącego klastra.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Skonfiguruj serwer KMIP

Przejdź do https://*Public-IP*, gdzie *Public-IP* jest publicznym adresem IP maszyny wirtualnej węzła kontrolki. Wykonaj te kroki z [dokumentacji HyTrust](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Konfigurowanie serwera KMIP](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Tworzenie pakietu certyfikatów na potrzeby szyfrowania oprogramowania VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>Interfejs użytkownika vCenter: Skonfiguruj szyfrowanie sieci vSAN, aby używać klastra usługi KMS w sieci wirtualnej platformy Azure

Postępuj zgodnie z instrukcjami HyTrust, aby [utworzyć klaster usługi KMS w programie vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Dodawanie szczegółów klastra usługi KMS w programie vCenter](media/vsan-config01.png)

W programie vCenter przejdź do pozycji **klaster > Skonfiguruj** i wybierz opcję **Ogólne** dla sieci vSAN. Włącz szyfrowanie i wybierz klaster usługi KMS, który został wcześniej dodany do programu vCenter.

![Włączanie szyfrowania sieci vSAN i Konfigurowanie klastra usługi KMS w programie vCenter](media/vsan-config02.png)

## <a name="references"></a>Odwołania

### <a name="azure"></a>Azure

[Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Nawiązywanie połączenia sieci wirtualnej z obwodem usługi ExpressRoute za pomocą witryny Portal](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust i Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Konfigurowanie serwera KMPI](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Tworzenie pakietu certyfikatów na potrzeby szyfrowania oprogramowania VMware](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Tworzenie klastra usługi KMS w vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
