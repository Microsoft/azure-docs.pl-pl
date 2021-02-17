---
title: Często zadawane pytania dotyczące usługi Azure Virtual Machine Scale Sets
description: Uzyskaj odpowiedzi na najczęściej zadawane pytania dotyczące zestawów skalowania maszyn wirtualnych na platformie Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: faq
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 3bc259f9ee6cb1e6fd927af82a1740403d3ae7d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587950"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Często zadawane pytania dotyczące usługi Azure Virtual Machine Scale Sets

Uzyskaj odpowiedzi na często zadawane pytania dotyczące zestawów skalowania maszyn wirtualnych na platformie Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Najpopularniejsze często zadawane pytania dotyczące zestawów skalowania

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Ile maszyn wirtualnych może się znajdować w zestawie skalowania?

Zestaw skalowania może zawierać od 0 do 1 000 maszyn wirtualnych opartych na obrazach platformy lub od 0 do 600 maszyn wirtualnych opartych na obrazach niestandardowych.

### <a name="are-data-disks-supported-within-scale-sets"></a>Czy zestawy skalowania obsługują dyski danych?

Tak. Zestaw skalowania może definiować konfigurację dołączonych dysków danych, która jest stosowana do wszystkich maszyn wirtualnych w zestawie. Aby uzyskać więcej informacji, zobacz [Zestawy skalowania na platformie Azure i dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md). Oto przykłady innych opcji magazynowania danych:

* Pliki platformy Azure (dyski udostępnione za pośrednictwem protokołu SMB)
* Dysk systemu operacyjnego
* Dysk tymczasowy (lokalny, bez kopii zapasowej w usłudze Azure Storage)
* Usługa danych platformy Azure (na przykład tabele platformy Azure, obiekty blob platformy Azure)
* Zewnętrzne usługi danych (na przykład zdalna baza danych)

### <a name="which-azure-regions-support-scale-sets"></a>Które regiony świadczenia usługi Azure obsługują zestawy skalowania?

Wszystkie regiony obsługują zestawy skalowania.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Jak utworzyć zestaw skalowania za pomocą obrazu niestandardowego?

Utwórz i Przechwyć obraz maszyny wirtualnej, a następnie użyj go jako źródła zestawu skalowania. Aby zapoznać się z samouczkiem dotyczącym tworzenia niestandardowego obrazu maszyny wirtualnej i korzystania z niego, można użyć [interfejsu wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md) lub [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Jeśli zmniejszę pojemność zestawu skalowania z 20 do 15, które maszyny wirtualne zostaną usunięte?

Domyślnie maszyny wirtualne są usuwane z zestawu skalowania równomiernie w różnych strefach dostępności (Jeśli zestaw skalowania jest wdrażany w konfiguracji stref) i domen błędów w celu maksymalizacji dostępności. Najpierw są usuwane maszyny wirtualne o najwyższym identyfikatorze.

Kolejność usuwania maszyn wirtualnych można zmienić, określając [zasady skalowania](virtual-machine-scale-sets-scale-in-policy.md) dla zestawu skalowania.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Co się stanie, jeśli następnie zwiększę pojemność z 15 do 18?

Zwiększenie pojemności do 18 spowoduje utworzenie 3 nowych maszyn wirtualnych. Za każdym razem następuje zwiększenie identyfikatora wystąpienia maszyny wirtualnej względem poprzedniej najwyższej wartości (na przykład 20, 21, 22). Maszyny wirtualne są zrównoważone w domenach błędów.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Czy mogę wymusić sekwencję wykonywania w przypadku korzystania z wielu rozszerzeń w zestawie skalowania?

Tak, można użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md)zestawu skalowania.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Czy zestawy skalowania współdziałają z zestawami dostępności platformy Azure?

Zestaw skalowania Regionalnego (bez stref) używa *grup umieszczania*, które działają jako niejawny zestaw dostępności z pięcioma domenami błędów i pięcioma domenami aktualizacji. Zestawy skalowania z ponad 100 maszyn wirtualnych zajmują wiele grup umieszczania. Aby uzyskać więcej informacji na temat grup umieszczania, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). Zestaw dostępności maszyn wirtualnych może znajdować się w tej samej sieci wirtualnej co zestaw skalowania maszyn wirtualnych. Typowa konfiguracja polega na umieszczeniu maszyn wirtualnych węzła kontrolnego (często wymagających unikatowej konfiguracji) w zestawie dostępności, a węzłów danych w zestawie skalowania.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Czy zestawy skalowania pracują z strefami dostępności platformy Azure?

