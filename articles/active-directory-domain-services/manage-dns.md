---
title: Zarządzanie usługami domenowymi usługi USŁUGI AZURE AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować narzędzia serwera DNS do zarządzania systemem DNS i tworzyć usługi przesyłania dalej warunkowe dla domeny zarządzanej Usług domenowych Usługi active directory platformy Azure.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603542"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>Administrowanie systemem DNS i tworzenie usług przesyłania dalej warunkowego w domenie zarządzanej usług domenowych usługi Azure AD

W usługach domenowych Usługi active directory platformy Azure (Azure AD DS) kluczowym składnikiem jest DNS (Domain Name Resolution). Usługi Azure AD DS zawiera serwer DNS, który zapewnia rozpoznawanie nazw dla domeny zarządzanej. Ten serwer DNS zawiera wbudowane rekordy DNS i aktualizacje dla kluczowych składników, które umożliwiają uruchomienie usługi.

Podczas uruchamiania własnych aplikacji i usług może być konieczne utworzenie rekordów DNS dla maszyn, które nie są przyłączone do domeny, skonfigurowanie wirtualnych adresów IP dla modułów równoważenia obciążenia lub skonfigurowanie zewnętrznych usług przesyłania dalej DNS. Użytkownicy należący do grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD* otrzymują uprawnienia administracyjne DNS w domenie zarządzanej usług Azure AD DS i mogą tworzyć i edytować niestandardowe rekordy DNS.

W środowisku hybrydowym strefy i rekordy DNS skonfigurowane w innych obszarach nazw DNS, takich jak lokalne środowisko usług AD DS, nie są synchronizowane z usługą Azure AD DS. Aby rozwiązać nazwane zasoby w innych obszarach nazw DNS, należy utworzyć i używać usług przesyłania dalej warunkowego, które wskazują istniejące serwery DNS w twoim środowisku.

W tym artykule pokazano, jak zainstalować narzędzia serwera DNS, a następnie użyć konsoli DNS do zarządzania rekordami i tworzenia warunkowych usług przesyłania dalej w usługach Azure AD DS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Łączność z sieci wirtualnej usług Azure AD DS do miejsca, w którym znajdują się inne obszary nazw DNS.
    * Ta łączność może być dostarczona z [połączeniem Azure ExpressRoute][expressroute] lub [Azure VPN Gateway.][vpn-gateway]
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną systemu Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

## <a name="install-dns-server-tools"></a>Instalowanie narzędzi serwera DNS

Aby tworzyć i modyfikować rekordy DNS w usługach Azure AD DS, należy zainstalować narzędzia serwera DNS. Narzędzia te można zainstalować jako funkcję w systemie Windows Server. Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz [instalowanie narzędzi administracji zdalnej (RSAT)][install-rsat].

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące łączenia się przy użyciu portalu Azure portal, zobacz [Łączenie się z maszyną wirtualną systemu Windows Server][connect-windows-server-vm].
1. Jeśli **Menedżer serwera** nie otwiera się domyślnie po zalogowaniu się do maszyny Wirtualnej, wybierz menu **Start,** a następnie wybierz pozycję **Menedżer serwera**.
1. W okienku *Pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję Dodaj role **i funkcje**.
1. Na stronie **Przed rozpoczęciem** *Kreatora dodawania ról i funkcji*wybierz pozycję **Dalej**.
1. W przypadku *typu instalacji*pozostaw zaznaczoną opcję **instalacji opartą na rolach lub funkcjach** i wybierz pozycję **Dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, taką jak *myvm.aaddscontoso.com*, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Role serwera** kliknij przycisk **Dalej**.
1. Na stronie **Funkcje** rozwiń węzeł **Narzędzia administracji zdalnej serwera,** a następnie rozwiń węzeł **Narzędzia administrowania rolami.** Wybierz funkcję **Narzędzia serwera DNS** z listy narzędzi do administrowania rolami.

    ![Aby zainstalować narzędzia serwera DNS z listy dostępnych narzędzi administracyjnych ról](./media/manage-dns/install-dns-tools.png)

1. Na stronie **Potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie narzędzi zarządzania zasadami grupy może potrwać minutę lub dwie.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij,** aby zakończyć pracę **kreatora Dodawanie ról i funkcji.**

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otwórz konsolę zarządzania DNS, aby administrować systemem DNS

Po zainstalowaniu narzędzi serwera DNS można administrować rekordami DNS w domenie zarządzanej usług Azure AD DS.

