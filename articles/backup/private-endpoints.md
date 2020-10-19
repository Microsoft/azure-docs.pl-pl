---
title: Prywatne punkty końcowe
description: Zapoznaj się z procesem tworzenia prywatnych punktów końcowych dla Azure Backup i scenariuszy, w których używanie prywatnych punktów końcowych pomaga zachować bezpieczeństwo zasobów.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0ca4e7a83e18ac72e25131d320737ce9578b1cf3
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172247"
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
- Sieci wirtualne z zasadami sieci nie są obsługiwane dla prywatnych punktów końcowych. Przed kontynuowaniem należy wyłączyć zasady sieci.
- Należy ponownie zarejestrować dostawcę zasobów Recovery Services z subskrypcją, jeśli został on zarejestrowany przed 1 2020 maja. Aby ponownie zarejestrować dostawcę, przejdź do subskrypcji w Azure Portal, przejdź do pozycji **dostawca zasobów** na lewym pasku nawigacyjnym, a następnie wybierz pozycję **Microsoft. RecoveryServices** i wybierz pozycję **zarejestruj ponownie**.

## <a name="recommended-and-supported-scenarios"></a>Zalecane i obsługiwane scenariusze

Gdy prywatne punkty końcowe są włączone dla magazynu, są używane do tworzenia kopii zapasowych i przywracania danych SQL i SAP HANA obciążeń na maszynach wirtualnych platformy Azure i w ramach kopii zapasowej agenta MARS. Magazyn można również używać do tworzenia kopii zapasowych innych obciążeń (nie wymagają jednak prywatnych punktów końcowych). Oprócz tworzenia kopii zapasowych obciążeń SQL i SAP HANA i tworzenia kopii zapasowej przy użyciu agenta MARS, prywatne punkty końcowe są również używane do odzyskiwania plików dla kopii zapasowej maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz następującą tabelę:

| Tworzenie kopii zapasowych obciążeń na maszynie wirtualnej platformy Azure (SQL, SAP HANA), kopia zapasowa przy użyciu agenta MARS | Zaleca się używanie prywatnych punktów końcowych, aby umożliwić tworzenie kopii zapasowych i przywracanie bez konieczności zezwalania na listę adresów IP/nazw FQDN dla Azure Backup lub Azure Storage z sieci wirtualnych. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Kopia zapasowa maszyny wirtualnej platformy Azure**                                         | Kopia zapasowa maszyny wirtualnej nie wymaga zezwolenia na dostęp do żadnych adresów IP ani nazw FQDN. Dlatego nie wymaga prywatnych punktów końcowych do tworzenia kopii zapasowych i przywracania dysków.  <br><br>   Jednak odzyskiwanie plików z magazynu zawierającego prywatne punkty końcowe będzie ograniczone do sieci wirtualnych, które zawierają prywatny punkt końcowy dla magazynu. <br><br>    W przypadku korzystania z dysków niezarządzanych ACL'ed upewnij się, że konto magazynu zawierające dyski umożliwia dostęp do **zaufanych usług firmy Microsoft** , jeśli jest to ACL'ed. |
| **Azure Files kopia zapasowa**                                      | Kopie zapasowe Azure Files są przechowywane na lokalnym koncie magazynu. Dlatego nie wymaga prywatnych punktów końcowych do tworzenia kopii zapasowych i przywracania. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Tworzenie i używanie prywatnych punktów końcowych do tworzenia kopii zapasowych

W tej sekcji omówiono kroki związane z tworzeniem i używaniem prywatnych punktów końcowych w celu Azure Backup wewnątrz sieci wirtualnych.

