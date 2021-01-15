---
title: Samouczek — Tworzenie zaufania lasu w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak utworzyć jednokierunkowe lasy wychodzące do lokalnej domeny AD DS w Azure Portal dla Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: faa46178262777454d4d67d23bbd0bb013974ab5
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208492"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Samouczek: Tworzenie zaufania lasu wychodzącego do domeny lokalnej w Azure Active Directory Domain Services

W środowiskach, w których nie można synchronizować skrótów haseł lub masz użytkowników, którzy logują się wyłącznie przy użyciu kart inteligentnych, aby nie wiedzieli hasła, możesz użyć lasu zasobów w Azure Active Directory Domain Services (Azure AD DS). Las zasobów używa jednokierunkowego zaufania wychodzącego z platformy Azure AD DS do co najmniej jednego środowiska AD DS lokalnego. Ta relacja zaufania umożliwia użytkownikom, aplikacjom i komputerom uwierzytelnianie w domenie lokalnej z domeny zarządzanej AD DS platformy Azure. W lesie zasobów lokalne skróty haseł nigdy nie są synchronizowane.

![Diagram zaufania lasów z usługi Azure AD DS do lokalnego AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj system DNS w lokalnym środowisku AD DS, aby zapewnić obsługę łączności z usługą Azure AD DS
> * Utwórz jednokierunkowe zaufanie lasu przychodzącego w środowisku lokalnym AD DS
> * Tworzenie jednokierunkowego zaufania lasu wychodzącego na platformie Azure AD DS
> * Testowanie i weryfikowanie relacji zaufania na potrzeby uwierzytelniania i dostępu do zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena utworzona przy użyciu lasu zasobów i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Upewnij się, że tworzysz domenę zarządzaną przy użyciu lasu *zasobów* . Opcja domyślna powoduje utworzenie lasu *użytkownika* . Tylko lasy zasobów mogą tworzyć relacje zaufania w środowiskach Premium AD DS.
    >
    > Należy również użyć minimalnej jednostki SKU *przedsiębiorstwa* dla domeny zarządzanej. W razie potrzeby [Zmień jednostkę SKU dla domeny zarządzanej][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz i skonfigurujesz zaufanie lasu wychodzącego z usługi Azure AD DS przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

Sieć wirtualna, która hostuje Las zasobów AD DS platformy Azure, musi mieć łączność sieciową z Active Directory lokalnymi. Aplikacje i usługi potrzebują także łączności sieciowej z siecią wirtualną hostującym Las zasobów AD DS platformy Azure. Łączność sieciowa z lasem zasobów usługi Azure AD DS musi być zawsze włączona i stabilna, w przeciwnym razie użytkownicy mogą nie być uwierzytelniani ani uzyskiwać dostępu do zasobów.

Przed skonfigurowaniem zaufania lasu w usłudze Azure AD DS upewnij się, że sieć między platformą Azure i środowiskiem lokalnym spełnia następujące wymagania:

* Użyj prywatnych adresów IP. Nie należy polegać na usłudze DHCP z przypisaniem dynamicznego adresu IP.
* Należy unikać nakładania się przestrzeni adresów IP, aby umożliwić komunikację równorzędną i routing sieci wirtualnych między platformą Azure i środowiskiem lokalnym.
* Sieć wirtualna platformy Azure wymaga podsieci bramy w celu skonfigurowania połączenia [sieci VPN typu lokacja-lokacja (S2S) na platformie Azure][vpn-gateway] lub [ExpressRoute][expressroute]
* Utwórz podsieci z wystarczającą liczbą adresów IP, aby obsłużyć twój scenariusz.
* Upewnij się, że usługa Azure AD DS ma własną podsieć, nie udostępniaj tej podsieci sieci wirtualnej z maszynami wirtualnymi i usługami aplikacji.
* Równorzędne sieci wirtualne nie są przechodnie.
    * Komunikacja równorzędna sieci wirtualnych platformy Azure musi zostać utworzona między wszystkimi sieciami wirtualnymi, do których ma być używane zaufanie lasu zasobów platformy Azure AD DS do lokalnego środowiska AD DS.
* Zapewnianie ciągłej łączności sieciowej z lokalnym lasem Active Directory. Nie używaj połączeń na żądanie.
* Upewnij się, że istnieje ciągłe rozpoznawanie nazw (DNS) między nazwą lasu zasobów usługi Azure AD DS i nazwą lasu lokalnego Active Directory.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurowanie systemu DNS w domenie lokalnej

Aby prawidłowo rozpoznać domenę zarządzaną w środowisku lokalnym, może być konieczne dodanie usług przesyłania dalej do istniejących serwerów DNS. Jeśli nie skonfigurowano środowiska lokalnego do komunikowania się z domeną zarządzaną, wykonaj następujące czynności z poziomu stacji roboczej zarządzania dla lokalnej domeny AD DS:

1. Wybierz pozycję **Uruchom**  >  **Narzędzia administracyjne**  >  **DNS**.
1. Kliknij prawym przyciskiem myszy opcję serwer DNS, na przykład *myAD01*, a następnie wybierz polecenie **Właściwości**.
1. Wybierz opcję **usługi przesyłania dalej**, a następnie pozycję **Edytuj** , aby dodać dodatkowe usługi przesyłania dalej.
1. Dodaj adresy IP domeny zarządzanej, takie jak *10.0.2.4* i *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Utwórz przychodzące zaufanie lasu w domenie lokalnej

Lokalna domena AD DS musi mieć przychodzące zaufanie lasu dla domeny zarządzanej. Relację zaufania należy utworzyć ręcznie w domenie AD DS lokalnej, nie można jej utworzyć przy użyciu Azure Portal.

Aby skonfigurować zaufanie przychodzące w domenie AD DS lokalnego, wykonaj następujące kroki na stacji roboczej zarządzania dla lokalnej domeny AD DS:

1. Wybierz pozycję **Uruchom | Narzędzia administracyjne | Active Directory domen i relacji zaufania**.
1. Kliknij prawym przyciskiem myszy pozycję domena, na przykład *OnPrem.contoso.com*, a następnie wybierz pozycję **Właściwości**.
1. Wybierz kartę **relacje zaufania** , a następnie pozycję **nowe zaufanie**.
1. Wprowadź nazwę nazwy domeny usługi Azure AD DS, na przykład *aaddscontoso.com*, a następnie wybierz przycisk **dalej**.
1. Wybierz opcję utworzenia **zaufania lasu**, aby utworzyć **jeden ze sposobów: zaufanie przychodzące** .
1. Wybierz, aby utworzyć relację zaufania **tylko dla tej domeny**. W następnym kroku utworzysz relację zaufania w Azure Portal dla domeny zarządzanej.
1. Wybierz opcję użycia **uwierzytelniania w całym lesie**, a następnie wprowadź i Potwierdź hasło zaufania. To samo hasło jest również wprowadzane w Azure Portal w następnej sekcji.
1. Przejdź do kolejnych kilku okien z opcjami domyślnymi, a następnie wybierz opcję **nie, nie potwierdzaj zaufania wychodzącego**.
1. Wybierz pozycję **Zakończ**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Tworzenie zaufania dla lasu wychodzącego na platformie Azure AD DS

W przypadku lokalnej domeny AD DS skonfigurowanej w celu rozpoznania domeny zarządzanej i utworzenia przychodzącego zaufania lasu Utwórz teraz wychodzące zaufanie lasu. To wychodzące zaufanie lasu umożliwia zakończenie relacji zaufania między domeną lokalną AD DS i domeną zarządzaną.

Aby utworzyć zaufanie wychodzące dla domeny zarządzanej w Azure Portal, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**, a następnie wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Z menu po lewej stronie domeny zarządzanej wybierz pozycję **relacje zaufania**, a następnie wybierz pozycję **+ Dodaj** zaufanie.

   > [!NOTE]
   > Jeśli nie widzisz opcji menu **relacje zaufania** , sprawdź w obszarze **Właściwości** *typu lasu*. Tylko lasy *zasobów* mogą tworzyć relacje zaufania. Jeśli typ lasu to *User*, nie można utworzyć relacji zaufania. Obecnie nie ma możliwości zmiany typu lasu domeny zarządzanej. Musisz usunąć i utworzyć ponownie domenę zarządzaną jako Las zasobów.

1. Wprowadź nazwę wyświetlaną, która identyfikuje zaufanie, a następnie nazwę DNS lokalnego lasu zaufanego, na przykład *OnPrem.contoso.com*.
1. Podaj to samo hasło zaufania, które było używane podczas konfigurowania zaufania lasu przychodzącego dla lokalnej domeny AD DS w poprzedniej sekcji.
1. Podaj co najmniej dwa serwery DNS dla lokalnej domeny AD DS, takie jak *10.1.1.4* i *klienta 10.1.1.5*.
1. Gdy wszystko będzie gotowe, **Zapisz** wychodzące zaufanie lasu.

    ![Utwórz zaufanie do lasu wychodzącego w Azure Portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Weryfikowanie uwierzytelniania zasobów

Następujące typowe scenariusze pozwalają sprawdzić, czy zaufanie lasu prawidłowo uwierzytelnia użytkowników i dostęp do zasobów:

* [Uwierzytelnianie użytkownika lokalnego z lasu zasobów usługi Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Dostęp do zasobów w lesie zasobów AD DS platformy Azure przy użyciu lokalnego użytkownika](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Włącz udostępnianie plików i drukarek](#enable-file-and-printer-sharing)
    * [Tworzenie grupy zabezpieczeń i Dodawanie członków](#create-a-security-group-and-add-members)
    * [Tworzenie udziału plików na potrzeby dostępu między lasami](#create-a-file-share-for-cross-forest-access)
    * [Weryfikowanie uwierzytelniania między lasami w ramach zasobu](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Uwierzytelnianie użytkownika lokalnego z lasu zasobów usługi Azure AD DS

Należy mieć przyłączoną maszynę wirtualną z systemem Windows Server do domeny zarządzanej. Ta maszyna wirtualna służy do testowania lokalnego użytkownika na maszynie wirtualnej. W razie potrzeby [Utwórz maszynę wirtualną z systemem Windows i Przyłącz ją do domeny zarządzanej][join-windows-vm].

1. Połącz się z maszyną wirtualną z systemem Windows Server przyłączoną do lasu zasobów AD DS platformy Azure przy użyciu [usługi Azure bastionu](../bastion/bastion-overview.md) i poświadczeń administratora usługi Azure AD DS.
1. Otwórz wiersz polecenia i użyj polecenia, `whoami` Aby wyświetlić nazwę wyróżniającą aktualnie uwierzytelnionego użytkownika:

    ```console
    whoami /fqdn
    ```

1. Użyj `runas` polecenia, aby uwierzytelnić się jako użytkownik z domeny lokalnej. W poniższym poleceniu Zastąp `userUpn@trusteddomain.com` wartość nazwą UPN użytkownika z zaufanej domeny lokalnej. Polecenie poprosi o hasło użytkownika:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Jeśli uwierzytelnianie zakończyło się pomyślnie, zostanie otwarty nowy wiersz polecenia. Tytuł nowego wiersza polecenia zawiera `running as userUpn@trusteddomain.com` .
1. Użyj `whoami /fqdn` w nowym wierszu polecenia, aby wyświetlić nazwę wyróżniającą uwierzytelnionego użytkownika z lokalnej Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Dostęp do zasobów w lesie zasobów AD DS platformy Azure przy użyciu lokalnego użytkownika

Korzystając z maszyny wirtualnej systemu Windows Server dołączonej do lasu zasobów AD DS platformy Azure, można przetestować scenariusz, w którym użytkownicy mogą uzyskiwać dostęp do zasobów hostowanych w lesie zasobów podczas uwierzytelniania z komputerów w domenie lokalnej z użytkownikami z domeny lokalnej. W poniższych przykładach pokazano, jak tworzyć i testować różne typowe scenariusze.

#### <a name="enable-file-and-printer-sharing"></a>Włącz udostępnianie plików i drukarek

1. Połącz się z maszyną wirtualną z systemem Windows Server przyłączoną do lasu zasobów AD DS platformy Azure przy użyciu [usługi Azure bastionu](../bastion/bastion-overview.md) i poświadczeń administratora usługi Azure AD DS.

1. Otwórz okno **Ustawienia systemu Windows**, a następnie wyszukaj i wybierz pozycję **Centrum sieci i udostępniania**.
1. Wybierz opcję **Zmień zaawansowane ustawienia udostępniania** .
1. W obszarze **Profil domeny** wybierz pozycję **Włącz udostępnianie plików i drukarek** , a następnie **Zapisz zmiany**.
1. Zamknij **Centrum sieci i udostępniania**.

#### <a name="create-a-security-group-and-add-members"></a>Tworzenie grupy zabezpieczeń i Dodawanie członków

1. Otwórz narzędzie **Użytkownicy i komputery usługi Active Directory**.
1. Kliknij prawym przyciskiem myszy nazwę domeny, wybierz pozycję **Nowy**, a następnie wybierz pozycję **jednostka organizacyjna**.
1. W polu Nazwa wpisz *LocalObjects*, a następnie wybierz przycisk **OK**.
1. Wybierz i kliknij prawym przyciskiem myszy pozycję **LocalObjects** w okienku nawigacji. Wybierz opcję **Nowy** , a następnie pozycję **Grupuj**.
1. W polu **Nazwa grupy** wpisz *FileServerAccess* . W obszarze **zakres grupy** wybierz pozycję **domena lokalna**, a następnie wybierz przycisk **OK**.
1. W okienku zawartości kliknij dwukrotnie pozycję **FileServerAccess**. Wybierz pozycję **Członkowie**, wybierz opcję **Dodaj**, a następnie wybierz pozycję **lokalizacje**.
1. Wybierz Active Directory lokalnego z widoku **Lokalizacja** , a następnie wybierz przycisk **OK**.
1. Wpisz *Użytkownicy domeny* w polu **Wprowadź nazwy obiektów do wybrania** . Wybierz pozycję **Sprawdź nazwy**, podaj poświadczenia dla Active Directory lokalnego, a następnie wybierz **przycisk OK**.

    > [!NOTE]
    > Musisz podać poświadczenia, ponieważ relacja zaufania ma tylko jeden sposób. Oznacza to, że użytkownicy z domeny zarządzanej AD DS platformy Azure nie mogą uzyskać dostępu do zasobów ani wyszukiwać użytkowników lub grup w domenie zaufanej (lokalnej).

1. Grupa **Użytkownicy domeny** z lokalnego Active Directory powinna być członkiem grupy **FileServerAccess** . Wybierz **przycisk OK** , aby zapisać grupę i zamknąć okno.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Tworzenie udziału plików na potrzeby dostępu między lasami

1. Na maszynie wirtualnej z systemem Windows Server przyłączonym do lasu zasobów usługi Azure AD DS Utwórz folder i podaj nazwę, taką jak *CrossForestShare*.
1. Kliknij prawym przyciskiem myszy folder i wybierz polecenie **Właściwości**.
1. Wybierz kartę **zabezpieczenia** , a następnie wybierz pozycję **Edytuj**.
1. W oknie dialogowym *uprawnienia dla CrossForestShare* wybierz pozycję **Dodaj**.
1. Wpisz *FileServerAccess* w polu **Wprowadź nazwy obiektów do wybrania**, a następnie wybierz przycisk **OK**.
1. Wybierz pozycję *FileServerAccess* z listy **nazwy grup lub użytkowników** . Na liście **uprawnienia dla FileServerAccess** wybierz opcję *Zezwalaj* na uprawnienia **Modyfikacja** i **zapis** , a następnie wybierz przycisk **OK**.
1. Wybierz kartę **udostępnianie** , a następnie wybierz pozycję **Udostępnianie zaawansowane...**.
1. Wybierz opcję **Udostępnij ten folder**, a następnie wprowadź nazwę zapamiętania udziału plików w polu **Nazwa udziału** , np. *CrossForestShare*.
1. Wybierz pozycję **Uprawnienia**. Na liście **uprawnienia dla wszystkich użytkowników** wybierz opcję **Zezwalaj** na uprawnienie **zmiana** .
1. Wybierz przycisk **OK** dwa razy, a następnie **Zamknij**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Weryfikowanie uwierzytelniania między lasami w ramach zasobu

1. Zaloguj się na komputerze z systemem Windows przyłączonym do lokalnego Active Directory przy użyciu konta użytkownika z Active Directory lokalnego.
1. Korzystając z **Eksploratora Windows**, Połącz się z udziałem utworzonym przy użyciu w pełni kwalifikowanej nazwy hosta i udziału, takiego jak `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Aby sprawdzić poprawność uprawnień do zapisu, zaznacz w folderze prawym przyciskiem myszy, wybierz polecenie **Nowy**, a następnie wybierz pozycję **dokument tekstowy**. Użyj domyślnej nazwy **nowego dokumentu tekstowego**.

    Jeśli uprawnienia do zapisu są ustawione prawidłowo, zostanie utworzony nowy dokument tekstowy. Poniższe kroki będą otwierać, edytować i usuwać plik stosownie do potrzeb.
1. Aby sprawdzić uprawnienia do odczytu, Otwórz **Nowy dokument tekstowy**.
1. Aby zweryfikować uprawnienia modyfikacja, Dodaj tekst do pliku i Zamknij **Notatnik**. Po wyświetleniu monitu o zapisanie zmian wybierz pozycję **Zapisz**.
1. Aby sprawdzić poprawność uprawnienia do usuwania, kliknij prawym przyciskiem myszy pozycję **Nowy dokument tekstowy** i wybierz polecenie **Usuń**. Wybierz opcję **tak** , aby potwierdzić usunięcie pliku.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj system DNS w lokalnym środowisku AD DS, aby zapewnić obsługę łączności z usługą Azure AD DS
> * Utwórz jednokierunkowe zaufanie lasu przychodzącego w środowisku lokalnym AD DS
> * Tworzenie jednokierunkowego zaufania lasu wychodzącego na platformie Azure AD DS
> * Testowanie i weryfikowanie relacji zaufania na potrzeby uwierzytelniania i dostępu do zasobów

Aby uzyskać więcej informacji koncepcyjnych o typach lasów w usłudze Azure AD DS, zobacz [co to są lasy zasobów?][concepts-forest] czy [relacje zaufania lasów działają w usłudze Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md