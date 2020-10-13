---
title: Zabezpieczanie danych przechowywanych w Azure Data Lake Storage Gen1 | Microsoft Docs
description: Dowiedz się, jak zabezpieczyć dane w Azure Data Lake Storage Gen1 przy użyciu grup i list kontroli dostępu
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 2dc802166b605ad7853c0910f1bab2a51f1f7297
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574147"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Securing data stored in Azure Data Lake Storage Gen1 (Zabezpieczanie danych przechowywanych w usłudze Azure Data Lake Storage Gen1)
Zabezpieczanie danych w Azure Data Lake Storage Gen1 jest podejściem trójwymiarowym.  Zarówno kontrola dostępu oparta na rolach (RBAC), jak i listy kontroli dostępu (ACL), muszą być ustawione, aby w pełni włączyć dostęp do danych dla użytkowników i grup zabezpieczeń.

1. Zacznij od utworzenia grup zabezpieczeń w Azure Active Directory (Azure AD). Te grupy zabezpieczeń służą do implementowania kontroli dostępu opartej na rolach (Azure RBAC) w Azure Portal. Aby uzyskać więcej informacji, zobacz [Azure RBAC](../role-based-access-control/role-assignments-portal.md).
2. Przypisz grupy zabezpieczeń usługi Azure AD do konta Data Lake Storage Gen1. Umożliwia to kontrolowanie dostępu do konta Data Lake Storage Gen1 z portalu i operacji zarządzania z poziomu portalu lub interfejsów API.
3. Przypisz grupy zabezpieczeń usługi Azure AD jako listy kontroli dostępu (ACL) w systemie plików Data Lake Storage Gen1.
4. Ponadto można również ustawić zakres adresów IP dla klientów, którzy mogą uzyskiwać dostęp do danych w Data Lake Storage Gen1.