>[!IMPORTANT]
> Zdecydowanie zaleca się wykonanie kroków w tej samej kolejności, jak wspomniano w tym dokumencie. Niewykonanie tej czynności może spowodować, że magazyn jest renderowany niezgodny z prywatnymi punktami końcowymi i wymaga ponownego uruchomienia procesu z nowym magazynem.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Zapoznaj się z [tą sekcją](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) , aby dowiedzieć się, jak utworzyć magazyn przy użyciu klienta Azure Resource Manager. Spowoduje to utworzenie magazynu z już włączoną zarządzaną tożsamością. Więcej informacji na temat Recovery Services magazynów [znajdziesz tutaj](./backup-azure-recovery-services-vault-overview.md).

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
- Grupa zasobów zawierająca Prywatna strefa DNS strefy, jak opisano szczegółowo w [tym miejscu](#creating-private-endpoints-for-backup)

Zalecamy przyznanie roli **współautor** dla tych trzech grup zasobów do magazynu (tożsamość zarządzana). W poniższych krokach opisano, jak to zrobić dla określonej grupy zasobów (należy to zrobić dla każdej z trzech grup zasobów):

1. Przejdź do grupy zasobów i przejdź do pozycji **Access Control (IAM)** na pasku po lewej stronie.
1. Raz w **Access Control**przejdź do obszaru **Dodawanie przypisania roli**.

    ![Dodaj przypisanie roli](./media/private-endpoints/add-role-assignment.png)

1. W okienku **Dodaj przypisanie roli** wybierz **współautor** jako **rolę**i Użyj **nazwy** magazynu jako **podmiotu zabezpieczeń**. Wybierz swój magazyn i wybierz pozycję **Zapisz** po zakończeniu.

    ![Wybieranie roli i podmiotu zabezpieczeń](./media/private-endpoints/choose-role-and-principal.png)

Aby zarządzać uprawnieniami na bardziej szczegółowym poziomie, zobacz [Ręczne tworzenie ról i uprawnień](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Tworzenie i zatwierdzanie prywatnych punktów końcowych dla Azure Backup

### <a name="creating-private-endpoints-for-backup"></a>Tworzenie prywatnych punktów końcowych dla kopii zapasowej

W tej sekcji opisano proces tworzenia prywatnego punktu końcowego dla magazynu.

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **link prywatny**. Spowoduje to przejście do **prywatnego centrum linków**.

    ![Wyszukaj link prywatny](./media/private-endpoints/search-for-private-link.png)

1. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **prywatne punkty końcowe**. W okienku **prywatne punkty końcowe** wybierz pozycję **+ Dodaj** , aby rozpocząć tworzenie prywatnego punktu końcowego dla magazynu.

    ![Dodaj prywatny punkt końcowy w prywatnym centrum linków](./media/private-endpoints/add-private-endpoint.png)

1. W procesie **tworzenia prywatnego punktu końcowego** należy określić szczegóły dotyczące tworzenia połączenia prywatnego punktu końcowego.

    1. **Podstawowe**: Podaj podstawowe informacje o prywatnych punktach końcowych. Region powinien być taki sam jak magazyn i zasób.

        ![Wypełnij podstawowe szczegóły](./media/private-endpoints/basic-details.png)

    1. **Zasób**: na tej karcie należy wspomnieć zasób PaaS, dla którego chcesz utworzyć połączenie. Wybierz pozycję **Microsoft. RecoveryServices/magazyny** z typu zasobu dla żądanej subskrypcji. Po zakończeniu wybierz nazwę magazynu Recovery Services jako **zasób** i **AzureBackup** jako **docelowy zasób podrzędny**.

        ![Wypełnij kartę zasobów](./media/private-endpoints/resource-tab.png)

    1. **Konfiguracja**: w obszarze Konfiguracja Określ sieć wirtualną i podsieć, w której ma zostać utworzony prywatny punkt końcowy. Będzie to sieć wirtualna, w której znajduje się maszyna wirtualna. Możesz wybrać opcję **integracji prywatnego punktu końcowego** z prywatną strefą DNS. Alternatywnie można również użyć niestandardowego serwera DNS lub utworzyć prywatną strefę DNS.

        ![Wypełnij kartę Konfiguracja](./media/private-endpoints/configuration-tab.png)

        Zapoznaj się z [tą sekcją](#dns-changes-for-custom-dns-servers) , jeśli chcesz używać niestandardowych serwerów DNS zamiast integrować je z usługą Azure prywatna strefa DNS Zones.  

    1. Opcjonalnie możesz dodać **Tagi** dla prywatnego punktu końcowego.

    1. Kontynuuj **przeglądanie i tworzenie** po zakończeniu wprowadzania szczegółów. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.

## <a name="approving-private-endpoints"></a>Zatwierdzanie prywatnych punktów końcowych

Jeśli użytkownik tworzący prywatny punkt końcowy jest również właścicielem magazynu Recovery Services, prywatny punkt końcowy utworzony powyżej zostanie zaakceptowany. W przeciwnym razie właściciel magazynu musi zatwierdzić prywatny punkt końcowy, zanim będzie mógł go używać. W tej sekcji omówiono ręczne zatwierdzanie prywatnych punktów końcowych za pomocą Azure Portal.

Zobacz [Ręczne zatwierdzanie prywatnych punktów końcowych przy użyciu klienta Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) , aby użyć klienta Azure Resource Manager do zatwierdzania prywatnych punktów końcowych.

1. W magazynie Recovery Services przejdź do **połączeń prywatnych punktów końcowych** na pasku po lewej stronie.
1. Wybierz połączenie prywatnego punktu końcowego, które chcesz zatwierdzić.
1. Wybierz pozycję **Zatwierdź** na górnym pasku. Możesz również wybrać opcję **Odrzuć** lub **Usuń** , jeśli chcesz odrzucić lub usunąć połączenie z punktem końcowym.

    ![Zatwierdzanie prywatnych punktów końcowych](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Używanie prywatnych punktów końcowych do tworzenia kopii zapasowych

Gdy prywatne punkty końcowe utworzone dla magazynu w sieci wirtualnej zostaną zatwierdzone, możesz zacząć ich używać do wykonywania kopii zapasowych i przywracania.

>[!IMPORTANT]
>Przed kontynuowaniem upewnij się, że wszystkie kroki wymienione powyżej zostały wykonane pomyślnie. Aby podsumowanie, należy wykonać kroki opisane na poniższej liście kontrolnej:
>
>1. Utworzono magazyn Recovery Services (nowy)
>1. Włączono magazyn do korzystania z tożsamości zarządzanej przypisanej przez system
>1. Przypisano odpowiednie uprawnienia do zarządzanej tożsamości magazynu
>1. Utworzono prywatny punkt końcowy dla magazynu
>1. Zatwierdzono prywatny punkt końcowy (jeśli nie został on zaakceptowany)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Tworzenie kopii zapasowych i przywracanie obciążeń na maszynie wirtualnej platformy Azure (SQL, SAP HANA)

Po utworzeniu i zatwierdzeniu prywatnego punktu końcowego nie są wymagane żadne dodatkowe zmiany po stronie klienta, aby użyć prywatnego punktu końcowego. Cała komunikacja i transfer danych z zabezpieczonej sieci do magazynu będzie odbywać się za pośrednictwem prywatnego punktu końcowego.
Jeśli jednak usuniesz prywatne punkty końcowe dla magazynu po zarejestrowaniu serwera (SQL/SAP HANA), należy ponownie zarejestrować kontener z magazynem. Nie musisz zatrzymać ochrony dla nich.

### <a name="backup-and-restore-through-mars-agent"></a>Tworzenie kopii zapasowych i przywracanie za poorednictwem agenta MARS

W przypadku korzystania z agenta MARS do tworzenia kopii zapasowych zasobów lokalnych upewnij się, że sieć lokalna (zawierająca zasoby, których kopia zapasowa ma zostać utworzona), jest połączona z siecią wirtualną platformy Azure, która zawiera prywatny punkt końcowy magazynu, dzięki czemu można z niej korzystać. Następnie można nadal zainstalować agenta MARS i skonfigurować kopię zapasową zgodnie z opisem w tym miejscu. Należy jednak upewnić się, że cała komunikacja dla kopii zapasowej odbywa się tylko za pomocą sieci równorzędnej.

Jeśli jednak usuniesz prywatne punkty końcowe dla magazynu po zarejestrowaniu do niego agenta MARS, należy ponownie zarejestrować kontener z magazynem. Nie musisz zatrzymać ochrony dla nich.

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

### <a name="dns-changes-for-custom-dns-servers"></a>Zmiany DNS dla niestandardowych serwerów DNS

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Tworzenie stref DNS dla niestandardowych serwerów DNS

Należy utworzyć trzy prywatne strefy DNS i połączyć je z siecią wirtualną.

| **Strefa**                                                     | **Usługa** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Obiekt blob        |
| `privatelink.queue.core.windows.net`                           | Kolejka       |

>[!NOTE]
>W powyższym tekście *geograficznym* odwołuje się do kodu regionu. Na przykład *wcus* i *ne* odpowiednio do regionu zachodnio-środkowe stany USA i Europa Północna.

Zapoznaj się z [tą listą](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) dla kodów regionów. Poniżej znajdują się linki do konwencji nazewnictwa adresów URL w regionach narodowych:

- [Chiny](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Niemcy](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Dodawanie rekordów DNS dla niestandardowych serwerów DNS

Wymaga to wprowadzenia wpisów dla każdej nazwy FQDN w prywatnym punkcie końcowym do strefy Prywatna strefa DNS.

Należy zauważyć, że będziemy używać prywatnych punktów końcowych utworzonych na potrzeby tworzenia kopii zapasowych, obiektów blob i usługa kolejki.

- Prywatny punkt końcowy magazynu używa nazwy określonej podczas tworzenia prywatnego punktu końcowego
- Prywatne punkty końcowe usług BLOB i Queue są poprzedzone nazwą tego samego magazynu.

Na przykład na poniższej ilustracji przedstawiono trzy prywatne punkty końcowe utworzone dla prywatnego połączenia punktu końcowego o nazwie *pee2epe*:

![Trzy prywatne punkty końcowe dla połączenia prywatnego punktu końcowego](./media/private-endpoints/three-private-endpoints.png)

Strefa DNS dla usługi kopii zapasowej ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Przejdź do prywatnego punktu końcowego, aby utworzyć kopię zapasową w **prywatnym centrum linków**. Na stronie Przegląd znajduje się lista nazw FQDN i prywatnych adresów IP dla prywatnego punktu końcowego.

1. Dodaj jeden wpis dla każdej nazwy FQDN i prywatnego adresu IP jako rekord typu.

    ![Dodaj wpis dla każdej nazwy FQDN i prywatnego adresu IP](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Strefa DNS dla Blob service ( `privatelink.blob.core.windows.net` ):

1. Przejdź do prywatnego punktu końcowego dla obiektu BLOB w **prywatnym centrum linków**. Na stronie Przegląd znajduje się lista nazw FQDN i prywatnych adresów IP dla prywatnego punktu końcowego.

1. Dodaj wpis dla nazwy FQDN i prywatnego adresu IP jako rekord typu.

    ![Dodaj wpis dla nazwy FQDN i prywatnego adresu IP jako rekord typu dla Blob service](./media/private-endpoints/add-type-a-record-for-blob.png)

Strefa DNS dla usługa kolejki ( `privatelink.queue.core.windows.net` ):

1. Przejdź do prywatnego punktu końcowego dla kolejki w **prywatnym centrum linków**. Na stronie Przegląd znajduje się lista nazw FQDN i prywatnych adresów IP dla prywatnego punktu końcowego.

1. Dodaj wpis dla nazwy FQDN i prywatnego adresu IP jako rekord typu.

    ![Dodaj wpis dla nazwy FQDN i prywatnego adresu IP jako rekord typu dla usługa kolejki](./media/private-endpoints/add-type-a-record-for-queue.png)

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

- Przeczytaj o wszystkich [funkcjach zabezpieczeń w programie Azure Backup](security-overview.md)