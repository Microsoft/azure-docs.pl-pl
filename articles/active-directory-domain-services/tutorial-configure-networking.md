---
title: Samouczek — Konfigurowanie sieci wirtualnych dla Azure AD Domain Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować podsieć lub komunikację równorzędną sieci wirtualnej platformy Azure dla domeny zarządzanej Azure Active Directory Domain Services przy użyciu Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: e0d2b235f671ca9b30bf61aef254cb850b25373e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86024778"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Samouczek: Konfigurowanie sieci wirtualnej dla Azure Active Directory Domain Services domeny zarządzanej

W celu zapewnienia łączności z użytkownikami i aplikacjami aplikacja zarządzana Azure Active Directory Domain Services (Azure AD DS) jest wdrażana w podsieci sieci wirtualnej platformy Azure. Ta podsieć sieci wirtualnej powinna być używana tylko dla zasobów domeny zarządzanej udostępnianych przez platformę Azure.

Podczas tworzenia własnych maszyn wirtualnych i aplikacji nie należy ich wdrażać w tej samej podsieci sieci wirtualnej. Zamiast tego należy tworzyć i wdrażać aplikacje w oddzielnej podsieci sieci wirtualnej lub w oddzielnej sieci wirtualnej, która jest połączona z siecią wirtualną platformy Azure AD DS.