Ten artykuł zawiera instrukcje dotyczące wykonywania powyższych zadań przy użyciu Azure Portal. Aby uzyskać szczegółowe informacje na temat sposobu, w jaki Data Lake Storage Gen1 implementuje zabezpieczenia na poziomie konta i danych, zobacz [zabezpieczenia w Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Aby uzyskać szczegółowe informacje na temat sposobu implementowania list ACL w Data Lake Storage Gen1, zobacz [omówienie Access Control w Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Tworzenie grup zabezpieczeń w Azure Active Directory
Aby uzyskać instrukcje dotyczące tworzenia grup zabezpieczeń usługi Azure AD i dodawania użytkowników do grupy, zobacz [Zarządzanie grupami zabezpieczeń w Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Możesz dodać użytkowników i inne grupy do grupy w usłudze Azure AD przy użyciu Azure Portal. Aby jednak dodać jednostkę usługi do grupy, użyj [modułu programu PowerShell usługi Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Przypisywanie użytkowników lub grup zabezpieczeń do kont Data Lake Storage Gen1
Podczas przypisywania użytkowników lub grup zabezpieczeń do kont Data Lake Storage Gen1 można kontrolować dostęp do operacji zarządzania na koncie przy użyciu interfejsów API Azure Portal i Azure Resource Manager. 

1. Otwórz konto Data Lake Storage Gen1. W okienku po lewej stronie kliknij pozycję **wszystkie zasoby**, a następnie w bloku wszystkie zasoby kliknij nazwę konta, do którego chcesz przypisać użytkownika lub grupę zabezpieczeń.

2. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Access Control (IAM)**. Domyślnie w bloku są wyświetlane właściciele subskrypcji jako właściciel.
   
    ![Przypisz grupę zabezpieczeń do konta Azure Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Przypisz grupę zabezpieczeń do konta Azure Data Lake Storage Gen1")

3. W bloku **Access Control (IAM)** kliknij przycisk **Dodaj** , aby otworzyć blok **Dodawanie uprawnień** . W bloku **Dodawanie uprawnień** wybierz **rolę** dla użytkownika/grupy. Wyszukaj grupę zabezpieczeń utworzoną wcześniej w Azure Active Directory i wybierz ją. Jeśli masz dużo użytkowników i grup do wyszukania, użyj pola tekstowego **Wybierz** , aby odfiltrować nazwę grupy. 
   
    ![Dodaj rolę dla użytkownika](./media/data-lake-store-secure-data/adl.add.user.1.png "Dodaj rolę dla użytkownika")
   
    Rola **właściciela** i **współautora** zapewniają dostęp do różnych funkcji administracyjnych na koncie Data Lake. Użytkownicy, którzy będą korzystać z danych w usłudze Data Lake, ale nadal muszą wyświetlać informacje dotyczące zarządzania kontami, możesz dodać ich do roli **czytelnik** . Zakres tych ról jest ograniczony do operacji zarządzania związanych z kontem Data Lake Storage Gen1.
   
    W przypadku operacji na danych poszczególne uprawnienia systemu plików definiują elementy, które mogą wykonywać użytkownicy. W związku z tym użytkownik mający rolę czytnika może wyświetlać tylko ustawienia administracyjne skojarzone z tym kontem, ale mogą być w stanie odczytywać i zapisywać dane na podstawie przypisanych im uprawnień systemu plików. Uprawnienia systemu plików Data Lake Storage Gen1 są opisane w [przypisywaniu grupy zabezpieczeń jako listy ACL do systemu plików Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Tylko rola **właściciela** automatycznie włącza dostęp do systemu plików. **Współautor**, **czytelnik**i wszystkie inne role wymagają list ACL, aby umożliwić dostęp do folderów i plików na dowolnym poziomie.  Rola **właściciela** zapewnia uprawnienia do plików i folderów administratora, których nie można zastąpić za pomocą list kontroli dostępu. Aby uzyskać więcej informacji o tym, jak zasady RBAC są mapowane na dostęp do danych, zobacz [RBAC dla zarządzania kontami](data-lake-store-security-overview.md#rbac-for-account-management).

4. Jeśli chcesz dodać grupę/użytkownika, którego nie ma na liście w bloku **Dodawanie uprawnień** , możesz zaprosić je, wpisując ich adresy e-mail w polu tekstowym **Wybierz** , a następnie wybierając je z listy.
   
    ![Dodawanie grupy zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.2.png "Dodawanie grupy zabezpieczeń")
   
5. Kliknij przycisk **Zapisz**. Powinna zostać wyświetlona dodana grupa zabezpieczeń, jak pokazano poniżej.
   
    ![Dodano grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.add.user.3.png "Dodano grupę zabezpieczeń")

6. Użytkownik/Grupa zabezpieczeń ma teraz dostęp do konta Data Lake Storage Gen1. Jeśli chcesz zapewnić dostęp do określonych użytkowników, możesz dodać ich do grupy zabezpieczeń. Podobnie, jeśli chcesz odwołać dostęp dla użytkownika, możesz je usunąć z grupy zabezpieczeń. Do konta można także przypisać wiele grup zabezpieczeń. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Przypisywanie użytkowników lub grup zabezpieczeń jako list ACL do systemu plików Data Lake Storage Gen1
Przypisując grupy użytkowników/zabezpieczeń do systemu plików Data Lake Storage Gen1, należy ustawić kontrolę dostępu dla danych przechowywanych w Data Lake Storage Gen1.

1. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.
   
    ![Wyświetlanie danych za pośrednictwem Eksplorator danych](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Wyświetlanie danych za pośrednictwem Eksplorator danych")
2. W bloku **Eksplorator danych** kliknij folder, dla którego chcesz skonfigurować listę kontroli dostępu, a następnie kliknij pozycję **dostęp**. Aby przypisać listy ACL do pliku, musisz najpierw kliknąć plik, aby go wyświetlić, a następnie kliknąć przycisk **dostęp** w bloku **Podgląd pliku** .
   
    ![Ustawianie list ACL w systemie plików Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Ustawianie list ACL w systemie plików Data Lake Storage Gen1")
3. Blok **dostęp** zawiera listę właścicieli i przypisanych uprawnień, które są już przypisane do katalogu głównego. Kliknij ikonę **Dodaj** , aby dodać dodatkowe listy ACL dostępu.
    > [!IMPORTANT]
    > Ustawienie uprawnień dostępu dla pojedynczego pliku nie musi przyznawać użytkownikowi/grupie dostępu do tego pliku. Ścieżka do pliku musi być dostępna dla przypisanego użytkownika/grupy. Aby uzyskać więcej informacji i przykładów, zobacz [typowe scenariusze związane z uprawnieniami](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Wyświetlanie listy standardowych i niestandardowych](./media/data-lake-store-secure-data/adl.acl.2.png "Wyświetlanie listy standardowych i niestandardowych")
   
   * **Właściciele** i **Wszyscy inni** zapewniają dostęp w stylu systemu UNIX, gdzie należy określić odczyt, zapis, Execute (RWX) do trzech różnych klas użytkowników: właściciel, Grupa i inne.
   * **Przypisane uprawnienia** odpowiadają listom kontroli dostępu POSIX, które umożliwiają ustawianie uprawnień dla określonych nazwanych użytkowników lub grup wykraczających poza właściciela lub grupę plików. 
     
     Aby uzyskać więcej informacji, zobacz [listy ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)systemu plików HDFS. Aby uzyskać więcej informacji na temat implementowania list ACL w Data Lake Storage Gen1, zobacz [Access Control w Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Kliknij ikonę **Dodaj** , aby otworzyć blok **przypisywanie uprawnień** . W tym bloku kliknij pozycję **Wybierz użytkownika lub grupę**, a następnie w bloku **Wybierz użytkownika lub grupę** Wyszukaj grupę zabezpieczeń utworzoną wcześniej w Azure Active Directory. Jeśli masz wiele grup do przeszukiwania, użyj pola tekstowego u góry, aby odfiltrować nazwę grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij pozycję **Wybierz**.
   
    ![Dodawanie grupy](./media/data-lake-store-secure-data/adl.acl.3.png "Dodawanie grupy")
5. Kliknij pozycję **Wybierz uprawnienia**, wybierz uprawnienia, czy uprawnienia mają być stosowane do rekursywnie, oraz czy chcesz przypisać uprawnienia jako listę ACL dostępu, domyślną listę ACL lub oba te elementy. Kliknij przycisk **OK**.
   
    ![Zrzut ekranu przedstawiający blok przypisywanie uprawnień z opcją wybierz uprawnienia o nazwie "i" Wybierz blok uprawnień z opcją OK o nazwie.](./media/data-lake-store-secure-data/adl.acl.4.png "Przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji o uprawnieniach w Data Lake Storage Gen1 i domyślnych listach kontroli dostępu, zobacz [Access Control w Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Po kliknięciu przycisku **OK** w bloku **Wybierz uprawnienia** nowo dodana grupa i skojarzone uprawnienia będą teraz wyświetlane w bloku **dostępu** .
   
    ![Zrzut ekranu przedstawiający blok dostępu z opcją Inżynieria danych o nazwie out.](./media/data-lake-store-secure-data/adl.acl.5.png "Przypisywanie uprawnień do grupy")
   
   > [!IMPORTANT]
   > W bieżącej wersji można mieć maksymalnie 28 wpisów w obszarze **przypisane uprawnienia**. Jeśli chcesz dodać więcej niż 28 użytkowników, należy utworzyć grupy zabezpieczeń, dodać użytkowników do grup zabezpieczeń, a następnie dodać dostęp do tych grup zabezpieczeń dla konta Data Lake Storage Gen1.
   > 
   > 
7. W razie potrzeby można także zmodyfikować uprawnienia dostępu po dodaniu grupy. Wyczyść lub zaznacz pole wyboru dla każdego typu uprawnienia (odczyt, zapis i wykonywanie) w zależności od tego, czy chcesz usunąć to uprawnienie do grupy zabezpieczeń, czy przypisać je. Kliknij przycisk **Zapisz** , aby zapisać zmiany, lub **Odrzuć** , aby cofnąć zmiany.

## <a name="set-ip-address-range-for-data-access"></a>Ustaw zakres adresów IP na potrzeby dostępu do danych
Data Lake Storage Gen1 umożliwia dalsze blokowanie dostępu do magazynu danych na poziomie sieci. Możesz włączyć zaporę, określić adres IP lub zdefiniować zakres adresów IP dla zaufanych klientów. Po włączeniu tylko klienci, którzy mają adresy IP w określonym zakresie, mogą łączyć się z magazynem.

![Ustawienia zapory i dostęp do protokołu IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Ustawienia zapory i adres IP")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Usuwanie grup zabezpieczeń dla konta Data Lake Storage Gen1
Po usunięciu grup zabezpieczeń z kont Data Lake Storage Gen1 można zmienić dostęp do operacji zarządzania na koncie przy użyciu interfejsów API Azure Portal i Azure Resource Manager.  

Dostęp do danych jest niezmieniony i nadal jest zarządzany przez listy ACL dostępu.  Wyjątkiem są użytkownicy/grupy w roli właściciele.  Użytkownicy/grupy usunięte z roli właściciele nie są już użytkownikami i ich dostęp do ustawień ACL jest uzyskiwany z powrotem. 

1. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Access Control (IAM)**. 
   
    ![Przypisz grupę zabezpieczeń do konta Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Przypisz grupę zabezpieczeń do konta Data Lake Storage Gen1")
2. W bloku **Access Control (IAM)** kliknij grupy zabezpieczeń, które chcesz usunąć. Kliknij przycisk **Usuń**.
   
    ![Usunięto grupę zabezpieczeń](./media/data-lake-store-secure-data/adl.remove.group.png "Usunięto grupę zabezpieczeń")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Usuwanie list ACL grup zabezpieczeń z systemu plików Data Lake Storage Gen1
Po usunięciu list ACL grup zabezpieczeń z systemu plików Data Lake Storage Gen1 można zmienić dostęp do danych na koncie Data Lake Storage Gen1.

1. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów na koncie Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Tworzenie katalogów na koncie Data Lake Storage Gen1")
2. W bloku **Eksplorator danych** kliknij folder, dla którego chcesz usunąć listę ACL, a następnie kliknij pozycję **dostęp**. Aby usunąć listy ACL dla pliku, musisz najpierw kliknąć plik, aby go wyświetlić, a następnie kliknąć przycisk **dostęp** w bloku **Podgląd pliku** . 
   
    ![Ustawianie list ACL w systemie plików Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Ustawianie list ACL w systemie plików Data Lake Storage Gen1")
3. W bloku **dostęp** kliknij grupę zabezpieczeń, którą chcesz usunąć. W bloku **szczegóły dostępu** kliknij pozycję **Usuń**.
   
    ![Zrzut ekranu przedstawiający blok dostęp z opcją Inżynieria danych o nazwie wychodzącą i blokiem szczegóły dostępu z opcją usuwania o nazwie out.](./media/data-lake-store-secure-data/adl.remove.acl.png "Przypisywanie uprawnień do grupy")

## <a name="see-also"></a>Zobacz też
* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Rozpocznij pracę z usługą Data Lake Storage 1. generacji przy użyciu programu PowerShell](data-lake-store-get-started-powershell.md)
* [Wprowadzenie do Data Lake Storage Gen1 przy użyciu zestawu SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
* [Uzyskiwanie dostępu do dzienników diagnostycznych dla Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)
