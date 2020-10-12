---
title: Zarządzanie usługą DNS dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak zainstalować narzędzia serwera DNS w celu zarządzania systemem DNS i tworzenia usług przesyłania warunkowego dla domeny zarządzanej Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: af321f4e9ef9e340026852a759bb70b9f03c77f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722946"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrowanie systemem DNS i tworzenie usług przesyłania dalej warunkowego w Azure Active Directory Domain Services domenie zarządzanej

W Azure Active Directory Domain Services (Azure AD DS), składnik klucza to DNS (rozpoznawanie nazw domen). Usługa Azure AD DS obejmuje serwer DNS, który zapewnia rozpoznawanie nazw dla domeny zarządzanej. Ten serwer DNS zawiera wbudowane rekordy DNS i aktualizacje dla kluczowych składników, które umożliwiają uruchomienie usługi.

Podczas uruchamiania własnych aplikacji i usług może być konieczne utworzenie rekordów DNS dla komputerów, które nie są przyłączone do domeny, skonfigurować wirtualne adresy IP dla modułów równoważenia obciążenia lub skonfigurować zewnętrzne usługi przesyłania dalej DNS. Użytkownicy, którzy należą do grupy *administratorów DC usługi AAD* , mają uprawnienia do administrowania systemem DNS w domenie zarządzanej usługi Azure AD DS i mogą tworzyć i edytować niestandardowe rekordy DNS.

W środowisku hybrydowym strefy i rekordy DNS skonfigurowane w innych przestrzeniach nazw DNS, takich jak lokalne środowisko AD DS, nie są synchronizowane z domeną zarządzaną. Aby rozwiązać nazwane zasoby w innych przestrzeniach nazw DNS, należy utworzyć i użyć usług przesyłania dalej warunkowego, które wskazują istniejące serwery DNS w danym środowisku.

W tym artykule opisano sposób instalowania narzędzi serwera DNS, a następnie używania konsoli DNS do zarządzania rekordami i tworzenia usług przesyłania dalej warunkowego w usłudze Azure AD DS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Łączność z sieci wirtualnej platformy Azure AD DS do lokalizacji, w której są hostowane inne przestrzenie nazw DNS.
    * Tę łączność można uzyskać za pomocą [usługi Azure ExpressRoute][expressroute] lub połączenia [VPN Gateway platformy Azure][vpn-gateway] .
* Maszyna wirtualna zarządzania systemem Windows Server, która jest przyłączona do domeny zarządzanej.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="install-dns-server-tools"></a>Instalowanie narzędzi serwera DNS

Aby utworzyć i zmodyfikować rekordy DNS w domenie zarządzanej, należy zainstalować narzędzia serwera DNS. Te narzędzia można zainstalować jako funkcję systemu Windows Server. Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz Install [Narzędzia administracji zdalnej serwera (RSAT)][install-rsat].

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące sposobu nawiązywania połączenia przy użyciu Azure Portal, zobacz [nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server][connect-windows-server-vm].
1. Jeśli **Menedżer serwera** nie zostanie otwarta domyślnie po zalogowaniu się do maszyny wirtualnej, wybierz menu **Start** , a następnie wybierz pozycję **Menedżer serwera**.
1. W okienku *pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**.
1. Na stronie **zanim rozpoczniesz** *Kreatora dodawania ról i funkcji*wybierz pozycję **dalej**.
1. W polu *Typ instalacji*pozostaw zaznaczoną opcję **Instalacja oparta na rolach lub oparta na funkcjach** , a następnie wybierz pozycję **dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, takiej jak *MyVM.aaddscontoso.com*, a następnie wybierz przycisk **dalej**.
1. Na stronie **role serwera** kliknij przycisk **dalej**.
1. Na stronie **funkcje** rozwiń węzeł **Narzędzia administracji zdalnej serwera** , a następnie rozwiń węzeł Narzędzia do **administrowania rolami** . Wybierz funkcję **Narzędzia serwera DNS** z listy narzędzi do administrowania rolami.

    ![Wybierz, aby zainstalować narzędzia serwera DNS z listy dostępnych narzędzi do administrowania rolami](./media/manage-dns/install-dns-tools.png)

1. Na stronie **potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie narzędzi serwera DNS może potrwać minutę lub dwa.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij** , aby zakończyć działanie kreatora **dodawania ról i funkcji** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otwórz konsolę zarządzania DNS w celu administrowania usługą DNS

Za pomocą zainstalowanych narzędzi serwera DNS można administrować rekordami DNS w domenie zarządzanej.