W tym samouczku pokazano, jak utworzyć i skonfigurować dedykowaną podsieć sieci wirtualnej lub sposób komunikacji równorzędnej innej sieci z siecią wirtualną domeny zarządzanej platformy Azure AD DS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Informacje o opcjach łączności sieci wirtualnej dla zasobów przyłączonych do domeny na platformie Azure AD DS
> * Utwórz zakres adresów IP i dodatkową podsieć w sieci wirtualnej usługi Azure AD DS
> * Konfigurowanie komunikacji równorzędnej sieci wirtualnej z siecią oddzielną od platformy Azure AD DS

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Aby skonfigurować AD DS platformy Azure, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usługi Azure AD DS, musisz mieć uprawnienia *współautora* w ramach subskrypcji platformy Azure.
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności pierwszy samouczek [tworzy i konfiguruje domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz i skonfigurujesz domenę zarządzaną przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Opcje łączności z obciążeniem aplikacji

W poprzednim samouczku utworzono domenę zarządzaną, która używała niektórych domyślnych opcji konfiguracji dla sieci wirtualnej. Te domyślne opcje zostały utworzone przy użyciu sieci wirtualnej platformy Azure i podsieci sieci wirtualnej. Kontrolery domeny AD DS platformy Azure, które zapewniają zarządzane usługi domenowe, są połączone z tą podsiecią sieci wirtualnej.

Podczas tworzenia i uruchamiania maszyn wirtualnych, które muszą korzystać z domeny zarządzanej, należy podać łączność sieciową. Tę łączność sieciową można zapewnić w jeden z następujących sposobów:

* Utwórz dodatkową podsieć sieci wirtualnej w sieci wirtualnej domeny zarządzanej. Ta dodatkowa podsieć służy do tworzenia i łączenia maszyn wirtualnych.
    * Ponieważ maszyny wirtualne są częścią tej samej sieci wirtualnej, mogą automatycznie przeprowadzać rozpoznawanie nazw i komunikować się z kontrolerami domeny AD DS platformy Azure.
* Skonfiguruj komunikację równorzędną sieci wirtualnej platformy Azure z sieci wirtualnej domeny zarządzanej do co najmniej jednej oddzielnej sieci wirtualnej. Te oddzielne sieci wirtualne umożliwiają tworzenie i łączenie maszyn wirtualnych.
    * Podczas konfigurowania komunikacji równorzędnej sieci wirtualnej należy również skonfigurować ustawienia DNS, aby używać rozpoznawania nazw z powrotem do kontrolerów domeny AD DS platformy Azure.

Zazwyczaj można używać tylko jednej z tych opcji łączności sieciowej. Wybór jest często w dół, aby zarządzać rozdzielonymi zasobami platformy Azure.

* Jeśli chcesz zarządzać usługą Azure AD DS i połączonymi maszynami wirtualnymi jako jedną grupą zasobów, możesz utworzyć dodatkową podsieć sieci wirtualnej dla maszyn wirtualnych.
* Jeśli chcesz rozdzielić zarządzanie AD DS platformy Azure, a następnie wszystkie połączone maszyny wirtualne, możesz użyć komunikacji równorzędnej sieci wirtualnej.
    * Możesz również użyć komunikacji równorzędnej sieci wirtualnej, aby zapewnić łączność z istniejącymi maszynami wirtualnymi w środowisku platformy Azure, które są podłączone do istniejącej sieci wirtualnej.

W tym samouczku wystarczy skonfigurować tylko jedną z tych opcji łączności sieci wirtualnej.

Aby uzyskać więcej informacji na temat planowania i konfigurowania sieci wirtualnej, zobacz [zagadnienia dotyczące sieci Azure Active Directory Domain Services][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Utwórz podsieć sieci wirtualnej

Domyślnie Sieć wirtualna platformy Azure utworzona przy użyciu domeny zarządzanej zawiera jedną podsieć sieci wirtualnej. Ta podsieć sieci wirtualnej powinna być używana tylko przez platformę Azure w celu zapewnienia usług domenowych zarządzanej. Aby tworzyć własne maszyny wirtualne i korzystać z nich w tej sieci wirtualnej platformy Azure, Utwórz dodatkową podsieć.

Aby utworzyć podsieć sieci wirtualnej dla obciążeń maszyn wirtualnych i aplikacji, wykonaj następujące czynności:

1. W Azure Portal wybierz grupę zasobów domeny zarządzanej, *na przykład grupa zasobów.* Z listy zasobów wybierz domyślną sieć wirtualną, na przykład *aadds-VNET*.
1. W menu po lewej stronie okna Sieć wirtualna wybierz pozycję **przestrzeń adresowa**. Sieć wirtualna jest tworzona z użyciem pojedynczej przestrzeni adresowej *10.0.2.0/24*, która jest używana przez domyślną podsieć.

    Dodaj dodatkowy zakres adresów IP do sieci wirtualnej. Rozmiar tego zakresu adresów oraz rzeczywisty zakres adresów IP, który ma być używany, zależy od innych już wdrożonych zasobów sieciowych. Zakres adresów IP nie powinien nakładać się na wszystkie istniejące zakresy adresów w środowisku platformy Azure lub lokalnym. Upewnij się, że rozmiar zakresu adresów IP jest wystarczająco duży, aby liczba maszyn wirtualnych, które mają zostać wdrożone w podsieci.

    W poniższym przykładzie dodano dodatkowy zakres adresów IP *10.0.3.0/24* . Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

    ![Dodaj dodatkowy zakres adresów IP sieci wirtualnej w Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Następnie w menu po lewej stronie okna Sieć wirtualna wybierz pozycję **podsieci**, a następnie wybierz pozycję **+ podsieć** , aby dodać podsieć.
1. Wprowadź nazwę podsieci, na przykład *obciążenia*. W razie potrzeby zaktualizuj **zakres adresów** , jeśli chcesz użyć podzbioru zakresu adresów IP skonfigurowanego dla sieci wirtualnej w poprzednich krokach. Na razie pozostaw wartości domyślne dla opcji takich jak sieciowa Grupa zabezpieczeń, tabela tras, punkty końcowe usługi.

    W poniższym przykładzie utworzono podsieć o nazwie *obciążenia* , która używa zakresu adresów IP *10.0.3.0/24* :

    ![Dodaj dodatkową podsieć sieci wirtualnej w Azure Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK**. Utworzenie podsieci sieci wirtualnej może chwilę potrwać.

Podczas tworzenia maszyny wirtualnej, która musi korzystać z domeny zarządzanej, należy upewnić się, że wybrano tę podsieć sieci wirtualnej. Nie twórz maszyn wirtualnych w domyślnej *podsieci aadds*. W przypadku wybrania innej sieci wirtualnej nie ma łączności sieciowej i rozpoznawania nazw DNS w celu uzyskania dostępu do domeny zarządzanej, chyba że zostanie skonfigurowana Komunikacja równorzędna sieci wirtualnej.

## <a name="configure-virtual-network-peering"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej

Być może masz istniejącą sieć wirtualną platformy Azure dla maszyn wirtualnych lub chcesz zachować oddzielną sieć wirtualną w domenie zarządzanej. Aby można było używać domeny zarządzanej, maszyny wirtualne w innych sieciach wirtualnych muszą mieć możliwość komunikowania się z kontrolerami domeny AD DS platformy Azure. Tę łączność można zapewnić przy użyciu komunikacji równorzędnej sieci wirtualnej platformy Azure.

Za pomocą komunikacji równorzędnej sieci wirtualnych platformy Azure dwie sieci wirtualne są połączone ze sobą bez konieczności używania urządzenia wirtualnej sieci prywatnej (VPN). Komunikacja równorzędna sieci umożliwia szybkie łączenie sieci wirtualnych i Definiowanie przepływów ruchu w środowisku platformy Azure.

Aby uzyskać więcej informacji na temat komunikacji równorzędnej, zobacz [Omówienie komunikacji równorzędnej usługi Azure Virtual Network][peering-overview].

Aby połączyć się z siecią wirtualną sieci wirtualnej z siecią wirtualną domeny zarządzanej, wykonaj następujące kroki:

1. Wybierz domyślną sieć wirtualną utworzoną dla domeny zarządzanej o nazwie *aadds-VNET*.
1. W menu po lewej stronie okna Sieć wirtualna wybierz pozycję **Komunikacja równorzędna**.
1. Aby utworzyć komunikację równorzędną, wybierz pozycję **+ Dodaj**. W poniższym przykładzie domyślną *aadds-VNET* jest Komunikacja równorzędna z siecią wirtualną o nazwie *myVnet*. Skonfiguruj następujące ustawienia przy użyciu własnych wartości:

    * **Nazwa komunikacji równorzędnej z aadds-VNET do zdalnej sieci wirtualnej**: opisowego identyfikatora dwóch sieci, takich jak *aadds-VNET-to-myvnet*
    * **Typ wdrożenia sieci wirtualnej**: *Menedżer zasobów*
    * **Subskrypcja**: subskrypcja sieci wirtualnej, z którą chcesz nawiązać komunikację równorzędną, na przykład *Azure*
    * **Sieć wirtualna**: Sieć wirtualna, z którą chcesz nawiązać komunikację równorzędną, taką jak *myVnet*
    * **Nazwa komunikacji równorzędnej z myVnet do aadds-VNET**: opisowy identyfikator dwóch sieci, na przykład *myVnet-to-aadds-VNET*

    ![Konfigurowanie komunikacji równorzędnej sieci wirtualnej w Azure Portal](./media/tutorial-configure-networking/create-peering.png)

    Pozostaw inne wartości domyślne dla dostępu do sieci wirtualnej lub ruchu przesyłanego dalej, chyba że masz określone wymagania dla danego środowiska, a następnie wybierz przycisk **OK**.

1. Utworzenie komunikacji równorzędnej w sieci wirtualnej platformy Azure AD DS i wybranej sieci wirtualnej trwa kilka chwil. Gdy jest gotowy, raporty o **stanie komunikacji równorzędnej** są *połączone*, jak pokazano w następującym przykładzie:

    ![Sieci równorzędne zostały pomyślnie połączone w Azure Portal](./media/tutorial-configure-networking/connected-peering.png)

Zanim maszyny wirtualne w sieci wirtualnej w komunikacji równorzędnej będą mogły korzystać z domeny zarządzanej, należy skonfigurować serwery DNS tak, aby zezwalały na poprawne rozpoznawanie nazw.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurowanie serwerów DNS w równorzędnej sieci wirtualnej

Aby maszyny wirtualne i aplikacje w równorzędnej sieci wirtualnej mogli pomyślnie komunikować się z domeną zarządzaną, należy zaktualizować ustawienia DNS. Adresy IP kontrolerów domeny AD DS platformy Azure muszą być skonfigurowane jako serwery DNS w równorzędnej sieci wirtualnej. Istnieją dwa sposoby skonfigurowania kontrolerów domeny jako serwery DNS dla równorzędnej sieci wirtualnej:

* Skonfiguruj serwery DNS w sieci wirtualnej platformy Azure, aby używały kontrolerów domeny AD DS platformy Azure.
* Skonfiguruj istniejący serwer DNS używany przez równorzędną sieć wirtualną do korzystania z warunkowego przekazywania DNS, aby kierować zapytania do domeny zarządzanej. Kroki te różnią się w zależności od istniejącego serwera DNS w użyciu.

W tym samouczku skonfigurujemy serwery DNS sieci wirtualnej platformy Azure, aby kierować wszystkie zapytania do kontrolerów domeny AD DS platformy Azure.

1. W Azure Portal wybierz grupę zasobów równorzędnej sieci wirtualnej, na przykład grupa *zasobów*. Z listy zasobów wybierz sieć wirtualną równorzędną, taką jak *myVnet*.
1. W menu po lewej stronie okna Sieć wirtualna wybierz pozycję **serwery DNS**.
1. Domyślnie Sieć wirtualna używa wbudowanych serwerów DNS udostępnianych przez platformę Azure. Wybierz użycie **niestandardowych** serwerów DNS. Wprowadź adresy IP dla kontrolerów domeny AD DS platformy Azure, które są zwykle *10.0.2.4* i *10.0.2.5*. Potwierdź te adresy IP w oknie **Przegląd** domeny zarządzanej w portalu.

    ![Skonfiguruj serwery DNS sieci wirtualnej, aby używały kontrolerów domeny AD DS platformy Azure](./media/tutorial-configure-networking/custom-dns.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. Aktualizacja serwerów DNS dla sieci wirtualnej trwa kilka chwil.
1. Aby zastosować zaktualizowane ustawienia DNS do maszyn wirtualnych, należy ponownie uruchomić maszyny wirtualne połączone z równorzędną siecią wirtualną.

Podczas tworzenia maszyny wirtualnej, która musi korzystać z domeny zarządzanej, należy upewnić się, że wybrano tę sieć wirtualną równorzędną. W przypadku wybrania innej sieci wirtualnej nie ma łączności sieciowej i rozpoznawania nazw DNS w celu uzyskania dostępu do domeny zarządzanej.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Informacje o opcjach łączności sieci wirtualnej dla zasobów przyłączonych do domeny na platformie Azure AD DS
> * Utwórz zakres adresów IP i dodatkową podsieć w sieci wirtualnej usługi Azure AD DS
> * Konfigurowanie komunikacji równorzędnej sieci wirtualnej z siecią oddzielną od platformy Azure AD DS

Aby wyświetlić tę domenę zarządzaną w działaniu, należy utworzyć maszynę wirtualną i przyłączyć ją do domeny.

> [!div class="nextstepaction"]
> [Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
