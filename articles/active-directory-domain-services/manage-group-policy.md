---
title: Tworzenie zasad grupy i zarządzanie nimi w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak edytować wbudowane obiekty zasad grupy (GPO) i utworzyć własne zasady niestandardowe w Azure Active Directory Domain Services domenie zarządzanej.
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: deefcb66e05199896e8997d707a06e45f397adec
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963741"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrowanie zasady grupy w Azure Active Directory Domain Servicesej domenie zarządzanej

Ustawienia obiektów użytkowników i komputerów w Azure Active Directory Domain Services (AD DS platformy Azure) są często zarządzane przy użyciu obiektów zasady grupy. Usługa Azure AD DS obejmuje wbudowane obiekty zasad grupy dla kontenerów *użytkowników AADDC* i *AADDC* . Można dostosować te wbudowane obiekty zasad grupy w celu skonfigurowania zasady grupy, zgodnie z potrzebami środowiska. Członkowie grupy *administratorzy kontrolera domeny usługi Azure AD* zasady grupy mają uprawnienia do administrowania w domenie AD DS platformy Azure, a także mogą tworzyć niestandardowe obiekty zasad grupy i jednostki organizacyjne (OU). Więcej informacji na temat tego, co zasady grupy jest i jak to działa, można znaleźć w temacie [zasady grupy Overview][group-policy-overview].

W środowisku hybrydowym zasady grupy skonfigurowane w środowisku lokalnym AD DS nie są synchronizowane z usługą Azure AD DS. Aby zdefiniować ustawienia konfiguracji dla użytkowników lub komputerów w usłudze Azure AD DS, Edytuj jeden z domyślnych obiektów zasad grupy lub Utwórz niestandardowy obiekt zasad grupy.

W tym artykule opisano sposób instalowania narzędzi do zarządzania zasady grupy, a następnie edytowania wbudowanych obiektów zasad grupy i tworzenia niestandardowych obiektów zasad grupy.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest dołączona do domeny zarządzanej AD DS platformy Azure.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

> [!NOTE]
> Możesz użyć Szablony administracyjne zasady grupy, kopiując nowe szablony do stacji roboczej zarządzania. Skopiuj pliki *ADMX* do `%SYSTEMROOT%\PolicyDefinitions` i skopiuj pliki *. adml* specyficzne dla ustawień regionalnych do programu `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` , gdzie `Language-CountryRegion` pasuje do języka i regionu plików *. adml* .
>
> Na przykład skopiuj angielską Stany Zjednoczone wersję plików *. adml* do `\en-us` folderu.
>
> Alternatywnie można centralnie przechowywać szablon administracyjny zasady grupy na kontrolerach domeny będących częścią domeny zarządzanej. Aby uzyskać więcej informacji, zobacz [jak utworzyć magazyn centralny dla zasady grupy Szablony administracyjne w systemie Windows i zarządzać](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)nim.

## <a name="install-group-policy-management-tools"></a>Instalowanie zasady grupy narzędzia do zarządzania

Aby utworzyć i skonfigurować zasady grupy obiektów (GPO), należy zainstalować narzędzia do zarządzania zasady grupy. Te narzędzia można zainstalować jako funkcję systemu Windows Server. Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz Install [Narzędzia administracji zdalnej serwera (RSAT)][install-rsat].

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące sposobu nawiązywania połączenia przy użyciu Azure Portal, zobacz [nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server][connect-windows-server-vm].
1. **Menedżer serwera** powinna być otwarta domyślnie po zalogowaniu się do maszyny wirtualnej. Jeśli nie, w menu **Start** wybierz **Menedżer serwera**.
1. W okienku *pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**.
1. Na stronie **zanim rozpoczniesz** *Kreatora dodawania ról i funkcji*wybierz pozycję **dalej**.
1. W polu *Typ instalacji*pozostaw zaznaczoną opcję **Instalacja oparta na rolach lub oparta na funkcjach** , a następnie wybierz pozycję **dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, takiej jak *MyVM.aaddscontoso.com*, a następnie wybierz przycisk **dalej**.
1. Na stronie **role serwera** kliknij przycisk **dalej**.
1. Na stronie **funkcje** wybierz funkcję **zarządzania zasady grupy** .

    ![Zainstaluj "zasady grupy Management" na stronie funkcji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Na stronie **potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie zasady grupy narzędzi do zarządzania może potrwać minutę lub dwa.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij** , aby zakończyć działanie kreatora **dodawania ról i funkcji** .

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Otwórz Konsola zarządzania zasadami grupy i edytuj obiekt

Istnieją domyślne obiekty zasad grupy dla użytkowników i komputerów w domenie zarządzanej. Dzięki funkcji zarządzania zasady grupy zainstalowanej w poprzedniej sekcji można wyświetlić i edytować istniejący obiekt zasad grupy. W następnej sekcji utworzysz niestandardowy obiekt zasad grupy.

> [!NOTE]
> Aby administrować zasadami grupy w domenie zarządzanej, użytkownik musi być zalogowany na koncie użytkownika, który jest członkiem grupy *Administratorzy domeny usługi AAD* .

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, w tym **zasady grupy zarządzania** zainstalowaną w poprzedniej sekcji.
1. Aby otworzyć Konsola zarządzania zasadami grupy (GPMC), wybierz pozycję **zarządzanie zasady grupy**.

    ![Konsola zarządzania zasadami grupy otwiera się gotowy do edytowania obiektów zasad grupy](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Istnieją dwa wbudowane obiekty zasady grupy (GPO) w domenie zarządzanej — jeden dla kontenera *komputery AADDC* i jeden dla kontenera *Użytkownicy AADDC* . Te obiekty zasad grupy można dostosować w taki sposób, aby w ramach domeny zarządzanej zostały skonfigurowane zasady grup.

1. W konsoli **zarządzania zasady grupy** rozwiń węzeł **Las: aaddscontoso.com** . Następnie rozwiń węzły **domeny** .

    Istnieją dwa wbudowane kontenery dla *komputerów AADDC* i *użytkowników AADDC*. Każdy z tych kontenerów ma zastosowany domyślny obiekt zasad grupy.

    ![Wbudowane obiekty zasad grupy stosowane do domyślnych kontenerów "AADDC komputery" i "AADDC użytkowników"](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Te wbudowane obiekty zasad grupy można dostosować w celu skonfigurowania określonych zasad grup w domenie zarządzanej. Kliknij prawym przyciskiem myszy jeden z obiektów zasad grupy, na przykład *AADDC komputery obiektów zasad grupy*, a następnie wybierz polecenie **Edytuj...**.

    ![Wybierz opcję "Edytuj" jeden z wbudowanych obiektów zasad grupy](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Zostanie otwarte narzędzie Edytor zarządzania zasadami grupy umożliwiające dostosowanie obiektu zasad grupy, takiego jak *Zasady konta*:

    ![Zrzut ekranu przedstawiający Edytor zarządzania zasadami grupy.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Po zakończeniu wybierz pozycję **plik > Zapisz** , aby zapisać zasady. Komputery domyślnie odświeżają zasady grupy co 90 minut i stosują wprowadzone zmiany.

## <a name="create-a-custom-group-policy-object"></a>Tworzenie niestandardowego obiektu zasady grupy

Aby zgrupować podobne ustawienia zasad, często tworzysz dodatkowe obiekty zasad grupy zamiast stosowania wszystkich wymaganych ustawień w jednym domyślnym obiekcie zasad grupy. Za pomocą usługi Azure AD DS można utworzyć lub zaimportować własne niestandardowe obiekty zasad grupy i połączyć je z niestandardową jednostką organizacyjną. Jeśli musisz najpierw utworzyć niestandardową jednostkę organizacyjną, zobacz [Tworzenie niestandardowej jednostki organizacyjnej w domenie zarządzanej](create-ou.md).

1. W konsoli **zarządzania zasady grupy** wybierz swoją niestandardową jednostkę organizacyjną (OU), taką jak *MyCustomOU*. Wybierz jednostkę organizacyjną, a następnie wybierz pozycję **Utwórz obiekt zasad grupy w tej domenie i umieść tu link...**:

    ![Tworzenie niestandardowego obiektu zasad grupy w konsoli zarządzania zasady grupy](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Określ nazwę dla nowego obiektu zasad grupy, na przykład *niestandardowy obiekt zasad grupy*, a następnie wybierz przycisk **OK**. Opcjonalnie można oprzeć ten niestandardowy obiekt zasad grupy na istniejącym obiekcie zasad grupy i zestawie opcji zasad.

    ![Określ nazwę nowego niestandardowego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Niestandardowy obiekt zasad grupy jest tworzony i połączony z niestandardową jednostką organizacyjną. Aby teraz skonfigurować ustawienia zasad, kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie wybierz polecenie **Edytuj...**:

    ![Wybierz opcję "Edytuj" niestandardowy obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Zostanie otwarty **Edytor zarządzania zasadami grupy** , aby umożliwić dostosowanie obiektu zasad grupy:

    ![Dostosuj obiekt zasad grupy, aby skonfigurować ustawienia zgodnie z potrzebami](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Po zakończeniu wybierz pozycję **plik > Zapisz** , aby zapisać zasady. Komputery domyślnie odświeżają zasady grupy co 90 minut i stosują wprowadzone zmiany.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dostępnych ustawień zasady grupy, które można skonfigurować za pomocą Konsola zarządzania zasadami grupy, zobacz [Work with zasady grupy Items][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
