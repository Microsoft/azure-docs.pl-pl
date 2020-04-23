---
title: Samouczek — tworzenie zaufania lasu w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć jednokierunkowy las wychodzący do lokalnej domeny usług AD DS w witrynie Azure portal dla usług domenowych usługi Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 9a76f72d3f01ab9253c452e49dde171280fe481d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654414"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Samouczek: tworzenie wychodzącego zaufania lasu do domeny lokalnej w usługach domenowych Usługi domenowe Active Directory platformy Azure (wersja zapoznawcza)

W środowiskach, w których nie można synchronizować skrótów haseł lub masz użytkowników, którzy logują się wyłącznie przy użyciu kart inteligentnych, aby nie znali swojego hasła, można użyć lasu zasobów w usługach domenowych Active Directory platformy Azure (AD DS). Las zasobów używa jednokierunkowego zaufania wychodzącego z usług Azure AD DS do jednego lub kilku lokalnych środowisk usług AD DS. Ta relacja zaufania umożliwia użytkownikom, aplikacjom i komputerom uwierzytelnianie się w domenie lokalnej z domeny zarządzanej usługi Azure AD DS. Lasy zasobów usług Azure AD DS są obecnie w wersji zapoznawczej.

![Diagram zaufania lasu od usług Azure AD DS do lokalnych usług AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie systemu DNS w lokalnym środowisku usług AD DS w celu obsługi łączności usług Ad DS
> * Tworzenie jednokierunkowego przychodzącego zaufania lasu w lokalnym środowisku usług AD DS
> * Tworzenie jednokierunkowego wychodzącego zaufania lasu w usługach Azure AD DS
> * Testowanie i sprawdzanie poprawności relacji zaufania dla uwierzytelniania i dostępu do zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi usługi Active Directory platformy Azure utworzona przy użyciu lasu zasobów i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Upewnij się, że utworzysz domenę zarządzaną usługą Azure AD DS przy użyciu lasu *zasobów.* Opcja domyślna tworzy las *użytkownika.* Tylko lasy zasobów mogą tworzyć relacje zaufania do środowisk usług AD DS.Only resource forests can create trusts to on-prem AD DS environments. Należy również użyć minimalnej jednostki SKU *przedsiębiorstwa* dla domeny zarządzanej. W razie potrzeby [zmień jednostkę SKU dla domeny zarządzanej usług Azure AD DS][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku można utworzyć i skonfigurować wychodzące zaufanie lasu z usługi Azure AD DS przy użyciu witryny Azure portal. Aby rozpocząć, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

Sieć wirtualna, która obsługuje las zasobów usługi Azure AD DS, wymaga łączności sieciowej z lokalną usługą Active Directory. Aplikacje i usługi wymagają również łączności sieciowej z siecią wirtualną hostującą las zasobów usługi Azure AD DS. Łączność sieciowa z lasem zasobów usług Azure AD DS musi być zawsze wł., a stabilna, w przeciwnym razie użytkownicy mogą nie uwierzytelniać się ani uzyskiwać dostępu do zasobów.

Przed skonfigurowaniem zaufania lasu w usługach Azure AD DS upewnij się, że sieć między platformą Azure a środowiskiem lokalnym spełnia następujące wymagania:

* Użyj prywatnych adresów IP. Nie polegaj na dhcp z dynamicznym przypisywaniem adresów IP.
* Unikaj nakładających się przestrzeni adresów IP, aby umożliwić komunikację równorzędną i routing sieci wirtualnej między platformą Azure a lokalną.
* Sieć wirtualna platformy Azure potrzebuje podsieci bramy do skonfigurowania połączenia [sieci VPN lokacja-lokacja platformy Azure (S2S)][vpn-gateway] lub połączenia [Usługi ExpressRoute][expressroute]
* Utwórz podsieci z wystarczającą ilością adresów IP do obsługi scenariusza.
* Upewnij się, że usługi Azure AD DS ma własną podsieć, nie udostępniaj tej podsieci sieci wirtualnej z maszynami wirtualnymi i usługami aplikacji.
* Sieci wirtualne typu peered NIE są przechodnie.
    * Komunikacji równorzędnej sieci wirtualnej platformy Azure muszą być tworzone między wszystkimi sieciami wirtualnymi, które mają być używane zaufania lasu zasobów usług Azure AD DS do lokalnego środowiska usług AD DS.
* Zapewnienie ciągłej łączności sieciowej z lokalnym lasem usługi Active Directory. Nie używaj połączeń na żądanie.
* Upewnij się, że istnieje ciągłe rozpoznawanie nazw (DNS) między nazwą lasu zasobów usługi Azure AD DS a lokalną nazwą lasu usługi Active Directory.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurowanie systemu DNS w domenie lokalnej

Aby poprawnie rozpoznać domenę zarządzaną usługą Azure AD DS ze środowiska lokalnego, może być konieczne dodanie usług przesyłania dalej do istniejących serwerów DNS. Jeśli środowisko lokalne nie jest skonfigurowane do komunikowania się z domeną zarządzaną usługą Azure AD DS, wykonaj następujące kroki z lokalnej stacji usług AD DS:

1. Wybierz **start | Narzędzia administracyjne | System DNS**
1. Wybierz prawym przycisku wyboru serwer DNS, taki jak *myAD01*, wybierz **pozycję Właściwości**
1. Wybierz **pozycję Przesyłanie dalej**, a następnie **edytuj,** aby dodać dodatkowe usługi przesyłania dalej.
1. Dodaj adresy IP domeny zarządzanej usługi Azure AD DS, takie jak *10.0.2.4* i *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Tworzenie zaufania lasu przychodzącego w domenie lokalnej

Lokalna domena usług AD DS wymaga przychodzącego zaufania lasu dla domeny zarządzanej usług Azure AD DS. To zaufanie musi być ręcznie tworzone w lokalnej domenie usług AD DS, nie można go utworzyć z witryny Azure portal.

Aby skonfigurować zaufanie przychodzące w lokalnej domenie usług AD DS, wykonaj następujące kroki ze stacji roboczej zarządzania lokalną domeną usług AD DS:

1. Wybierz **start | Narzędzia administracyjne | Domeny i relacje zaufania usługi Active Directory**
1. Wybierz domenę po prawej stronie, na przykład *onprem.contoso.com*, wybierz **pozycję Właściwości**
1. Następnie wybierz kartę **Ufanie,** a następnie **pozycję Nowe zaufanie**
1. Wprowadź nazwę w nazwie domeny usług Azure AD DS, na przykład *aaddscontoso.com,* a następnie wybierz pozycję **Dalej**
1. Wybierz opcję, aby utworzyć **zaufanie lasu**, a następnie utworzyć **jeden sposób: zaufanie przychodzące.**
1. Wybierz, aby utworzyć zaufanie tylko dla **tej domeny**. W następnym kroku można utworzyć zaufanie w witrynie Azure portal dla domeny zarządzanej usług Azure AD DS.
1. Wybierz opcję **używania uwierzytelniania w całym lesie,** a następnie wprowadź i potwierdź hasło zaufania. To samo hasło jest również wprowadzane w witrynie Azure portal w następnej sekcji.
1. Krok po kilku następnych oknach z opcjami domyślnymi, a następnie wybierz opcję **Nie, nie potwierdzaj wychodzącego zaufania**.
1. Wybierz pozycję **Zakończ**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Tworzenie wychodzącego zaufania lasu w usługach Azure AD DS

Po skonfigurowaniu lokalnej domeny usług AD DS w celu rozpoznawania domeny zarządzanej usług Azure AD DS i utworzenia zaufania lasu przychodzącego utworzono teraz wychodzące zaufanie lasu. To wychodzące zaufanie lasu kończy relację zaufania między lokalną domeną usług AD DS a domeną zarządzaną usługą Azure AD DS.

Aby utworzyć zaufanie wychodzące dla domeny zarządzanej usługi Azure AD DS w witrynie Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure portal wyszukaj i wybierz **pozycję Usługi domenowe usługi Azure AD**, a następnie wybierz domenę zarządzana, taką jak *aaddscontoso.com*
1. Z menu po lewej stronie domeny zarządzanej usługi Azure AD DS wybierz pozycję **Ufa,** a następnie wybierz opcję **+ Dodaj** zaufanie.

   > [!NOTE]
   > Jeśli nie widzisz opcji menu **Zaufania,** zaznacz w obszarze **Właściwości** *typu Las*. Tylko lasy *zasobów* mogą tworzyć relacje zaufania. Jeśli typem lasu jest *Użytkownik,* nie można utworzyć relacji zaufania. Obecnie nie ma możliwości zmiany typu lasu domeny zarządzanej usługą Azure AD DS. Należy usunąć i ponownie utworzyć domenę zarządzaną jako las zasobów.

1. Wprowadź nazwę wyświetlaną identyfikującą zaufanie, a następnie lokalną zaufaną nazwę DNS lasu, taką jak *onprem.contoso.com*
1. Podaj to samo hasło zaufania, które zostało użyte podczas konfigurowania zaufania lasu przychodzącego dla lokalnej domeny usług AD DS w poprzedniej sekcji.
1. Podaj co najmniej dwa serwery DNS dla lokalnej domeny usług AD DS, takich jak *10.1.1.4* i *10.1.1.5*
1. Gdy jest to gotowe, **zapisz** wychodzące zaufanie lasu

    ![Tworzenie wychodzącego zaufania lasu w witrynie Azure portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Sprawdzanie poprawności uwierzytelniania zasobów

Następujące typowe scenariusze umożliwiają sprawdzenie poprawności zaufania lasu poprawnie uwierzytelnia użytkowników i dostęp do zasobów:

* [Lokalne uwierzytelnianie użytkowników z lasu zasobów usług Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Uzyskiwanie dostępu do zasobów w lesie zasobów usług Azure AD DS przy użyciu użytkownika lokalnego](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Włączanie udostępniania plików i drukarek](#enable-file-and-printer-sharing)
    * [Tworzenie grupy zabezpieczeń i dodawanie członków](#create-a-security-group-and-add-members)
    * [Tworzenie udziału plików w celu uzyskania dostępu do różnych lasów](#create-a-file-share-for-cross-forest-access)
    * [Sprawdzanie poprawności uwierzytelniania między lasami w zasobie](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Lokalne uwierzytelnianie użytkowników z lasu zasobów usług Azure AD DS

Maszyna wirtualna systemu Windows Server powinna być przyłączona do domeny zasobów usług Azure AD DS. Ta maszyna wirtualna służy do testowania lokalnego użytkownika można uwierzytelnić na maszynie wirtualnej.

1. Połącz się z maszyną wirtualną systemu Windows Server przyłączony do lasu zasobów usług Azure AD DS przy użyciu [usługi Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) i poświadczeń administratora usług Azure AD DS.
1. Otwórz wiersz polecenia i `whoami` użyj polecenia, aby wyświetlić nazwę wyróżniającą aktualnie uwierzytelnionego użytkownika:

    ```console
    whoami /fqdn
    ```

1. `runas` Polecenie służy do uwierzytelniania jako użytkownik z domeny lokalnej. W poniższym poleceniu zastąp `userUpn@trusteddomain.com` nazwę UPN użytkownika z zaufanej domeny lokalnej. Polecenie monituje o hasło użytkownika:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Jeśli uwierzytelnianie zakończy się pomyślnie, zostanie otwarty nowy wiersz polecenia. Tytuł nowego wiersza polecenia `running as userUpn@trusteddomain.com`zawiera .
1. Użyj `whoami /fqdn` w nowym wierszu polecenia, aby wyświetlić nazwę wyróżniającą uwierzytelnionego użytkownika z lokalnej usługi Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Uzyskiwanie dostępu do zasobów w lesie zasobów usług Azure AD DS przy użyciu użytkownika lokalnego

Za pomocą maszyny Wirtualnej systemu Windows Server przyłączony do lasu zasobów usługi Azure AD DS, można przetestować scenariusz, w którym użytkownicy mogą uzyskać dostęp do zasobów hostowanych w lesie zasobów, gdy uwierzytelniają się z komputerów w domenie lokalnej z użytkownikami z domeny lokalnej. Poniższe przykłady pokazują, jak tworzyć i testować różne typowe scenariusze.

#### <a name="enable-file-and-printer-sharing"></a>Włączanie udostępniania plików i drukarek

1. Połącz się z maszyną wirtualną systemu Windows Server przyłączony do lasu zasobów usług Azure AD DS przy użyciu [usługi Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) i poświadczeń administratora usług Azure AD DS.

1. Otwórz **pozycję Ustawienia systemu Windows**, a następnie wyszukaj i wybierz pozycję Centrum sieci i **udostępniania**.
1. Wybierz opcję **Zmień ustawienia udostępniania zaawansowanego.**
1. W obszarze **Profil domeny**wybierz pozycję Włącz udostępnianie plików **i drukarek,** a następnie **zapisz zmiany**.
1. Zamknij **centrum sieci i udostępniania**.

#### <a name="create-a-security-group-and-add-members"></a>Tworzenie grupy zabezpieczeń i dodawanie członków

1. Otwórz narzędzie **Użytkownicy i komputery usługi Active Directory**.
1. Wybierz prawym przyciskiem wyboru nazwę domeny, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Jednostka organizacyjna**.
1. W polu nazwa wpisz *LocalObjects*, a następnie wybierz przycisk **OK**.
1. W okienku nawigacji zaznacz i kliknij prawym przyciskiem myszy pozycję **LocalObjects.** Wybierz **pozycję Nowy,** a następnie **grupuj**.
1. Wpisz *FileServerAccess* w polu **Nazwa grupy.** W przypadku **opcji Zakres grupy**wybierz pozycję **Domena lokalna**, a następnie wybierz przycisk **OK**.
1. W okienku zawartości kliknij dwukrotnie pozycję **FileServerAccess**. Wybierz **pozycję Członkowie**, wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Lokalizacje**.
1. Wybierz lokalną usługę Active Directory z widoku **Lokalizacja,** a następnie wybierz przycisk **OK**.
1. Wpisz *pozycję Użytkownicy domeny* w polu Wprowadź nazwy obiektów do **zaznaczenia.** Wybierz **pozycję Sprawdź nazwy**, podaj poświadczenia lokalnej usługi Active Directory, a następnie wybierz przycisk **OK**.

    > [!NOTE]
    > Należy podać poświadczenia, ponieważ relacja zaufania jest tylko jeden sposób. Oznacza to, że użytkownicy usług Azure AD DS nie mogą uzyskiwać dostępu do zasobów ani wyszukiwać użytkowników lub grup w domenie zaufanej (lokalnej).

1. Grupa **Użytkownicy domeny** z lokalnej usługi Active Directory powinna należeć do grupy **FileServerAccess.** Wybierz **przycisk OK,** aby zapisać grupę i zamknąć okno.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Tworzenie udziału plików w celu uzyskania dostępu do różnych lasów

1. Na maszynie Wirtualnej systemu Windows Server przyłączony do lasu zasobów usług Azure AD DS, należy utworzyć folder i podać nazwę, takich jak *CrossForestShare*.
1. Wybierz folder z prawej strony i wybierz polecenie **Właściwości**.
1. Wybierz kartę **Zabezpieczenia,** a następnie wybierz pozycję **Edytuj**.
1. W oknie dialogowym *Uprawnienia dla funkcji CrossForestShare* wybierz pozycję **Dodaj**.
1. Wpisz *FileServerAccess* w **wprowadź nazwy obiektów do zaznaczenia**, a następnie wybierz przycisk **OK**.
1. Wybierz *pozycję FileServerAccess* z listy **Grupy lub nazwy użytkowników.** Na liście **Uprawnienia dla serwera Dostępu Do serwera plików** wybierz pozycję *Zezwalaj* na uprawnienia **Modyfikowania** i **zapisu,** a następnie wybierz przycisk **OK**.
1. Wybierz kartę **Udostępnianie,** a następnie wybierz pozycję **Udostępnianie zaawansowane...**
1. Wybierz **pozycję Udostępnij ten folder**, a następnie wprowadź zapadającą do zapamiętuszkę dla udziału plików w polu **Udostępnij nazwę,** taką jak *CrossForestShare*.
1. Wybierz **pozycję Uprawnienia**. Na liście **Uprawnienia dla wszystkich** wybierz pozycję **Zezwalaj** na uprawnienie **Zmiana.**
1. Wybierz **przycisk OK** dwa razy, a następnie **zamknij**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Sprawdzanie poprawności uwierzytelniania między lasami w zasobie

1. Zaloguj się na komputerze z systemem Windows przyłączonym do lokalnej usługi Active Directory przy użyciu konta użytkownika z lokalnej usługi Active Directory.
1. Za pomocą **Eksploratora Windows**połącz się z utworzonym `\\fs1.aaddscontoso.com\CrossforestShare`udziałem przy użyciu w pełni kwalifikowanej nazwy hosta i udziału, takiego jak .
1. Aby sprawdzić poprawność uprawnień do zapisu, wybierz prawym przyciskiem wyboru w folderze, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Dokument tekstowy**. Użyj domyślnej nazwy **Nowy dokument tekstowy**.

    Jeśli uprawnienia do zapisu są ustawione poprawnie, tworzony jest nowy dokument tekstowy. Następujące kroki zostaną następnie otwarte, edytowane i usuwane plik odpowiednio.
1. Aby sprawdzić poprawność uprawnienia do odczytu, otwórz **nowy dokument tekstowy**.
1. Aby sprawdzić poprawność uprawnień do modyfikowania, dodaj tekst do pliku i zamknij **Notatnik**. Po wyświetleniu monitu o zapisanie zmian wybierz pozycję **Zapisz**.
1. Aby sprawdzić poprawność uprawnień do usuwania, wybierz prawym przyciskiem wyboru **Nowy dokument tekstowy** i wybierz polecenie **Usuń**. Wybierz **pozycję Tak,** aby potwierdzić usunięcie pliku.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie systemu DNS w lokalnym środowisku usług AD DS w celu obsługi łączności usług Ad DS
> * Tworzenie jednokierunkowego przychodzącego zaufania lasu w lokalnym środowisku usług AD DS
> * Tworzenie jednokierunkowego wychodzącego zaufania lasu w usługach Azure AD DS
> * Testowanie i sprawdzanie poprawności relacji zaufania dla uwierzytelniania i dostępu do zasobów

Aby uzyskać więcej informacji koncepcyjnych dotyczących typów lasów w usługach Azure AD DS, zobacz Co to są [lasy][concepts-trust] [zasobów?][concepts-forest]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