> [!NOTE]
> Aby administrować systemem DNS w domenie zarządzanej, użytkownik musi być zalogowany na koncie użytkownika, który jest członkiem grupy *administratorzy kontrolera domeny usługi AAD* .

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, w tym **usługi DNS** zainstalowane w poprzedniej sekcji. Wybierz pozycję **DNS** , aby uruchomić konsolę zarządzania DNS.
1. W oknie dialogowym **łączenie z serwerem DNS** wybierz **następujący komputer**, a następnie wprowadź nazwę domeny DNS domeny zarządzanej, taką jak *aaddscontoso.com*:

    ![Nawiązywanie połączenia z domeną zarządzaną w konsoli DNS](./media/manage-dns/connect-dns-server.png)

1. Konsola DNS nawiązuje połączenie z określoną domeną zarządzaną. Rozwiń **strefy wyszukiwania do przodu** lub **strefy wyszukiwania wstecznego** , aby utworzyć wymagane wpisy DNS, lub edytuj istniejące rekordy stosownie do potrzeb.

    ![Konsola DNS-Administruj domeną](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Podczas zarządzania rekordami przy użyciu narzędzi serwera DNS upewnij się, że nie usunięto ani nie modyfikujesz wbudowanych rekordów DNS, które są używane przez usługę Azure AD DS. Wbudowane rekordy DNS obejmują rekordy DNS domeny, rekordy serwera nazw i inne rekordy używane do lokalizacji kontrolera domeny. W przypadku zmodyfikowania tych rekordów usługi domenowe są zakłócone w sieci wirtualnej.

## <a name="create-conditional-forwarders"></a>Tworzenie usług przesyłania dalej warunkowego

Strefa DNS usługi Azure AD DS powinna zawierać tylko strefy i rekordy dla samej domeny zarządzanej. Nie należy tworzyć dodatkowych stref w domenie zarządzanej do rozwiązywania nazwanych zasobów w innych przestrzeniach nazw DNS. Zamiast tego należy użyć usług przesyłania dalej warunkowego w domenie zarządzanej w celu poinformowania serwera DNS, gdzie ma się znaleźć, aby rozwiązać adresy tych zasobów.

Usługa przesyłania dalej warunkowego jest opcją konfiguracji na serwerze DNS, która umożliwia zdefiniowanie domeny DNS, takiej jak *contoso.com*, do przesyłania zapytań do programu. Zamiast lokalnego serwera DNS próbującego rozpoznać zapytania dotyczące rekordów w tej domenie zapytania DNS są przekazywane do skonfigurowanego systemu DNS dla tej domeny. Ta konfiguracja gwarantuje, że zwracane są poprawne rekordy DNS, ponieważ nie utworzysz lokalnej strefy DNS zawierającej zduplikowane rekordy w domenie zarządzanej w celu odzwierciedlenia tych zasobów.

Aby utworzyć usługę przesyłania dalej warunkowego w domenie zarządzanej, wykonaj następujące czynności:

1. Wybierz strefę DNS, taką jak *aaddscontoso.com*.
1. Wybierz pozycję **usługi przesyłania dalej warunkowe**, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję **Nowa usługa przesyłania dalej warunkowego...**
1. Wprowadź inną **domenę DNS**, na przykład *contoso.com*, a następnie wprowadź adresy IP serwerów DNS dla tej przestrzeni nazw, jak pokazano w następującym przykładzie:

    ![Dodawanie i Konfigurowanie usługi przesyłania dalej warunkowej dla serwera DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Zaznacz pole wyboru **przechowuj tę usługę przesyłania dalej warunkowego w Active Directory i Replikuj ją w następujący sposób**, a następnie wybierz opcję dla *wszystkich serwerów DNS w tej domenie*, jak pokazano w następującym przykładzie:

    ![Konsola DNS — wybierz wszystkie serwery DNS w tej domenie](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Jeśli usługa przesyłania dalej warunkowego jest przechowywana w *lesie* , a nie w *domenie*, warunkowego przesyłania dalej kończy się niepowodzeniem.

1. Aby utworzyć usługę przesyłania dalej warunkowego, wybierz **przycisk OK**.

Rozpoznawanie nazw zasobów w innych przestrzeniach nazw z maszyn wirtualnych połączonych z domeną zarządzaną powinno teraz rozwiązać problem. Zapytania dotyczące domeny DNS skonfigurowanej w warunkowej usługi przesyłania dalej są przekazywane do odpowiednich serwerów DNS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania usługą DNS, zobacz [artykuł dotyczący narzędzi DNS w witrynie TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh