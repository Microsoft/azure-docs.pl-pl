---
title: Prywatne punkty końcowe
description: Zapoznaj się z procesem tworzenia prywatnych punktów końcowych dla Azure Backup i scenariuszy, w których używanie prywatnych punktów końcowych pomaga zachować bezpieczeństwo zasobów.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 7423157abbc0833394af055f5e31f724caa10b46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224711"
---
# <a name="private-endpoints-for-azure-backup"></a>Prywatne punkty końcowe dla Azure Backup

Azure Backup pozwala na bezpieczne wykonywanie kopii zapasowych i przywracanie danych z magazynów Recovery Services przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). Prywatne punkty końcowe wykorzystują co najmniej jeden prywatny adres IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej.

Ten artykuł pomoże Ci zrozumieć proces tworzenia prywatnych punktów końcowych dla Azure Backup i scenariuszy, w których używanie prywatnych punktów końcowych pomaga zachować bezpieczeństwo zasobów.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Prywatne punkty końcowe można utworzyć tylko dla nowych magazynów Recovery Services (nie ma żadnych elementów zarejestrowanych w magazynie). Należy utworzyć prywatne punkty końcowe przed podjęciem próby ochrony wszystkich elementów w magazynie.
- Jedna sieć wirtualna może zawierać prywatne punkty końcowe dla wielu magazynów Recovery Services. Ponadto jeden magazyn Recovery Services może mieć prywatne punkty końcowe dla niego w wielu sieciach wirtualnych. Jednak Maksymalna liczba prywatnych punktów końcowych, które można utworzyć dla magazynu, wynosi 12.
- Po utworzeniu prywatnego punktu końcowego dla magazynu magazyn zostanie zablokowany. Nie będzie on dostępny (w przypadku tworzenia kopii zapasowych i przywracania) z sieci poza tymi, które zawierają prywatny punkt końcowy dla magazynu. Jeśli wszystkie prywatne punkty końcowe dla magazynu zostaną usunięte, magazyn będzie dostępny ze wszystkich sieci.
- Połączenie prywatnego punktu końcowego dla kopii zapasowej używa łącznie 11 prywatnych adresów IP w podsieci, łącznie z tymi używanymi przez Azure Backup na potrzeby magazynu. Ta liczba może być większa (do 25) w przypadku niektórych regionów świadczenia usługi Azure. Zalecamy, aby przy próbie utworzenia prywatnych punktów końcowych dla kopii zapasowej była dostępna wystarczająca liczba prywatnych adresów IP.
- Magazyn Recovery Services jest używany przez program (oba) Azure Backup i Azure Site Recovery w tym artykule omówiono użycie prywatnych punktów końcowych tylko dla Azure Backup.
- Azure Active Directory nie obsługuje obecnie prywatnych punktów końcowych. Aby adresy IP i nazwy FQDN wymagane do Azure Active Directory pracy w regionie muszą mieć dozwolony dostęp wychodzący z zabezpieczonej sieci podczas wykonywania kopii zapasowej baz danych na maszynach wirtualnych platformy Azure i kopii zapasowej przy użyciu agenta MARS. Możesz również użyć tagów sieciowej grupy zabezpieczeń i tagów zapory platformy Azure, aby umożliwić dostęp do usługi Azure AD, zgodnie z wymaganiami.
- Sieci wirtualne z zasadami sieci nie są obsługiwane dla prywatnych punktów końcowych. Przed kontynuowaniem należy [wyłączyć zasady sieci](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy) .
- Należy ponownie zarejestrować dostawcę zasobów Recovery Services z subskrypcją, jeśli został on zarejestrowany przed 1 2020 maja. Aby ponownie zarejestrować dostawcę, przejdź do subskrypcji w Azure Portal, przejdź do pozycji **dostawca zasobów** na lewym pasku nawigacyjnym, a następnie wybierz pozycję **Microsoft. RecoveryServices** i wybierz pozycję **zarejestruj ponownie**.
- [Przywracanie między regionami](backup-create-rs-vault.md#set-cross-region-restore) dla kopii zapasowych SQL i SAP HANA bazy danych nie jest obsługiwane, jeśli magazyn ma włączone prywatne punkty końcowe.
- Gdy przeniesiesz magazyn Recovery Services już korzystający z prywatnych punktów końcowych do nowej dzierżawy, musisz zaktualizować magazyn Recovery Services, aby ponownie utworzyć i ponownie skonfigurować tożsamość zarządzaną magazynu i utworzyć nowe prywatne punkty końcowe zgodnie z potrzebami (które powinny znajdować się w nowej dzierżawie). Jeśli to nie zrobisz, operacje tworzenia kopii zapasowej i przywracania zakończą się niepowodzeniem. Ponadto należy zmienić konfigurację wszystkich uprawnień kontroli dostępu opartej na rolach (RBAC) skonfigurowanych w ramach subskrypcji.

## <a name="recommended-and-supported-scenarios"></a>Zalecane i obsługiwane scenariusze

Gdy prywatne punkty końcowe są włączone dla magazynu, są używane do tworzenia kopii zapasowych i przywracania danych SQL i SAP HANA obciążeń na maszynach wirtualnych platformy Azure i w ramach kopii zapasowej agenta MARS. Magazyn można również używać do tworzenia kopii zapasowych innych obciążeń (nie wymagają jednak prywatnych punktów końcowych). Oprócz tworzenia kopii zapasowych obciążeń SQL i SAP HANA i tworzenia kopii zapasowej przy użyciu agenta MARS, prywatne punkty końcowe są również używane do odzyskiwania plików dla kopii zapasowej maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz następującą tabelę:

| Tworzenie kopii zapasowych obciążeń na maszynie wirtualnej platformy Azure (SQL, SAP HANA), kopia zapasowa przy użyciu agenta MARS | Zaleca się używanie prywatnych punktów końcowych, aby umożliwić wykonywanie kopii zapasowych i przywracanie bez konieczności dodawania do listy dozwolonych adresów IP/nazw FQDN dla Azure Backup lub usługi Azure Storage z sieci wirtualnych. W tym scenariuszu upewnij się, że maszyny wirtualne, które obsługują bazy danych SQL, mogą uzyskać dostęp do adresów IP lub nazw FQDN usługi Azure AD. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Kopia zapasowa maszyny wirtualnej platformy Azure**                                         | Kopia zapasowa maszyny wirtualnej nie wymaga zezwolenia na dostęp do żadnych adresów IP ani nazw FQDN. Dlatego nie wymaga prywatnych punktów końcowych do tworzenia kopii zapasowych i przywracania dysków.  <br><br>   Jednak odzyskiwanie plików z magazynu zawierającego prywatne punkty końcowe będzie ograniczone do sieci wirtualnych, które zawierają prywatny punkt końcowy dla magazynu. <br><br>    W przypadku korzystania z dysków niezarządzanych ACL'ed upewnij się, że konto magazynu zawierające dyski umożliwia dostęp do **zaufanych usług firmy Microsoft** , jeśli jest to ACL'ed. |
| **Azure Files kopia zapasowa**                                      | Kopie zapasowe Azure Files są przechowywane na lokalnym koncie magazynu. Dlatego nie wymaga prywatnych punktów końcowych do tworzenia kopii zapasowych i przywracania. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Wprowadzenie do tworzenia prywatnych punktów końcowych dla kopii zapasowej

W poniższych sekcjach omówiono kroki związane z tworzeniem i używaniem prywatnych punktów końcowych w celu Azure Backup wewnątrz sieci wirtualnych.

>[!IMPORTANT]
> Zdecydowanie zaleca się wykonanie kroków w tej samej kolejności, jak wspomniano w tym dokumencie. Niewykonanie tej czynności może spowodować, że magazyn jest renderowany niezgodny z prywatnymi punktami końcowymi i wymaga ponownego uruchomienia procesu z nowym magazynem.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Prywatne punkty końcowe dla kopii zapasowej można utworzyć tylko dla magazynów Recovery Services, które nie mają żadnych elementów chronionych do nich (lub nie miały żadnych elementów, które próbowano chronić lub zarejestrowano w przeszłości). Zalecamy utworzenie nowego magazynu, aby rozpocząć pracę z usługą. Aby uzyskać więcej informacji na temat tworzenia nowego magazynu, zobacz  [Tworzenie i Konfigurowanie magazynu Recovery Services](backup-create-rs-vault.md).

Zapoznaj się z [tą sekcją](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) , aby dowiedzieć się, jak utworzyć magazyn przy użyciu klienta Azure Resource Manager. Spowoduje to utworzenie magazynu z już włączoną zarządzaną tożsamością.

## <a name="enable-managed-identity-for-your-vault"></a>Włącz zarządzaną tożsamość magazynu

Zarządzane tożsamości umożliwiają magazynowi tworzenie i używanie prywatnych punktów końcowych. W tej sekcji omówiono Włączanie zarządzanej tożsamości magazynu.

1. Przejdź do magazynu Recovery Services — > **tożsamość**.

    ![Zmień stan tożsamości na włączone](./media/private-endpoints/identity-status-on.png)

1. Zmień **stan** na **włączone** i wybierz pozycję **Zapisz**.

1. Generowany jest **Identyfikator obiektu** , który jest tożsamością zarządzaną magazynu.

    >[!NOTE]
    >Po włączeniu **nie** można wyłączyć zarządzanej tożsamości (nawet tymczasowo). Wyłączenie zarządzanej tożsamości może prowadzić do niespójnych zachowań.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Udziel uprawnień do magazynu w celu utworzenia wymaganych prywatnych punktów końcowych

Aby można było utworzyć wymagane prywatne punkty końcowe dla Azure Backup, magazyn (zarządzana tożsamość magazynu) musi mieć uprawnienia do następujących grup zasobów:

- Grupa zasobów, która zawiera docelową sieć wirtualną
- Grupa zasobów, w której mają zostać utworzone prywatne punkty końcowe
- Grupa zasobów zawierająca Prywatna strefa DNS strefy, jak opisano szczegółowo w [tym miejscu](#create-private-endpoints-for-azure-backup)

Zalecamy przyznanie roli **współautor** dla tych trzech grup zasobów do magazynu (tożsamość zarządzana). W poniższych krokach opisano, jak to zrobić dla określonej grupy zasobów (należy to zrobić dla każdej z trzech grup zasobów):

1. Przejdź do grupy zasobów i przejdź do pozycji **Access Control (IAM)** na pasku po lewej stronie.
1. Raz w **Access Control** przejdź do obszaru **Dodawanie przypisania roli**.

    ![Dodaj przypisanie roli](./media/private-endpoints/add-role-assignment.png)

1. W okienku **Dodaj przypisanie roli** wybierz **współautor** jako **rolę** i Użyj **nazwy** magazynu jako **podmiotu zabezpieczeń**. Wybierz swój magazyn i wybierz pozycję **Zapisz** po zakończeniu.

    ![Wybieranie roli i podmiotu zabezpieczeń](./media/private-endpoints/choose-role-and-principal.png)

Aby zarządzać uprawnieniami na bardziej szczegółowym poziomie, zobacz [Ręczne tworzenie ról i uprawnień](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Utwórz prywatne punkty końcowe dla Azure Backup

W tej sekcji opisano sposób tworzenia prywatnego punktu końcowego dla magazynu.

1. Przejdź do utworzonego powyżej magazynu i przejdź do **połączeń prywatnych punktów końcowych** na pasku nawigacyjnym po lewej stronie. Wybierz pozycję **+ prywatny punkt końcowy** u góry, aby rozpocząć tworzenie nowego prywatnego punktu końcowego dla tego magazynu.

    ![Utwórz nowy prywatny punkt końcowy](./media/private-endpoints/new-private-endpoint.png)

1. W procesie **tworzenia prywatnego punktu końcowego** należy określić szczegóły dotyczące tworzenia połączenia prywatnego punktu końcowego.
  
    1. **Podstawowe**: Podaj podstawowe informacje o prywatnych punktach końcowych. Region powinien być taki sam jak magazyn i zasób, którego kopia zapasowa ma zostać utworzona.

        ![Wypełnij podstawowe szczegóły](./media/private-endpoints/basics-tab.png)

    1. **Zasób**: Ta karta wymaga wybrania zasobu PaaS, dla którego chcesz utworzyć połączenie. Wybierz pozycję **Microsoft. RecoveryServices/magazyny** z typu zasobu dla żądanej subskrypcji. Po zakończeniu wybierz nazwę magazynu Recovery Services jako **zasób** i **AzureBackup** jako **docelowy zasób podrzędny**.

        ![Wybierz zasób dla połączenia](./media/private-endpoints/resource-tab.png)

    1. **Konfiguracja**: w obszarze Konfiguracja Określ sieć wirtualną i podsieć, w której ma zostać utworzony prywatny punkt końcowy. Będzie to sieć wirtualna, w której znajduje się maszyna wirtualna.

        Aby połączyć się prywatnie, potrzebne są wymagane rekordy DNS. Na podstawie konfiguracji sieci można wybrać jedną z następujących opcji:

          - Zintegruj prywatny punkt końcowy z prywatną strefą DNS: wybierz opcję **tak** , jeśli chcesz przeprowadzić integrację.
          - Użyj niestandardowego serwera DNS: wybierz pozycję **nie** , jeśli chcesz użyć własnego serwera DNS.

        Zarządzanie rekordami DNS w obu tych tematach [opisano w dalszej części artykułu](#manage-dns-records).

          ![Określ sieć wirtualną i podsieć](./media/private-endpoints/configuration-tab.png)

    1. Opcjonalnie możesz dodać **Tagi** dla prywatnego punktu końcowego.
    1. Kontynuuj **przeglądanie i tworzenie** po zakończeniu wprowadzania szczegółów. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.

## <a name="approve-private-endpoints"></a>Zatwierdzanie prywatnych punktów końcowych

Jeśli użytkownik tworzący prywatny punkt końcowy jest również właścicielem magazynu Recovery Services, prywatny punkt końcowy utworzony powyżej zostanie zaakceptowany. W przeciwnym razie właściciel magazynu musi zatwierdzić prywatny punkt końcowy, zanim będzie mógł go używać. W tej sekcji omówiono ręczne zatwierdzanie prywatnych punktów końcowych za pomocą Azure Portal.

Zobacz [Ręczne zatwierdzanie prywatnych punktów końcowych przy użyciu klienta Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) , aby użyć klienta Azure Resource Manager do zatwierdzania prywatnych punktów końcowych.

1. W magazynie Recovery Services przejdź do **połączeń prywatnych punktów końcowych** na pasku po lewej stronie.
1. Wybierz połączenie prywatnego punktu końcowego, które chcesz zatwierdzić.
1. Wybierz pozycję **Zatwierdź** na górnym pasku. Możesz również wybrać opcję **Odrzuć** lub **Usuń** , jeśli chcesz odrzucić lub usunąć połączenie z punktem końcowym.

    ![Zatwierdzanie prywatnych punktów końcowych](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Zarządzanie rekordami DNS

Zgodnie z powyższym opisem wymagane rekordy DNS w prywatnych strefach DNS lub serwerach mają być połączone prywatnie. Możesz zintegrować prywatny punkt końcowy bezpośrednio z prywatnymi strefami DNS platformy Azure lub użyć niestandardowych serwerów DNS do osiągnięcia tego w zależności od preferencji sieci. Należy to zrobić dla wszystkich trzech usług: kopii zapasowych, obiektów blob i kolejek.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Podczas integrowania prywatnych punktów końcowych z prywatnymi strefami DNS platformy Azure

W przypadku wybrania integracji prywatnego punktu końcowego z prywatnymi strefami DNS usługa Backup doda wymagane rekordy DNS. Można wyświetlić prywatne strefy DNS używane w ramach **konfiguracji DNS** prywatnego punktu końcowego. Jeśli te strefy DNS nie są obecne, zostaną utworzone automatycznie podczas tworzenia prywatnego punktu końcowego. Należy jednak sprawdzić, czy sieć wirtualna (zawierająca zasoby, których kopia zapasowa ma zostać utworzona), jest prawidłowo połączona ze wszystkimi trzema prywatnymi strefami DNS, zgodnie z poniższym opisem.

![Konfiguracja systemu DNS w prywatnej strefie DNS platformy Azure](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Weryfikuj linki sieci wirtualnej w prywatnych strefach DNS

Dla **każdej prywatnej strefy DNS** wymienionej powyżej (dla kopii zapasowych, obiektów blob i kolejek) wykonaj następujące czynności:

1. Przejdź do odpowiedniej opcji **łącza sieci wirtualnej** na lewym pasku nawigacyjnym.
1. Powinien być widoczny wpis dla sieci wirtualnej, dla której został utworzony prywatny punkt końcowy, tak jak pokazano poniżej:

    ![Sieć wirtualna dla prywatnego punktu końcowego](./media/private-endpoints/virtual-network-links.png)

1. Jeśli nie widzisz wpisu, Dodaj łącze do sieci wirtualnej do wszystkich tych stref DNS, które ich nie posiadają.

    ![Dodaj łącze sieci wirtualnej](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>W przypadku korzystania z niestandardowego serwera DNS lub plików hosta

Jeśli używasz niestandardowych serwerów DNS, musisz utworzyć wymagane strefy DNS i dodać rekordy DNS wymagane przez prywatne punkty końcowe do serwerów DNS. W przypadku obiektów blob i kolejek można także użyć usług przesyłania dalej warunkowego.

#### <a name="for-the-backup-service"></a>Dla usługi kopii zapasowej

1. Na serwerze DNS Utwórz strefę DNS dla kopii zapasowej zgodnie z następującą konwencją nazewnictwa:

    |Strefa |Usługa |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > W powyższym tekście `<geo>` odwołuje się do kodu regionu (na przykład *EUS* i *ne* dla Wschodnie stany USA i Europa Północna). Zapoznaj się z następującymi listami dla kodów regionów:
    >
    > - [Wszystkie chmury publiczne](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [Chiny](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Niemcy](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. Następnie musimy dodać wymagane rekordy DNS. Aby wyświetlić rekordy, które należy dodać do strefy DNS kopii zapasowej, przejdź do prywatnego punktu końcowego utworzonego powyżej i przejdź do opcji **konfiguracji DNS** na lewym pasku nawigacyjnym.

    ![Konfiguracja DNS dla niestandardowego serwera DNS](./media/private-endpoints/custom-dns-configuration.png)

1. Dodaj jedną pozycję dla każdej nazwy FQDN i adresu IP wyświetlanej jako rekordy typu w strefie DNS na potrzeby tworzenia kopii zapasowych. Jeśli używasz pliku hosta do rozpoznawania nazw, wprowadź odpowiednie wpisy w pliku hosta dla każdego adresu IP i nazwy FQDN zgodnie z następującym formatem:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Jak pokazano na poniższym zrzucie ekranu, nazwy FQDN przedstawiają się `xxxxxxxx.<geo>.backup.windowsazure.com` i nie `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . W takich przypadkach upewnij się, że dołączysz (i w razie potrzeby Dodaj) `.privatelink.` zgodnie z ustalonym formatem.

#### <a name="for-blob-and-queue-services"></a>Dla usług BLOB i Queue

W przypadku obiektów blob i kolejek można użyć usług przesyłania dalej warunkowego lub utworzyć strefy DNS na serwerze DNS.

##### <a name="if-using-conditional-forwarders"></a>W przypadku korzystania z usług przesyłania dalej warunkowego

Jeśli używasz usług przesyłania dalej warunkowego, Dodaj usługi przesyłania dalej dla obiektów blob i FQDN w kolejce w następujący sposób:

|Nazwa FQDN  |Adres IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Jeśli używane są prywatne strefy DNS

Jeśli używasz stref DNS dla obiektów blob i kolejek, musisz najpierw utworzyć te strefy DNS i później dodać wymagane rekordy.

|Strefa |Usługa  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Obiekt blob     |
|`privatelink.queue.core.windows.net`     | Kolejka        |

W tej chwili utworzymy tylko strefy dla obiektów blob i kolejek w przypadku korzystania z niestandardowych serwerów DNS. Dodawanie rekordów DNS zostanie wykonane później w dwóch krokach:

1. Po zarejestrowaniu pierwszego wystąpienia kopii zapasowej, czyli podczas konfigurowania kopii zapasowej po raz pierwszy
1. Po uruchomieniu pierwszej kopii zapasowej

Te kroki zostaną wykonane w poniższych sekcjach.

## <a name="use-private-endpoints-for-backup"></a>Używanie prywatnych punktów końcowych do tworzenia kopii zapasowych

Gdy prywatne punkty końcowe utworzone dla magazynu w sieci wirtualnej zostaną zatwierdzone, możesz zacząć ich używać do wykonywania kopii zapasowych i przywracania.

>[!IMPORTANT]
>Przed kontynuowaniem upewnij się, że wszystkie kroki wymienione powyżej zostały wykonane pomyślnie. Aby podsumowanie, należy wykonać kroki opisane na poniższej liście kontrolnej:
>
>1. Utworzono magazyn Recovery Services (nowy)
>2. Włączono magazyn do korzystania z tożsamości zarządzanej przypisanej przez system
>3. Przypisano odpowiednie uprawnienia do zarządzanej tożsamości magazynu
>4. Utworzono prywatny punkt końcowy dla magazynu
>5. Zatwierdzono prywatny punkt końcowy (jeśli nie został on zaakceptowany)
>6. Upewnij się, że wszystkie rekordy DNS zostały odpowiednio dodane (oprócz rekordów obiektów blob i kolejek dla serwerów niestandardowych, które zostaną omówione w poniższych sekcjach)

### <a name="check-vm-connectivity"></a>Sprawdź łączność z maszyną wirtualną

Na maszynie wirtualnej w zablokowanej sieci upewnij się, że:

1. Maszyna wirtualna powinna mieć dostęp do usługi AAD.
2. Aby zapewnić łączność, wykonaj polecenie **nslookup** na adresie URL kopii zapasowej ( `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` ) z maszyny wirtualnej. Powinno to zwrócić prywatny adres IP przypisany w sieci wirtualnej.

### <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Po upewnieniu się, że powyższa lista kontrolna zostanie pomyślnie ukończona, można nadal skonfigurować tworzenie kopii zapasowych obciążeń do magazynu. Jeśli używasz niestandardowego serwera DNS, musisz dodać wpisy DNS dla obiektów blob i kolejek, które są dostępne po skonfigurowaniu pierwszej kopii zapasowej.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>Rekordy DNS dla obiektów blob i kolejek (tylko dla niestandardowych serwerów DNS/plików hosta) po pierwszej rejestracji

Po skonfigurowaniu kopii zapasowej dla co najmniej jednego zasobu w magazynie z włączoną obsługą prywatnego punktu końcowego Dodaj wymagane rekordy DNS dla obiektów blob i kolejek zgodnie z poniższym opisem.

1. Przejdź do grupy zasobów, a następnie wyszukaj utworzony prywatny punkt końcowy.
1. Poza podaną przez Ciebie nazwą prywatnego punktu końcowego zobaczysz dwa więcej prywatnych punktów końcowych. Te zaczynają się od `<the name of the private endpoint>_ecs` i są sufiksem `_blob` i `_queue` odpowiednio.

    ![Zasoby prywatnego punktu końcowego](./media/private-endpoints/private-endpoint-resources.png)

1. Przejdź do każdego z tych prywatnych punktów końcowych. W opcji Konfiguracja DNS dla każdego z dwóch prywatnych punktów końcowych zostanie wyświetlony rekord z nazwą FQDN i adresem IP. Dodaj oba te elementy do niestandardowego serwera DNS, oprócz tych opisanych wcześniej.
Jeśli używasz pliku hosta, wprowadź odpowiednie wpisy w pliku hosta dla każdego adresu IP/FQDN zgodnie z następującym formatem:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Konfiguracja usługi BLOB DNS](./media/private-endpoints/blob-dns-configuration.png)

Oprócz powyższego, po wykonaniu pierwszej kopii zapasowej należy wykonać kolejną pozycję, która została omówiona [później](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Tworzenie kopii zapasowych i przywracanie obciążeń na maszynie wirtualnej platformy Azure (SQL i SAP HANA)

Po utworzeniu i zatwierdzeniu prywatnego punktu końcowego do korzystania z prywatnego punktu końcowego nie są wymagane żadne inne zmiany (chyba że są używane grupy dostępności SQL, które omówiono w dalszej części tej sekcji). Cała komunikacja i transfer danych z zabezpieczonej sieci do magazynu będzie odbywać się za pośrednictwem prywatnego punktu końcowego. Jeśli jednak usuniesz prywatne punkty końcowe dla magazynu po zarejestrowaniu serwera (SQL lub SAP HANA), należy ponownie zarejestrować kontener z magazynem. Nie musisz zatrzymać ochrony dla nich.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>Rekordy DNS dla obiektów BLOB (tylko dla niestandardowych serwerów DNS/plików hosta) po pierwszej kopii zapasowej

Po uruchomieniu pierwszej kopii zapasowej i użyciu niestandardowego serwera DNS (bez warunkowego przekazywania) prawdopodobnie kopia zapasowa zakończy się niepowodzeniem. W takim przypadku:

1. Przejdź do grupy zasobów, a następnie wyszukaj utworzony prywatny punkt końcowy.
1. Oprócz trzech prywatnych punktów końcowych omówionych wcześniej zobaczysz teraz czwarty prywatny punkt końcowy z nazwą rozpoczynającą się od `<the name of the private endpoint>_prot` i są sufiksem `_blob` .

    ![Prywatny endpoing z sufiksem "Prot"](./media/private-endpoints/private-endpoint-prot.png)

1. Przejdź do tego nowego prywatnego punktu końcowego. W opcji Konfiguracja DNS zobaczysz rekord z nazwą FQDN i adresem IP. Dodaj je do prywatnego serwera DNS, a także do tych, które zostały opisane wcześniej.

    Jeśli używasz pliku hosta, wprowadź odpowiednie wpisy w pliku hosta dla każdego adresu IP i nazwy FQDN zgodnie z następującym formatem:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>W tym momencie powinno być możliwe uruchomienie **polecenia nslookup** z maszyny wirtualnej i rozwiązaniu się z prywatnymi adresami IP w przypadku tworzenia kopii zapasowych i adresów URL magazynu.

### <a name="when-using-sql-availability-groups"></a>W przypadku korzystania z grup dostępności SQL

W przypadku korzystania z grup dostępności SQL (AG) należy zastanowić się nad przekazaniem warunkowym w niestandardowym systemie DNS, zgodnie z poniższym opisem:

1. Zaloguj się do kontrolera domeny.
1. W obszarze aplikacji DNS Dodaj usługi warunkowego przesyłania dalej dla wszystkich trzech stref DNS (kopii zapasowych, obiektów blob i kolejek) do 168.63.129.16 IP hosta lub niestandardowego adresu IP serwera DNS, jeśli jest to konieczne. Poniższe zrzuty ekranu pokazują się, gdy przekazujesz adres IP hosta platformy Azure. Jeśli używasz własnego serwera DNS, Zastąp ciąg adresem IP serwera DNS.

    ![Usługi przesyłania dalej warunkowego w Menedżerze DNS](./media/private-endpoints/dns-manager.png)

    ![Nowa usługa przesyłania dalej warunkowego](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Tworzenie kopii zapasowych i przywracanie za poorednictwem agenta MARS

W przypadku korzystania z agenta MARS do tworzenia kopii zapasowych zasobów lokalnych upewnij się, że sieć lokalna (zawierająca zasoby, których kopia zapasowa ma zostać utworzona), jest połączona z siecią wirtualną platformy Azure, która zawiera prywatny punkt końcowy magazynu, dzięki czemu można z niej korzystać. Następnie można nadal zainstalować agenta MARS i skonfigurować kopię zapasową zgodnie z opisem w tym miejscu. Należy jednak upewnić się, że cała komunikacja w ramach kopii zapasowej odbywa się tylko za pomocą sieci równorzędnej.

Jeśli jednak usuniesz prywatne punkty końcowe dla magazynu po zarejestrowaniu agenta MARS, należy ponownie zarejestrować kontener w magazynie. Nie musisz zatrzymać ochrony dla nich.

## <a name="deleting-private-endpoints"></a>Usuwanie prywatnych punktów końcowych

Zapoznaj się z [tą sekcją](https://docs.microsoft.com/rest/api/virtualnetwork/privateendpoints/delete) , aby dowiedzieć się, jak usunąć prywatne punkty końcowe.

## <a name="additional-topics"></a>Tematy dodatkowe

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Tworzenie magazynu Recovery Services przy użyciu klienta Azure Resource Manager

Można utworzyć magazyn Recovery Services i włączyć jego tożsamość zarządzaną (wymaganie tożsamości zarządzanej jest wymagane, co później będzie można zobaczyć) przy użyciu klienta Azure Resource Manager. Przykład do wykonania tej czynności jest udostępniony poniżej:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Plik JSON powinien zawierać następującą zawartość:

Plik JSON żądania:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Plik JSON odpowiedzi:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Magazyn utworzony w tym przykładzie za pomocą klienta Azure Resource Manager został już utworzony przy użyciu tożsamości zarządzanej przypisanej do systemu.

### <a name="managing-permissions-on-resource-groups"></a>Zarządzanie uprawnieniami w grupach zasobów

Zarządzana tożsamość magazynu musi mieć następujące uprawnienia w grupie zasobów i sieci wirtualnej, w której zostaną utworzone prywatne punkty końcowe:

- `Microsoft.Network/privateEndpoints/*` Jest to wymagane do przeprowadzenia CRUD na prywatnych punktach końcowych w grupie zasobów. Powinien być przypisany do grupy zasobów.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Jest to wymagane w sieci wirtualnej, w której prywatny adres IP jest dołączany do prywatnego punktu końcowego.
- `Microsoft.Network/networkInterfaces/read` Jest to wymagane w grupie zasobów, aby uzyskać interfejs sieciowy utworzony dla prywatnego punktu końcowego.
- Rola współautora strefy Prywatna strefa DNS ta rola już istnieje i może służyć do udostępniania `Microsoft.Network/privateDnsZones/A/*` i `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` uprawnień.

Aby utworzyć role z wymaganymi uprawnieniami, można użyć jednej z następujących metod:

#### <a name="create-roles-and-permissions-manually"></a>Ręczne tworzenie ról i uprawnień

Utwórz następujące pliki JSON i użyj polecenia programu PowerShell na końcu sekcji, aby utworzyć role:

PrivateEndpointContributorRoleDef.jsna

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsna

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsna

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Używanie skryptu

1. Uruchom **Cloud Shell** w Azure Portal i wybierz polecenie **Przekaż plik** w oknie programu PowerShell.

    ![Wybierz opcję Przekaż plik w oknie programu PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Przekaż następujący skrypt: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Przejdź do folderu macierzystego (na przykład: `cd /home/user` )

1. Uruchom poniższy skrypt:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Są to następujące parametry:

    - **subskrypcja**: * * Identyfikator subskrypcji zawierający grupę zasobów, w której ma zostać utworzony prywatny punkt końcowy dla magazynu, oraz podsieć, w której zostanie dołączony prywatny punkt końcowy magazynu

    - **vaultPEResourceGroup**: Grupa zasobów, w której zostanie utworzony prywatny punkt końcowy dla magazynu

    - **vaultPESubnetResourceGroup**: Grupa zasobów podsieci, do której zostanie dołączony prywatny punkt końcowy

    - **vaultMsiName**: nazwa pliku MSI magazynu, który jest taki sam jak **magazynname**

1. Ukończ uwierzytelnianie, a skrypt przyjmie kontekst podanej subskrypcji powyżej. Jeśli brakuje ich w dzierżawie i przypiszesz role do pliku MSI magazynu, zostaną utworzone odpowiednie role.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Tworzenie prywatnych punktów końcowych przy użyciu Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Wstępnie zatwierdzone prywatne punkty końcowe

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Ręczne zatwierdzanie prywatnych punktów końcowych przy użyciu klienta Azure Resource Manager

1. Użyj elementu **getmagazyn** , aby uzyskać identyfikator prywatnego połączenia punktu końcowego dla prywatnego punktu końcowego.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Spowoduje to zwrócenie identyfikatora połączenia prywatnego punktu końcowego. Nazwę połączenia można pobrać przy użyciu pierwszej części identyfikatora połączenia w następujący sposób:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Pobierz **Identyfikator prywatnego połączenia punktu końcowego** (oraz **nazwę prywatnego punktu końcowego**, gdziekolwiek jest to wymagane) z odpowiedzi i Zastąp ją w poniższym kodzie JSON i Azure Resource Manager identyfikatorze URI, a następnie spróbuj zmienić stan na "zatwierdzone/odrzucone/odłączone", jak pokazano w poniższym przykładzie:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    KODU

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

PYTANIE: Czy można utworzyć prywatny punkt końcowy dla istniejącego magazynu kopii zapasowych?<br>
A. Nie, prywatne punkty końcowe można utworzyć tylko dla nowych magazynów kopii zapasowych. W związku z tym magazyn nie może mieć żadnych elementów chronionych. W rzeczywistości żadna próba ochrony jakichkolwiek elementów w magazynie nie może zostać wykonana przed utworzeniem prywatnych punktów końcowych.

PYTANIE: Podjęto próbę włączenia ochrony elementu do mojego magazynu, ale jego awaria nie powiodła się, a magazyn nadal nie zawiera żadnych elementów chronionych. Czy mogę utworzyć prywatne punkty końcowe dla tego magazynu?<br>
A. Nie. magazyn nie może mieć żadnych prób ochrony jakichkolwiek elementów w przeszłości.

PYTANIE: Mam magazyn, który używa prywatnych punktów końcowych do tworzenia kopii zapasowych i przywracania. Czy można później dodawać lub usuwać prywatne punkty końcowe dla tego magazynu, nawet jeśli mam do niego chronione elementy kopii zapasowej?<br>
A. Tak. Jeśli utworzono już prywatne punkty końcowe dla magazynu i chronionych kopii zapasowych, możesz później dodać lub usunąć prywatne punkty końcowe zgodnie z potrzebami.

PYTANIE: Czy można użyć prywatnego punktu końcowego dla Azure Backup również dla Azure Site Recovery?<br>
A. Nie, prywatny punkt końcowy dla kopii zapasowej może być używany tylko dla Azure Backup. Należy utworzyć nowy prywatny punkt końcowy dla Azure Site Recovery, jeśli jest on obsługiwany przez usługę.

PYTANIE: Pominięto jeden z kroków tego artykułu i zaszło do ochrony mojego źródła danych. Czy nadal mogę używać prywatnych punktów końcowych?<br>
A. Niewykonanie czynności opisanych w artykule i kontynuowanie ochrony elementów może prowadzić do tego, że magazyn nie będzie mógł używać prywatnych punktów końcowych. Dlatego zalecamy odwoływanie się do tej listy kontrolnej przed kontynuowaniem ochrony elementów.

PYTANIE: Czy mogę użyć własnego serwera DNS zamiast strefy prywatnej DNS platformy Azure lub zintegrowanej prywatnej strefy DNS?<br>
A. Tak, możesz użyć własnych serwerów DNS. Upewnij się jednak, że wszystkie wymagane rekordy DNS są dodawane zgodnie z opisem w tej sekcji.

PYTANIE: Czy po wykonaniu procesu w tym artykule muszę wykonać wszelkie dodatkowe czynności na serwerze?<br>
A. Po przeprowadzeniu procedury przedstawionej w tym artykule nie trzeba wykonywać dodatkowych czynności w celu używania prywatnych punktów końcowych w celu tworzenia kopii zapasowych i przywracania.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o wszystkich [funkcjach zabezpieczeń w Azure Backup](security-overview.md).