> [!NOTE]
> Aby administrować usługą DNS w domenie zarządzanej usług Azure AD DS, musisz zalogować się do konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD.*

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, w tym **system DNS** zainstalowany w poprzedniej sekcji. Wybierz **pozycję DNS,** aby uruchomić konsolę Zarządzania DNS.
1. W oknie dialogowym **Połącz z serwerem DNS** wybierz pozycję **Następujący komputer**, a następnie wprowadź nazwę domeny DNS domeny zarządzanej, taką jak *aaddscontoso.com:*

    ![Łączenie się z domeną zarządzałą usługą Azure AD DS w konsoli DNS](./media/manage-dns/connect-dns-server.png)

1. Konsola DNS łączy się z określoną domeną zarządzana usługą Azure AD DS. Rozwiń **strefy wyszukiwania do przodu** lub strefy **odnośnego wstecznego,** aby utworzyć wymagane wpisy DNS lub w razie potrzeby edytować istniejące rekordy.

    ![Konsola DNS — administrowanie domeną](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Podczas zarządzania rekordami przy użyciu narzędzi serwera DNS upewnij się, że nie usuwasz ani nie modyfikujesz wbudowanych rekordów DNS używanych przez usługi Azure AD DS. Wbudowane rekordy DNS obejmują rekordy DNS domeny, rekordy serwera nazw i inne rekordy używane dla lokalizacji kontrolera domeny. Jeśli zmodyfikujesz te rekordy, usługi domeny zostaną zakłócone w sieci wirtualnej.

## <a name="create-conditional-forwarders"></a>Tworzenie warunkowych spedytów

Strefa DNS usług Azure AD DS powinna zawierać tylko strefę i rekordy dla samej domeny zarządzanej. Nie należy tworzyć dodatkowych stref w usługach Azure AD DS, aby rozwiązać nazwane zasoby w innych obszarach nazw DNS. Zamiast tego należy użyć usług przesyłania dalej warunkowej w domenie zarządzanej usługi Azure AD DS, aby poinformować serwer DNS, gdzie należy się udać, aby rozpoznać adresy dla tych zasobów.

Usługa przesyłania dalej warunkowej to opcja konfiguracji na serwerze DNS, która umożliwia zdefiniowanie domeny DNS, takiej jak *contoso.com*, do przekazywania zapytań. Zamiast lokalnego serwera DNS próbującego rozpoznać kwerendy dotyczące rekordów w tej domenie, kwerendy DNS są przekazywane do skonfigurowanego systemu DNS dla tej domeny. Ta konfiguracja zapewnia, że są zwracane poprawne rekordy DNS, ponieważ nie tworzysz lokalnej strefy DNS ze zduplikowanymi rekordami w domenie zarządzanej usługi Azure AD DS w celu odzwierciedlenia tych zasobów.

Aby utworzyć usługę przesyłania dalej warunkowej w domenie zarządzanej usług Azure AD DS, wykonaj następujące kroki:

1. Wybierz strefę DNS usług Azure AD DS, taką jak *aaddscontoso.com*.vb
1. Wybierz **warunkowe przesyłanie dalej**, a następnie wybierz prawym przyciskiem wyboru i wybierz pozycję **Nowy warunkowy przesyłania dalej...**
1. Wprowadź inną **domenę DNS**, taką jak *contoso.com,* a następnie wprowadź adresy IP serwerów DNS dla tego obszaru nazw, jak pokazano w poniższym przykładzie:

    ![Dodawanie i konfigurowanie usługi przesyłania dalej warunkowej serwera DNS](./media/manage-dns/create-conditional-forwarder.png)

1. Zaznacz pole wyboru **Przechowywanie tej usługi przesyłania dalej warunkowej w usłudze Active Directory i replikuj ją w następujący sposób,** a następnie wybierz opcję dla wszystkich *serwerów DNS w tej domenie,* jak pokazano w poniższym przykładzie:

    ![Konsola DNS — administrowanie domeną](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Jeśli usługa przesyłania dalej warunkowej jest przechowywana w *lesie,* a nie w *domenie,* usługa przesyłania dalej warunkowej kończy się niepowodzeniem.

1. Aby utworzyć warunkową przesyłanie dalej, wybierz przycisk **OK**.

Rozpoznawanie nazw zasobów w innych obszarach nazw z maszyn wirtualnych połączonych z domeną zarządzana usługą Azure AD DS powinno teraz zostać poprawnie rozpoznane. Zapytania dotyczące domeny DNS skonfigurowanej w uiwarowanym warunkowym uchyłka są przekazywane do odpowiednich serwerów DNS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania systemem DNS, zobacz [artykuł o narzędziach DNS w serwisie Technet](https://technet.microsoft.com/library/cc753579.aspx).

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