Tak! Aby uzyskać więcej informacji, zobacz [dokument o strefie zestawu skalowania](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatyczne skalowanie

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jakie są najlepsze rozwiązania dotyczące automatycznego skalowania na platformie Azure?

Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi automatycznego skalowania, zobacz [najlepsze rozwiązania dotyczące skalowania maszyn wirtualnych](../azure-monitor/autoscale/autoscale-best-practices.md).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Gdzie można znaleźć nazwy metryk skalowania automatycznego, które korzysta z metryk opartych na hoście?

Aby uzyskać nazwy metryk skalowania automatycznego, które korzysta z metryk opartych na hoście, zobacz temat [obsługiwane metryki z Azure monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Czy istnieją przykłady skalowania automatycznego na podstawie Azure Service Bus tematu i długości kolejki?

Tak. Aby zapoznać się z przykładami skalowania automatycznego na podstawie Azure Service Bus tematu i długości kolejki, zobacz [Azure monitor automatyczne skalowanie typowych metryk](../azure-monitor/autoscale/autoscale-common-metrics.md).

W przypadku kolejki Service Bus Użyj następującego kodu JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

W przypadku kolejki magazynu Użyj następującego kodu JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Zastąp przykładowe wartości identyfikatorami Uniform Resource Identifier (URI) zasobów.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Czy należy automatyczne skalowanie przy użyciu metryk opartych na hoście czy rozszerzenia diagnostyki?

Można utworzyć ustawienie automatycznego skalowania na maszynie wirtualnej, aby użyć metryk na poziomie hosta lub metryk opartych na systemie operacyjnym gościa.

Aby uzyskać listę obsługiwanych metryk, zobacz [Azure monitor skalowanie często spotykane](../azure-monitor/autoscale/autoscale-common-metrics.md).

Aby uzyskać pełny przykład dla zestawów skalowania maszyn wirtualnych, zobacz [Zaawansowane Konfigurowanie automatycznego skalowania za pomocą szablonów Menedżer zasobów dla zestawów skalowania maszyn wirtualnych](../azure-monitor/autoscale/autoscale-virtual-machine-scale-sets.md).

Przykład wykorzystuje metrykę procesora CPU na poziomie hosta i metrykę zliczania komunikatów.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Jak mogę ustawić reguły alertów w zestawie skalowania maszyn wirtualnych?

Możesz tworzyć alerty dotyczące metryk dla zestawów skalowania maszyn wirtualnych za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz Azure Monitor samouczków szybki start dla [programu PowerShell](../azure-monitor/powershell-samples.md#create-metric-alerts) i [Azure monitor Międzyplatformowe przykłady interfejsu wiersza polecenia](../azure-monitor/cli-samples.md#work-with-alerts).

Element targetresourceid zestawu skalowania maszyn wirtualnych wygląda następująco:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Możesz wybrać dowolny licznik wydajności maszyny wirtualnej jako metrykę, dla której ma zostać ustawiony alert. Aby uzyskać więcej informacji, zobacz [metryki systemu operacyjnego gościa dla maszyn wirtualnych z systemem Windows opartych na Menedżer zasobów](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) i [metryki systemu operacyjnego gościa dla maszyn wirtualnych systemu Linux](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-linux-vms) w artykule [Azure monitor częsty skalowanie typowych metryk](../azure-monitor/autoscale/autoscale-common-metrics.md)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak mogę skonfigurować automatyczne skalowanie w zestawie skalowania maszyn wirtualnych za pomocą programu PowerShell?

Aby skonfigurować Skalowanie automatyczne na podstawie zestawu skalowania maszyn wirtualnych przy użyciu programu PowerShell, zobacz sekcję [Automatyczna skalowanie zestawu skalowania maszyn wirtualnych](tutorial-autoscale-powershell.md). Możesz również skonfigurować Skalowanie automatyczne za pomocą [interfejsu wiersza polecenia platformy Azure](tutorial-autoscale-cli.md) i [szablonów platformy Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Jeśli zatrzymano (cofnięto przydział) maszyny wirtualnej, czy maszyna wirtualna została uruchomiona w ramach operacji automatycznego skalowania?

Nie. Jeśli reguły automatycznego skalowania wymagają dodatkowych wystąpień maszyn wirtualnych w ramach zestawu skalowania, tworzone jest nowe wystąpienie maszyny wirtualnej. Wystąpienia maszyn wirtualnych, które są zatrzymane (bez przydziału), nie zostały uruchomione jako część zdarzenia skalowania automatycznego. Jednak te zatrzymane (cofnięte alokacje) maszyny wirtualne mogą zostać usunięte jako część zdarzenia skalowania automatycznego, które skaluje się w liczbie wystąpień, tak samo jak każde wystąpienie maszyny wirtualnej może zostać usunięte na podstawie kolejności identyfikatora wystąpienia maszyny wirtualnej.



## <a name="certificates"></a>Certyfikaty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Jak mogę bezpiecznie dostarczyć certyfikat do maszyny wirtualnej?

Aby bezpiecznie dostarczyć certyfikat do maszyny wirtualnej, możesz zainstalować certyfikat klienta bezpośrednio w magazynie certyfikatów systemu Windows z magazynu kluczy klienta.

Użyj następującego kodu JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Kod obsługuje systemy Windows i Linux.

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie zestawu skalowania maszyn wirtualnych](/rest/api/compute/virtualmachinescalesets/createorupdate).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Jak mogę używać certyfikatów z podpisem własnym dla klastrów usługi Azure Service Fabric?
Aby zapoznać się z najnowszym przykładem, użyj następującej instrukcji interfejsu wiersza polecenia platformy Azure w usłudze Azure Shell. Przeczytaj przykładową dokumentację modułu interfejsu wiersza polecenia usługi Service Fabric, która zostanie wydrukowany w stdout:

```azurecli
az sf cluster create -h
```

Certyfikatów z podpisem własnym nie można używać w przypadku dystrybuowanej relacji zaufania udostępnianej przez urząd certyfikacji i nie należy jej używać w żadnym klastrze Service Fabric przeznaczonym do hostowania rozwiązań produkcyjnych przedsiębiorstwa. Aby uzyskać dodatkowe Service Fabric wskazówki dotyczące zabezpieczeń, zapoznaj się z [najlepszymi rozwiązaniami](../security/fundamentals/service-fabric-best-practices.md) dotyczącymi zabezpieczeń Azure Service Fabric i [Service Fabric scenariuszami zabezpieczeń klastra](../service-fabric/service-fabric-cluster-security.md).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Czy można określić parę kluczy SSH, która ma być używana na potrzeby uwierzytelniania SSH z zestawem skalowania maszyn wirtualnych z systemem Linux na podstawie szablonu Menedżer zasobów?

Tak. Interfejs API REST dla **osProfile** jest podobny do standardowego interfejsu API REST maszyny wirtualnej.

Dołącz **osProfile** do szablonu:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Ten blok JSON jest używany w [tym szablonie szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie zestawu skalowania maszyn wirtualnych](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak mogę usunąć przestarzałe certyfikaty?

Aby usunąć przestarzałe certyfikaty, usuń stary certyfikat z listy certyfikaty magazynu. Na liście pozostaw wszystkie certyfikaty, które mają pozostać na komputerze. Nie spowoduje to usunięcia certyfikatu ze wszystkich maszyn wirtualnych. Nie dodaje również certyfikatu do nowych maszyn wirtualnych, które są tworzone w zestawie skalowania maszyn wirtualnych.

Aby usunąć certyfikat z istniejących maszyn wirtualnych, Użyj niestandardowego rozszerzenia skryptu, aby ręcznie usunąć certyfikaty z magazynu certyfikatów.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Jak mogę wstrzyknąć istniejący klucz publiczny SSH do warstwy SSH zestawu skalowania maszyn wirtualnych podczas aprowizacji?

Jeśli są udostępniane maszyny wirtualne tylko z publicznym kluczem SSH, nie trzeba umieszczać kluczy publicznych w Key Vault. Klucze publiczne nie są tajne.

Klucze publiczne SSH można podawać w postaci zwykłego tekstu podczas tworzenia maszyny wirtualnej z systemem Linux:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

Nazwa elementu linuxConfiguration | Wymagany | Typ | Opis
--- | --- | --- | ---
SSH | Nie | Kolekcja | Określa konfigurację klucza SSH dla systemu operacyjnego Linux
path | Tak | Ciąg | Określa ścieżkę pliku systemu Linux, w której należy zlokalizować klucze SSH lub certyfikat
Dane dotyczące danych | Tak | Ciąg | Określa klucz publiczny SSH szyfrowany algorytmem Base64

Aby zapoznać się z przykładem, zobacz [szablon 101-VM-Sshkey GitHub — szybki start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Gdy uruchamiam `Update-AzVmss` się po dodaniu więcej niż jednego certyfikatu z tego samego magazynu kluczy, zobaczysz następujący komunikat:

>Polecenie Update-AzVmss: wpis tajny listy zawiera powtórzone wystąpienia/subscriptions/ \<my-subscription-id> /resourceGroups/Internal-RG-dev/Providers/Microsoft.KeyVault/Vaults/Internal-keyvault-dev, które są niedozwolone.

Taka sytuacja może wystąpić, jeśli spróbujesz ponownie dodać ten sam magazyn zamiast korzystać z nowego certyfikatu magazynu dla istniejącego magazynu źródłowego. `Add-AzVmssSecret`Polecenie nie działa prawidłowo w przypadku dodawania dodatkowych wpisów tajnych.

Aby dodać więcej wpisów tajnych z tego samego magazynu kluczy, zaktualizuj listę $vmss. Properties. osProfile. Secret [0]. vaultCertificates.

Aby uzyskać oczekiwaną strukturę danych wejściowych, zobacz [Tworzenie lub aktualizowanie zestawu maszyn wirtualnych](/rest/api/compute/virtualmachinescalesets/createorupdate).

Znajdź wpis tajny w obiekcie zestawu skalowania maszyn wirtualnych, który znajduje się w magazynie kluczy. Następnie Dodaj odwołanie do certyfikatu (adres URL i nazwa magazynu wpisów tajnych) do listy skojarzonej z magazynem.

> [!NOTE]
> Obecnie nie można usunąć certyfikatów z maszyn wirtualnych przy użyciu interfejsu API zestawu skalowania maszyn wirtualnych.
>

Nowe maszyny wirtualne nie będą miały starego certyfikatu. Jednak maszyny wirtualne, które mają już wdrożony certyfikat, będą mieć stary certyfikat.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Czy można wypychanie certyfikatów do zestawu skalowania maszyn wirtualnych bez podawania hasła, gdy certyfikat znajduje się w magazynie wpisów tajnych?

Nie jest konieczne wprowadzanie twardych haseł w skryptach. Można dynamicznie pobierać hasła z uprawnieniami używanymi do uruchamiania skryptu wdrażania. Jeśli masz skrypt, który przenosi certyfikat z magazynu kluczy magazynu tajnego, polecenie magazynu tajnego zapisuje `get certificate` również hasło do pliku PFX.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak działa Właściwość Secret elementu virtualMachineProfile. osProfile dla zestawu skalowania maszyn wirtualnych? Dlaczego muszę mieć wartość sourceVault, gdy muszę określić bezwzględny identyfikator URI dla certyfikatu za pomocą właściwości certificateUrl?

Odwołanie do certyfikatu Windows Remote Management (WinRM) musi być obecne we właściwości Secret profilu systemu operacyjnego.

Celem wskazania magazynu źródłowego jest wymuszenie zasad listy kontroli dostępu (ACL), które istnieją w modelu usług w chmurze platformy Azure. Jeśli magazyn źródłowy nie zostanie określony, użytkownicy, którzy nie mają uprawnień do wdrażania lub uzyskiwania dostępu do kluczy tajnych w magazynie kluczy, będą mogli korzystać z dostawcy zasobów obliczeniowych (CRP). Listy ACL istnieją nawet dla nieistniejących zasobów.

Jeśli podano nieprawidłowy identyfikator magazynu źródłowego, ale prawidłowy adres URL magazynu kluczy, zostanie zgłoszony błąd podczas sondowania operacji.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Jeśli dodajesz klucze tajne do istniejącego zestawu skalowania maszyn wirtualnych, czy wpisy tajne są wprowadzane do istniejących maszyn wirtualnych lub tylko do nowych?

Certyfikaty są dodawane do wszystkich maszyn wirtualnych, nawet wcześniej istniejących. Jeśli właściwość upgradePolicy zestawu skalowania maszyn wirtualnych jest ustawiona na wartość **Ręczna**, certyfikat zostanie dodany do maszyny wirtualnej podczas przeprowadzania ręcznej aktualizacji na maszynie wirtualnej.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Gdzie mogę umieścić certyfikaty dla maszyn wirtualnych z systemem Linux?

Aby dowiedzieć się, jak wdrażać certyfikaty dla maszyn wirtualnych z systemem Linux, zobacz [Wdrażanie certyfikatów na maszynach wirtualnych z magazynu kluczy zarządzanego przez klienta](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak mogę dodać nowego certyfikatu magazynu do nowego obiektu certyfikatu?

Aby dodać certyfikat magazynu do istniejącego wpisu tajnego, zapoznaj się z poniższym przykładem programu PowerShell. Używaj tylko jednego obiektu tajnego.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co się dzieje z certyfikatami w przypadku odtworzenie obrazu maszyny wirtualnej?

W przypadku odtworzenie obrazu maszyny wirtualnej certyfikaty zostaną usunięte. Odtworzenie obrazu powoduje usunięcie całego dysku systemu operacyjnego.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co się stanie w przypadku usunięcia certyfikatu z magazynu kluczy?

Jeśli wpis tajny jest usuwany z magazynu kluczy, a następnie uruchamiane `stop deallocate` dla wszystkich maszyn wirtualnych, a następnie ponownie uruchamiany, wystąpi błąd. Błąd występuje, ponieważ CRP musi pobrać wpisy tajne z magazynu kluczy, ale nie może. W tym scenariuszu można usunąć certyfikaty z modelu zestawu skalowania maszyn wirtualnych.

Składnik CRP nie utrzymuje wpisów tajnych klienta. Po uruchomieniu `stop deallocate` dla wszystkich maszyn wirtualnych w zestawie skalowania maszyn wirtualnych pamięć podręczna zostanie usunięta. W tym scenariuszu wpisy tajne są pobierane z magazynu kluczy.

Ten problem nie występuje podczas skalowania w poziomie, ponieważ w usłudze Azure Service Fabric istnieje buforowana kopia klucza tajnego (w modelu dzierżawy o pojedynczej sieci szkieletowej).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Dlaczego muszę określić wersję certyfikatu, gdy używam Key Vault?

Aby określić wersję certyfikatu, należy uzyskać informacje o tym, jaki certyfikat jest wdrażany na maszynach wirtualnych. Key Vault

Jeśli utworzysz maszynę wirtualną, a następnie zaktualizujesz wpis tajny w magazynie kluczy, nowy certyfikat nie zostanie pobrany do maszyn wirtualnych. Ale Twoje maszyny wirtualne są wyświetlane w celu odwoływania się do niego, a nowe są dostępne dla nowych maszyn wirtualnych. Aby tego uniknąć, należy odwołać się do wersji tajnej.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mój zespół współpracuje z kilkoma certyfikatami, które są dystrybuowane do nas jako klucze publiczne CER. Jakie jest zalecane podejście do wdrażania tych certyfikatów w zestawie skalowania maszyn wirtualnych?

Aby wdrożyć klucze publiczne. cer do zestawu skalowania maszyn wirtualnych, można wygenerować plik PFX zawierający tylko pliki. cer. W tym celu użyj `X509ContentType = Pfx` . Na przykład Załaduj plik CER jako obiekt x509Certificate2 w języku C# lub PowerShell, a następnie Wywołaj metodę.

Aby uzyskać więcej informacji, zobacz [Metoda x509. Export (X509ContentType, String)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?view=netcore-3.1#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Jak mogę przekazać certyfikaty jako ciągi Base64?

Aby emulować przekazywanie certyfikatu jako ciąg Base64, można wyodrębnić najnowszą wersję adresu URL w szablonie Menedżer zasobów. Dołącz następującą właściwość JSON do szablonu Menedżer zasobów:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Czy muszę zawijać certyfikaty w obiektach JSON w magazynach kluczy?

W przypadku zestawów skalowania maszyn wirtualnych i maszyn wirtualnych certyfikaty muszą być opakowane w obiekty JSON.

Obsługujemy również typ zawartości application/x-PKCS12.

Obecnie nie obsługujemy plików CER. Aby użyć plików cer, wyeksportuj je do kontenerów PFX.



## <a name="compliance-and-security"></a>Zgodność i zabezpieczenia

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Czy zestawy skalowania maszyn wirtualnych są zgodne ze standardem PCI?

Zestawy skalowania maszyn wirtualnych to cienka warstwa interfejsu API na rozwiązaniu CRP. Obydwa składniki są częścią platformy obliczeniowej w drzewie usług platformy Azure.

Z punktu widzenia zgodności zestawy skalowania maszyn wirtualnych są integralną częścią platformy obliczeniowej Azure. Współużytkują one zespół, narzędzia, procesy, metodologię wdrażania, kontrolę zabezpieczeń kompilację dokładnie na czas (JIT), monitorowanie, alerty itd. z rozwiązaniem CRP. Zestawy skalowania maszyn wirtualnych są zgodne ze standardem PCI (Payment Card Industry), ponieważ rozwiązanie CRP jest częścią bieżącego atestu PCI Data Security Standard (DSS).

Aby uzyskać więcej informacji, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Czy [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) działają z zestawami skalowania maszyn wirtualnych?

Tak. Możesz zobaczyć przykładowe szablony MSI w szablonach szybkiego startu platformy Azure dla [systemów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) i Windows.

## <a name="deleting"></a>Usuwanie

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Czy po usunięciu wystąpień będą przestrzegane blokady ustawione w miejscu dla wystąpień zestawu skalowania maszyn wirtualnych?

W witrynie Azure Portal można usunąć pojedyncze wystąpienie lub usuwanie zbiorcze, zaznaczając wiele wystąpień. Jeśli podjęto próbę usunięcia pojedynczego wystąpienia, które ma zablokowaną blokadę, blokada jest przestrzegana i nie będzie można usunąć wystąpienia. Jeśli jednak zbiorczo wybierzesz wiele wystąpień, a dowolne z tych wystąpień ma blokadę, blokady nie będą przestrzegane, a wszystkie wybrane wystąpienia zostaną usunięte.

W interfejsie wiersza polecenia platformy Azure można tylko usunąć pojedyncze wystąpienie. Jeśli podjęto próbę usunięcia pojedynczego wystąpienia, które ma zablokowaną blokadę, blokada jest przestrzegana i nie będzie można usunąć tego wystąpienia.

## <a name="extensions"></a>Rozszerzenia

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak mogę usunąć rozszerzenia zestawu skalowania maszyn wirtualnych?

Aby usunąć rozszerzenie zestawu skalowania maszyn wirtualnych, użyj następującego przykładu programu PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Wartość ExtensionName można znaleźć w temacie `$vmss` .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Czy istnieje przykład szablonu zestawu skalowania maszyn wirtualnych, który integruje się z dziennikami Azure Monitor?

Przykład szablonu zestawu skalowania maszyn wirtualnych, który integruje się z dziennikami Azure Monitor, znajduje się w drugim przykładzie w temacie [wdrażanie klastra Service Fabric platformy Azure i włączanie monitorowania przy użyciu dzienników Azure monitor](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak mogę dodać rozszerzenia do wszystkich maszyn wirtualnych w moim zestawie skalowania maszyn wirtualnych?

Jeśli dla zasad aktualizacji ustawiono wartość **Automatyczne**, ponowne wdrożenie szablonu przy użyciu nowych właściwości rozszerzenia aktualizuje wszystkie maszyny wirtualne.

Jeśli zasady aktualizacji zostały ustawione na **Ręczne**, najpierw zaktualizuj rozszerzenie, a następnie ręcznie zaktualizuj wszystkie wystąpienia na maszynach wirtualnych.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Czy rozszerzenia skojarzone z istniejącym zestawem skalowania maszyn wirtualnych są aktualizowane, czy dotyczą istniejące maszyny wirtualne?

Jeśli definicja rozszerzenia w modelu zestawu skalowania maszyn wirtualnych jest aktualizowana, a właściwość upgradePolicy jest ustawiona na **Automatyczne**, aktualizuje maszyny wirtualne. Jeśli właściwość upgradePolicy jest ustawiona na **ręczny**, rozszerzenia są oflagowane jako niezgodne z modelem.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Czy rozszerzenia są uruchamiane ponownie, gdy istniejąca maszyna jest zaleczona lub odtworzona z obrazu?

Jeśli istniejąca maszyna wirtualna jest zaleczona, zostanie wyświetlona jako ponowny rozruch, a rozszerzenia nie zostaną uruchomione ponownie. Jeśli maszyna wirtualna zostanie odtworzony z obrazu, proces przypomina zastąpienie dysku systemu operacyjnego obrazem źródłowym. Wszystkie specjalizacje z najnowszego modelu, takie jak rozszerzenia, są uruchamiane ponownie.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak mogę dołączyć zestaw skalowania maszyn wirtualnych do domeny Active Directory?

Aby przyłączyć zestaw skalowania maszyn wirtualnych do domeny Active Directory (AD), możesz zdefiniować rozszerzenie.

Aby zdefiniować rozszerzenie, należy użyć właściwości JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Moje rozszerzenie zestawu skalowania maszyn wirtualnych próbuje zainstalować coś, co wymaga ponownego uruchomienia.

Jeśli rozszerzenie zestawu skalowania maszyn wirtualnych próbuje zainstalować coś wymagającego ponownego uruchomienia, można użyć rozszerzenia konfiguracji żądanego stanu Azure Automation (Automation DSC). Jeśli system operacyjny to Windows Server 2012 R2, platforma Azure Pobiera konfigurację systemu Windows Management Framework (WMF) 5,0, uruchamia ponownie rozruch, a następnie kontynuuje konfigurację.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak mogę włączyć ochrony przed złośliwym kodem w moim zestawie skalowania maszyn wirtualnych?

Aby włączyć oprogramowanie chroniące przed złośliwym kodem w zestawie skalowania maszyn wirtualnych, użyj następującego przykładu programu PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Jak mogę wykonać skrypt niestandardowy, który jest hostowany na prywatnym koncie magazynu?

Aby wykonać niestandardowy skrypt, który jest hostowany na prywatnym koncie magazynu, skonfiguruj ustawienia chronione przy użyciu klucza i nazwy konta magazynu. Aby uzyskać więcej informacji, zobacz [rozszerzenie niestandardowego skryptu](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity).

## <a name="passwords"></a>Hasła

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Jak mogę zresetować hasła dla maszyn wirtualnych w zestawie skalowania maszyn wirtualnych?

Istnieją dwa podstawowe sposoby zmiany hasła dla maszyn wirtualnych w zestawach skalowania.

- Zmień model zestawu skalowania maszyn wirtualnych bezpośrednio. Dostępne w przypadku interfejsu API 2017-12-01 i nowszych.

    Zaktualizuj poświadczenia administratora bezpośrednio w modelu zestawu skalowania (na przykład przy użyciu Azure Resource Explorer, programu PowerShell lub interfejsu wiersza polecenia). Po zaktualizowaniu zestawu skalowania wszystkie nowe maszyny wirtualne będą miały nowe poświadczenia. Istniejące maszyny wirtualne mają nowe poświadczenia tylko wtedy, gdy są odtwarzane z obrazu.

- Zresetuj hasło przy użyciu rozszerzeń dostępu do maszyny wirtualnej. Upewnij się, że spełniasz wymagania dotyczące haseł, zgodnie z opisem w [tym miejscu](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    Użyj poniższego przykładu z programu PowerShell:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Sieć

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Czy można przypisać sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) do zestawu skalowania, aby odnosił się do wszystkich kart sieciowych maszyn wirtualnych w zestawie?

Tak. Grupę zabezpieczeń sieci można zastosować bezpośrednio do zestawu skalowania, odwołując się do niego w sekcji networkInterfaceConfigurations profilu sieciowego. Przykład:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jak mogę przeprowadzić zamianę adresu VIP dla zestawów skalowania maszyn wirtualnych w ramach tej samej subskrypcji i tego samego regionu?

Jeśli masz dwa zestawy skalowania maszyn wirtualnych z Azure Load Balancer frontonów i znajdują się one w tej samej subskrypcji i regionie, możesz cofnąć alokację publicznych adresów IP od każdego z nich i przypisać je do drugiego. Zobacz artykuł [wymiana wirtualnych adresów VIP — wdrożenie Blue-zielony w Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) na przykład. Oznacza to opóźnienia, gdy zasoby są cofane i przyliczane na poziomie sieci. Szybszą opcją jest użycie Application Gateway platformy Azure z dwiema pulami zaplecza i regułą routingu. Możesz również hostować aplikację za pomocą [usługi Azure App Service](https://azure.microsoft.com/services/app-service/) , która zapewnia obsługę szybkiego przełączania między miejscami przejściowymi i produkcyjnymi.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Jak mogę określić zakres prywatnych adresów IP do użycia w ramach alokacji statycznego prywatnego adresu IP?

Adresy IP są wybierane z określonej podsieci.

Metoda alokacji adresów IP zestawu skalowania maszyn wirtualnych jest zawsze "dynamiczna", ale nie oznacza to, że te adresy IP mogą się zmieniać. W takim przypadku "dynamiczny" oznacza tylko, że adres IP nie zostanie określony w żądaniu PUT. Określ zestaw statyczny przy użyciu podsieci.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Jak mogę wdrożyć zestaw skalowania maszyn wirtualnych w istniejącej sieci wirtualnej platformy Azure?

Aby wdrożyć zestaw skalowania maszyn wirtualnych w istniejącej sieci wirtualnej platformy Azure, zobacz [wdrażanie zestawu skalowania maszyn wirtualnych do istniejącej sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Czy można używać zestawów skalowania z przyspieszoną siecią?

Tak. Aby używać przyspieszonej sieci, należy ustawić enableAcceleratedNetworking na true w ustawieniach networkInterfaceConfigurations zestawu skalowania. Na przykład

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak można skonfigurować serwery DNS używane przez zestaw skalowania?

Aby utworzyć zestaw skalowania maszyn wirtualnych za pomocą niestandardowej konfiguracji DNS, Dodaj pakiet dnsSettings JSON do sekcji skalowanie networkInterfaceConfigurations. Przykład:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak skonfigurować zestaw skalowania, aby przypisać publiczny adres IP do każdej maszyny wirtualnej?

Aby utworzyć zestaw skalowania maszyn wirtualnych, który przypisuje publiczny adres IP do każdej maszyny wirtualnej, upewnij się, że wersja interfejsu API Microsoft. COMPUTE/virtualMachineScaleSets to 2017-03-30, a następnie Dodaj pakiet _publicipaddressconfiguration_ JSON do sekcji elementy ipconfiguration zestawu skalowania. Przykład:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Czy można skonfigurować zestaw skalowania do pracy z wieloma bramami aplikacji?

Tak. Identyfikatory zasobów dla wielu pul adresów zaplecza Application Gateway można dodać do listy _applicationGatewayBackendAddressPools_ w obszarze elementy _ipconfiguration_ profilu sieciowego zestawu skalowania.

## <a name="scale"></a>Skalowanie

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>W jakim przypadku można utworzyć zestaw skalowania maszyn wirtualnych z mniej niż dwiema maszynami wirtualnymi?

Jednym z powodów utworzenia zestawu skalowania maszyn wirtualnych z mniejszą liczbą niż dwie maszyny wirtualne będzie użycie właściwości elastycznych zestawu skalowania maszyn wirtualnych. Na przykład można wdrożyć zestaw skalowania maszyn wirtualnych z zerowymi maszynami wirtualnymi w celu zdefiniowania infrastruktury bez płacenia kosztów związanych z maszyną wirtualną. Następnie, gdy wszystko będzie gotowe do wdrożenia maszyn wirtualnych, zwiększ "pojemność" zestawu skalowania maszyn wirtualnych do liczby wystąpień produkcyjnych.

Kolejną przyczyną może być utworzenie zestawu skalowania maszyn wirtualnych z mniejszą ilością niż dwie maszyny wirtualne, jeśli chodzi o dostępność niż w przypadku korzystania z zestawu dostępności z dyskretnymi maszynami wirtualnymi. Zestawy skalowania maszyn wirtualnych umożliwiają współpracę z niezróżnicowanymi jednostkami obliczeniowymi zamienne. Ta jednorodność jest kluczem odróżniającym dla zestawów skalowania maszyn wirtualnych i zestawów dostępności. Wiele obciążeń bezstanowych nie śledzi poszczególnych jednostek. Jeśli obciążenie spadnie, można skalować w dół do jednej jednostki obliczeniowej, a następnie skalować w górę do wielu w miarę wzrostu obciążenia.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Jak mogę zmienić liczbę maszyn wirtualnych w zestawie skalowania maszyn wirtualnych?

Aby zmienić liczbę maszyn wirtualnych w zestawie skalowania maszyn wirtualnych w Azure Portal, w sekcji Właściwości zestawu skalowania maszyn wirtualnych kliknij blok skalowania i Użyj paska suwaka.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak mogę zdefiniować niestandardowe alerty dla po osiągnięciu określonych progów?

Masz pewną elastyczność w obsłudze alertów dla określonych progów. Na przykład można zdefiniować niestandardowe elementy webhook. Następujący przykład elementu webhook pochodzi z szablonu Menedżer zasobów:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Stosowanie poprawek i operacji

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Czy można utworzyć zestaw skalowania w istniejącej grupie zasobów?

Tak, można utworzyć zestaw skalowania w istniejącej grupie zasobów.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Czy mogę przenieść zestaw skalowania do innej grupy zasobów?

Tak. zasoby zestawu skalowania można przenosić do nowej subskrypcji lub grupy zasobów.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak zaktualizować zestaw skalowania maszyn wirtualnych do nowego obrazu? Jak mogę zarządzać poprawkami?

Aby zaktualizować zestaw skalowania maszyn wirtualnych do nowego obrazu i aby zarządzać poprawkami, zobacz [uaktualnianie zestawu skalowania maszyn wirtualnych](./virtual-machine-scale-sets-upgrade-scale-set.md).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Czy mogę użyć operacji odtworzenie obrazu do zresetowania maszyny wirtualnej bez zmiany obrazu? (Oznacza to, że chcę zresetować maszynę wirtualną do ustawień fabrycznych, a nie na nowy obraz).

Tak, możesz użyć operacji Odtwórz z obrazu, aby zresetować maszynę wirtualną bez zmiany obrazu. Jeśli jednak zestaw skalowania maszyn wirtualnych odwołuje się do obrazu platformy przy użyciu programu `version = latest` , maszyna wirtualna może przeprowadzić aktualizację do nowszej wersji obrazu systemu operacyjnego podczas wywoływania `reimage` .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Czy możliwe jest zintegrowanie zestawów skalowania z dziennikami Azure Monitor?

Tak, możesz zainstalować rozszerzenie Azure Monitor na maszynach wirtualnych zestawu skalowania. Oto przykład interfejsu wiersza polecenia platformy Azure:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Wymagane identyfikator obszaru roboczego i workspaceKey można znaleźć w obszarze roboczym Log Analytics Azure Portal. Na stronie Przegląd kliknij kafelek ustawienia. Kliknij kartę połączone źródła w górnej części strony.

> [!NOTE]
> Jeśli zestaw skalowania _upgradePolicy_ jest ustawiony na ręczny, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia będzie to _AZ VMSS Update-Instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak mogę włączyć diagnostykę rozruchu?

Aby włączyć diagnostykę rozruchu, najpierw utwórz konto magazynu. Następnie umieść ten blok JSON w zestawie skalowania maszyn wirtualnych **virtualMachineProfile** i zaktualizuj zestaw skalowania maszyn wirtualnych:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Po utworzeniu nowej maszyny wirtualnej Właściwość InstanceView maszyny wirtualnej wyświetla szczegóły zrzutu ekranu i tak dalej. Oto przykład:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Właściwości maszyny wirtualnej

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak mogę pobrać informacji o właściwościach dla każdej maszyny wirtualnej bez wykonywania wielu wywołań? Na przykład jak uzyskać domenę błędów dla każdej z maszyn wirtualnych 100 w moim zestawie skalowania maszyn wirtualnych?

Aby uzyskać informacje o właściwościach dla każdej maszyny wirtualnej bez wykonywania wielu wywołań, możesz wywołać `ListVMInstanceViews` interfejs API REST `GET` dla następującego identyfikatora URI zasobu:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines? $expand = instanceView&$select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Czy można przekazywać inne argumenty rozszerzenia do różnych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych?

Nie, nie można przekazać różnych argumentów rozszerzenia do różnych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. Jednak rozszerzenia mogą działać na podstawie unikatowych właściwości maszyny wirtualnej, na przykład na nazwę komputera. Rozszerzenia mogą również wykonywać zapytania dotyczące metadanych wystąpienia http://169.254.169.254 , aby uzyskać więcej informacji o maszynie wirtualnej.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Dlaczego istnieją przerwy między nazwami maszyn wirtualnych zestawu skalowania maszyn wirtualnych i identyfikatorami maszyn wirtualnych? Na przykład: 0, 1, 3...

Istnieją przerwy między nazwami maszyn wirtualnych zestawu skalowania maszyn wirtualnych i identyfikatorami maszyn wirtualnych, ponieważ właściwość nadmiernego **udostępniania** zestawu skalowania maszyn wirtualnych jest ustawiona na wartość domyślną **true**. Jeśli nadmierne Inicjowanie obsługi administracyjnej ma **wartość true**, tworzone są więcej maszyn wirtualnych niż żądane. Dodatkowe maszyny wirtualne zostaną następnie usunięte. W takim przypadku zwiększasz niezawodność wdrożenia, ale kosztem ciągłego nazewnictwa i ciągłymi regułami translacji adresów sieciowych (NAT).

Dla tej właściwości można ustawić **wartość false**. W przypadku małych zestawów skalowania maszyn wirtualnych nie wpływa to znacząco na niezawodność wdrożenia.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaka jest różnica między usuwaniem maszyny wirtualnej w zestawie skalowania maszyn wirtualnych i cofanie przydziału maszyny wirtualnej? Kiedy należy wybrać jeden w drugim?

Główną różnicą między usuwaniem maszyny wirtualnej w zestawie skalowania maszyn wirtualnych i cofnięciem przydziału maszyny wirtualnej jest to, że `deallocate` nie są usuwane wirtualne dyski twarde (VHD). Istnieją koszty magazynu skojarzone z uruchomionym programem `stop deallocate` . Możesz użyć jednej z nich lub drugiej z następujących powodów:

- Chcesz zatrzymać płatne koszty obliczeń, ale chcesz zachować stan dysku maszyn wirtualnych.
- Chcesz szybciej uruchomić zestaw maszyn wirtualnych, niż można skalować zestaw skalowania maszyn wirtualnych.
  - W związku z tym scenariuszem można utworzyć własny aparat skalowania automatycznego i zwiększyć skalę kompleksową.
- Istnieje zestaw skalowania maszyn wirtualnych, który jest nierównomiernie dystrybuowany w domenach błędów lub domenach aktualizacji. Może to być spowodowane tym, że zostały selektywnie usunięte maszyny wirtualne lub maszyny wirtualne zostały usunięte po przeprowadzeniu aprowizacji. Uruchomiona, `stop deallocate` a następnie `start` w zestawie skalowania maszyn wirtualnych równomiernie dystrybuuje maszyny wirtualne między domenami błędów lub domenami aktualizacji.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Jak mogę utworzyć migawki wystąpienia zestawu skalowania maszyn wirtualnych?
Utwórz migawkę z wystąpienia zestawu skalowania maszyn wirtualnych.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Utwórz dysk zarządzany na podstawie migawki.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
