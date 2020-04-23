---
title: Często zadawane pytania dotyczące usługi Azure Virtual Machine Scale Sets
description: Uzyskaj odpowiedzi na najczęściej zadawane pytania dotyczące zestawów skalowania maszyny wirtualnej na platformie Azure.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mimckitt
ms.openlocfilehash: c2db0cca120d08b85229618547a2aaabbba437ad
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870215"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Często zadawane pytania dotyczące usługi Azure Virtual Machine Scale Sets

Uzyskaj odpowiedzi na często zadawane pytania dotyczące zestawów skalowania maszyny wirtualnej na platformie Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Najczęściej zadawane pytania dotyczące zestawów skalowania

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Ile maszyn wirtualnych może się znajdować w zestawie skalowania?

Zestaw skalowania może mieć od 0 do 1000 maszyn wirtualnych na podstawie obrazów platformy lub od 0 do 600 maszyn wirtualnych na podstawie obrazów niestandardowych.

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

Utwórz i przechwyć obraz maszyny Wirtualnej, a następnie użyj go jako źródła zestawu skalowania. Aby uzyskać samouczek dotyczący tworzenia i używania niestandardowego obrazu maszyny Wirtualnej, można użyć [interfejsu wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md) lub programu Azure [PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Jeśli zmniejszę pojemność zestawu skalowania z 20 do 15, które maszyny wirtualne zostaną usunięte?

W celu zapewnienia maksymalnej dostępności maszyny wirtualne są usuwane z zestawu skalowania równomiernie w domenach błędów i domenach aktualizacji. Najpierw są usuwane maszyny wirtualne o najwyższym identyfikatorze.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Co się stanie, jeśli następnie zwiększę pojemność z 15 do 18?

Zwiększenie pojemności do 18 spowoduje utworzenie 3 nowych maszyn wirtualnych. Za każdym razem następuje zwiększenie identyfikatora wystąpienia maszyny wirtualnej względem poprzedniej najwyższej wartości (na przykład 20, 21, 22). Maszyny wirtualne są równoważone w domenach błędów i domenach aktualizacji.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Czy mogę wymusić sekwencję wykonywania w przypadku korzystania z wielu rozszerzeń w zestawie skalowania?

Tak, można użyć [sekwencjonowania rozszerzenia](virtual-machine-scale-sets-extension-sequencing.md)zestawu skalowania .

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Czy zestawy skalowania współdziałają z zestawami dostępności platformy Azure?

Zestaw skalowania regionalnego (nieprzezłowego) używa *grup miejsc docelowych,* które działają jako niejawny zestaw dostępności z pięcioma domenami błędów i pięcioma domenami aktualizacji. Zestawy skalowania ponad 100 maszyn wirtualnych obejmują wiele grup miejsc docelowych. Aby uzyskać więcej informacji na temat grup umieszczania, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). Zestaw dostępności maszyn wirtualnych może znajdować się w tej samej sieci wirtualnej co zestaw skalowania maszyn wirtualnych. Typowa konfiguracja polega na umieszczeniu maszyn wirtualnych węzła kontrolnego (często wymagających unikatowej konfiguracji) w zestawie dostępności, a węzłów danych w zestawie skalowania.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Czy zestawy skalowania działają ze strefami dostępności platformy Azure?

Tak! Aby uzyskać więcej informacji, zobacz [dokument strefy zestawu skalowania](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatyczne skalowanie

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jakie są najlepsze rozwiązania dotyczące skalowania automatycznego platformy Azure?

Aby uzyskać najlepsze rozwiązania dotyczące skalowania automatycznego, zobacz [Najważniejsze wskazówki dotyczące skalowania automatycznego maszyn wirtualnych.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices)

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Gdzie znajdę nazwy metryk skalowania automatycznego, które używają metryk opartych na hoście?

Aby uzyskać nazwy metryk skalowania automatycznego korzystającego z metryk opartych na hoście, zobacz [Obsługiwane metryki za pomocą usługi Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Czy są jakieś przykłady skalowania automatycznego na podstawie tematu usługi Azure Service Bus i długości kolejki?

Tak. Aby zapoznać się z przykładami skalowania automatycznego na podstawie tematu usługi Azure Service Bus i długości kolejki, zobacz [Skalowanie automatyczne monitora platformy Azure — typowe metryki](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

W przypadku kolejki usługi Service Bus należy użyć następującego JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

W przypadku kolejki magazynu należy użyć następującego JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Zastąp przykładowe wartości identyfikatorami URI (Uniform Resource Identifiers) zasób.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Czy należy skalować automatycznie przy użyciu metryk opartych na hoście lub rozszerzenia diagnostyki?

Można utworzyć ustawienie skalowania automatycznego na maszynie wirtualnej, aby używać metryk na poziomie hosta lub metryk opartych na goszce gościa.

Aby uzyskać listę obsługiwanych metryk, zobacz [Skalowanie automatyczne usługi Azure Monitor typowe metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Aby uzyskać pełną próbkę dla zestawów skalowania maszyny wirtualnej, zobacz [Zaawansowana konfiguracja skalowania automatycznego przy użyciu szablonów Menedżera zasobów dla zestawów skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

W przykładzie użyto metryki procesora CPU na poziomie hosta i metryki liczby komunikatów.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Jak ustawić reguły alertów w zestawie skalowania maszyny wirtualnej?

Alerty dotyczące metryk dla zestawów skalowania maszyn wirtualnych można tworzyć za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [przykłady szybkiego startu programu Azure Monitor PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) i [przykłady szybkiego startu interfejsu wiersza szybkiego interfejsu wiersza platformy Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId zestawu skalowania maszyny wirtualnej wygląda następująco:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Można wybrać dowolny licznik wydajności maszyny Wirtualnej jako metrykę, aby ustawić alert dla. Aby uzyskać więcej informacji, zobacz [metryki systemu operacyjnego gościa dla maszyn wirtualnych systemu Windows opartych na Menedżerze zasobów](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) i [metryk systemu operacyjnego gościa dla maszyn wirtualnych systemu Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) w [artykule skalowanie automatyczne usługi Azure Monitor.](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak skonfigurować skalowanie automatyczne w skali maszyny wirtualnej ustawionej przy użyciu programu PowerShell?

Aby skonfigurować skalowanie automatyczne w skali maszyny wirtualnej ustawionej przy użyciu programu PowerShell, zobacz [automatyczne skalowanie zestawu skalowania maszyny wirtualnej](tutorial-autoscale-powershell.md). Można również skonfigurować skalowanie automatyczne za pomocą [szablonów](tutorial-autoscale-template.md) [interfejsu wiersza polecenia](tutorial-autoscale-cli.md) platformy Azure i platformy Azure


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Jeśli mam zatrzymane (cofnięto) maszyny Wirtualnej, jest to, że maszyna wirtualna została uruchomiona w ramach operacji skalowania automatycznego?

Nie. Jeśli reguły skalowania automatycznego wymagają dodatkowych wystąpień maszyn wirtualnych jako część zestawu skalowania, tworzone jest nowe wystąpienie maszyny Wirtualnej. Wystąpienia maszyn wirtualnych, które są zatrzymane (cofnięto przydział) nie są uruchamiane jako część zdarzenia skalowania automatycznego. Jednak te zatrzymane (cofnięto) maszyny wirtualne mogą zostać usunięte jako część zdarzenia skalowania automatycznego, które skaluje się w liczbie wystąpień, w taki sam sposób, w jaki każde wystąpienie maszyny Wirtualnej może zostać usunięte na podstawie kolejności identyfikatora wystąpienia maszyny Wirtualnej.



## <a name="certificates"></a>Certyfikaty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Jak bezpiecznie wysłać certyfikat na maszynę wirtualną?

Aby bezpiecznie wysłać certyfikat do maszyny Wirtualnej, można zainstalować certyfikat klienta bezpośrednio w magazynie certyfikatów systemu Windows z magazynu kluczy klienta.

Użyj następującego JSON:

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

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie zestawu skalowania maszyny wirtualnej](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Jak używać certyfikatów z podpisem własnym aprowizowanym dla klastrów sieci szkieletowej usług Azure?
W najnowszym przykładzie użyj następującej instrukcji interfejsu wiersza polecenia azure w powłoki azure, przeczytaj przykładową dokumentację modułu interfejsu wiersza polecenia sieci szkieletowych usług, która zostanie wydrukowana w stdout:

```azurecli
az sf cluster create -h
```

Certyfikaty z podpisem własnym nie mogą być używane do rozproszonego zaufania dostarczanego przez urząd certyfikacji i nie powinny być używane dla żadnego klastra sieci szkieletowej usług przeznaczonego do obsługi rozwiązań produkcyjnych przedsiębiorstwa; aby uzyskać dodatkowe wskazówki dotyczące zabezpieczeń sieci szkieletowej usług, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń sieci szkieletowej usług azure](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) i [scenariuszami zabezpieczeń klastra sieci szkieletowej usług.](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Czy można określić parę kluczy SSH do użycia do uwierzytelniania SSH z zestawem skalowania maszyny wirtualnej systemu Linux z szablonu Menedżera zasobów?

Tak. Interfejs API REST dla **systemu osProfile** jest podobny do standardowego interfejsu API REST maszyny Wirtualnej.

Dołącz **osProfile w** szablonie:

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

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie zestawu skalowania maszyny wirtualnej](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak usunąć przestarzałe certyfikaty?

Aby usunąć przestarzałe certyfikaty, usuń stary certyfikat z listy certyfikatów przechowalni. Pozostaw wszystkie certyfikaty, które mają pozostać na komputerze na liście. Nie spowoduje to usunięcia certyfikatu ze wszystkich maszyn wirtualnych. Nie dodaje również certyfikatu do nowych maszyn wirtualnych, które są tworzone w zestawie skalowania maszyny wirtualnej.

Aby usunąć certyfikat z istniejących maszyn wirtualnych, użyj niestandardowego rozszerzenia skryptu, aby ręcznie usunąć certyfikaty z magazynu certyfikatów.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Jak wstrzyknąć istniejący klucz publiczny SSH do warstwy SSH zestawu skalowania maszyny wirtualnej podczas inicjowania obsługi administracyjnej?

Jeśli udostępniasz maszynom wirtualnym tylko publiczny klucz SSH, nie musisz umieszczać kluczy publicznych w magazynie kluczy kluczowych. Klucze publiczne nie są tajne.

Podczas tworzenia maszyny Wirtualnej systemu Linux można podać klucze publiczne SSH w postaci zwykłego tekstu:

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

linuxNazwa elementu konfiguracji | Wymagany | Typ | Opis
--- | --- | --- | ---
Ssh | Nie | Collection | Określa konfigurację klucza SSH dla systemu operacyjnego Linux
ścieżka | Yes | Ciąg | Określa ścieżkę pliku systemu Linux, w której powinny znajdować się klucze lub certyfikat SSH
Keydata | Yes | Ciąg | Określa podstawowy klucz publiczny SSH zakodowany w kodach bazowych

Na przykład zobacz [szablon szybki start programu 101-vm-sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Po uruchomieniu `Update-AzVmss` po dodaniu więcej niż jednego certyfikatu z tego samego magazynu kluczy, widzę następujący komunikat:

>Update-AzVmss: Klucz tajny listy zawiera powtarzające\<się wystąpienia /subscriptions/ my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, co jest niedozwolone.

Może się tak zdarzyć, jeśli spróbujesz ponownie dodać ten sam magazyn zamiast nowego certyfikatu magazynu dla istniejącego magazynu źródłowego. Polecenie `Add-AzVmssSecret` nie działa poprawnie, jeśli dodajesz dodatkowe wpisy tajne.

Aby dodać więcej wpisów tajnych z tego samego magazynu kluczy, zaktualizuj listę $vmss.properties.osProfile.secrets[0].vaultCertificates list.

Aby uzyskać oczekiwaną strukturę danych wejściowych, zobacz [Tworzenie lub aktualizowanie zestawu maszyn wirtualnych](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Znajdź klucz tajny w obiekcie zestawu skalowania maszyny wirtualnej, który znajduje się w magazynie kluczy. Następnie dodaj odwołanie do certyfikatu (adres URL i nazwę tajnego magazynu) do listy skojarzonej z przechowalnią.

> [!NOTE]
> Obecnie nie można usunąć certyfikatów z maszyn wirtualnych przy użyciu interfejsu API zestawu skalowania maszyny wirtualnej.
>

Nowe maszyny wirtualne nie będą miały starego certyfikatu. Jednak maszyny wirtualne, które mają certyfikat i które są już wdrożone, będą miały stary certyfikat.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Czy mogę wypchnąć certyfikaty do zestawu skalowania maszyny wirtualnej bez podawania hasła, gdy certyfikat znajduje się w magazynie tajnym?

Nie trzeba zakodować haseł w skryptach. Można dynamicznie pobierać hasła z uprawnieniami używanymi do uruchamiania skryptu wdrażania. Jeśli masz skrypt, który przenosi certyfikat z tajnego magazynu `get certificate` kluczy, polecenie magazynu tajnego powoduje również wysunie hasło pliku .pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak działa właściwość Secrets virtualMachineProfile.osProfile dla zestawu skalowania maszyny wirtualnej? Dlaczego potrzebuję wartości sourceVault, gdy muszę określić bezwzględny identyfikator URI certyfikatu przy użyciu właściwości certificateUrl?

Odwołanie do certyfikatu zdalnego zarządzania systemem Windows (WinRM) musi znajdować się we właściwości Wpisy tajne profilu systemu operacyjnego.

Celem wskazania skarbca źródłowego jest wymuszanie zasad listy kontroli dostępu (ACL), które istnieją w modelu usługi Azure Cloud Service użytkownika. Jeśli magazyn źródłowy nie jest określony, użytkownicy, którzy nie mają uprawnień do wdrażania lub uzyskiwania dostępu do wpisów tajnych do magazynu kluczy, będą mogli za pośrednictwem dostawcy zasobów obliczeniowych (CRP). Listy ACL istnieją nawet dla zasobów, które nie istnieją.

Jeśli podasz niepoprawny identyfikator magazynu źródłowego, ale prawidłowy adres URL magazynu kluczy, podczas sondowania operacji zostanie zgłoszony błąd.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Jeśli dodaję wpisy tajne do istniejącego zestawu skalowania maszyny wirtualnej, są wpisy tajne wstrzykuje się do istniejących maszyn wirtualnych lub tylko do nowych?

Certyfikaty są dodawane do wszystkich maszyn wirtualnych, nawet wcześniej istniejących. Jeśli uaktualnienie zestawu skalowania maszyny wirtualnej Właściwośćpolicy jest ustawiona na **ręczną,** certyfikat jest dodawany do maszyny Wirtualnej podczas wykonywania ręcznej aktualizacji na maszynie wirtualnej.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Gdzie mogę umieścić certyfikaty dla maszyn wirtualnych z systemem Linux?

Aby dowiedzieć się, jak wdrażać certyfikaty dla maszyn wirtualnych z systemem Linux, zobacz [Wdrażanie certyfikatów na maszynach wirtualnych z magazynu kluczy zarządzanych przez klienta](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak dodać nowy certyfikat przechowalni do nowego obiektu certyfikatu?

Aby dodać certyfikat przechowalni do istniejącego klucza tajnego, zobacz następujący przykład programu PowerShell. Użyj tylko jednego obiektu tajnego.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co się stanie z certyfikatami, jeśli ponownie zaimażujesz maszynę wirtualną?

Jeśli ponownie zaimaż maszyny Wirtualnej, certyfikaty są usuwane. Reimaging usuwa cały dysk systemu operacyjnego.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co się stanie, jeśli usuniesz certyfikat z magazynu kluczy?

Jeśli klucz tajny zostanie usunięty z magazynu `stop deallocate` kluczy, a następnie uruchomić dla wszystkich maszyn wirtualnych, a następnie uruchomić je ponownie, napotkasz błąd. Błąd występuje, ponieważ crp musi pobrać wpisy tajne z magazynu kluczy, ale nie może. W tym scenariuszu można usunąć certyfikaty z modelu zestawu skalowania maszyny wirtualnej.

Składnik CRP nie utrwala wpisów tajnych klienta. Jeśli zostanie `stop deallocate` uruchomiony dla wszystkich maszyn wirtualnych w zestawie skalowania maszyny wirtualnej, pamięć podręczna zostanie usunięta. W tym scenariuszu wpisy tajne są pobierane z magazynu kluczy.

Nie wystąpi ten problem podczas skalowania w poziomie, ponieważ istnieje buforowana kopia klucza tajnego w sieci szkieletowej usługi Azure (w modelu dzierżawy pojedynczej sieci szkieletowej).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Dlaczego muszę określić wersję certyfikatu podczas korzystania z usługi Key Vault?

Celem wymagania usługi Key Vault, aby określić wersję certyfikatu jest, aby wyjaśnić użytkownikowi, jaki certyfikat jest wdrażany na ich maszyn wirtualnych.

Jeśli utworzysz maszynę wirtualną, a następnie zaktualizujesz klucz tajny w magazynie kluczy, nowy certyfikat nie zostanie pobrany do maszyn wirtualnych. Ale maszyny wirtualne wydają się odwoływać się do niego, a nowe maszyny wirtualne uzyskać nowy klucz tajny. Aby tego uniknąć, należy odwołać się do wersji tajnej.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mój zespół współpracuje z kilkoma certyfikatami, które są dystrybuowane do nas jako .cer klucze publiczne. Jakie jest zalecane podejście do wdrażania tych certyfikatów do zestawu skalowania maszyny wirtualnej?

Aby wdrożyć klucze publiczne .cer w zestawie skalowania maszyny wirtualnej, można wygenerować plik .pfx zawierający tylko pliki .cer. Aby to zrobić, użyj . `X509ContentType = Pfx` Na przykład załaduj plik cer jako obiekt x509Certificate2 w języku C# lub PowerShell, a następnie wywołaj metodę.

Aby uzyskać więcej informacji, zobacz [X509Certificate.Export Method (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Jak przekazać certyfikaty jako ciągi base64?

Aby emulować przekazywanie w certyfikacie jako ciąg base64, można wyodrębnić najnowszy adres URL wersji w szablonie Menedżera zasobów. Dołącz następującą właściwość JSON w szablonie Menedżera zasobów:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Czy muszę zawijać certyfikaty w obiektach JSON w magazynach kluczy?

W zestawach skalowania maszyny wirtualnej i maszynach wirtualnych certyfikaty muszą być zawijane w obiektach JSON.

Obsługujemy również aplikację typu zawartości/x-pkcs12.

Obecnie nie obsługujemy plików .cer. Aby użyć plików cer, wyeksportuj je do kontenerów .pfx.



## <a name="compliance-and-security"></a>Zgodność i bezpieczeństwo

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Czy zestawy skalowania maszyny wirtualnej są zgodne z PCI?

Zestawy skalowania maszyn wirtualnych to cienka warstwa interfejsu API na rozwiązaniu CRP. Obydwa składniki są częścią platformy obliczeniowej w drzewie usług platformy Azure.

Z punktu widzenia zgodności zestawy skalowania maszyn wirtualnych są integralną częścią platformy obliczeniowej Azure. Współużytkują one zespół, narzędzia, procesy, metodologię wdrażania, kontrolę zabezpieczeń kompilację dokładnie na czas (JIT), monitorowanie, alerty itd. z rozwiązaniem CRP. Zestawy skalowania maszyn wirtualnych są zgodne ze standardem PCI (Payment Card Industry), ponieważ rozwiązanie CRP jest częścią bieżącego atestu PCI Data Security Standard (DSS).

Aby uzyskać więcej informacji, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Czy tożsamości zarządzane dla zasobów platformy Azure działają z [zestawami](https://docs.microsoft.com/azure/active-directory/msi-overview) skalowania maszyny wirtualnej?

Tak. W szablonach szybki startu platformy Azure dla [systemów Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) i [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)można zobaczyć przykładowe szablony MSI.

## <a name="deleting"></a>Usuwanie 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Czy blokady ustawione w miejscu na stronie zestawu skalowania maszyny wirtualnej będą przestrzegane podczas usuwania wystąpień?

W witrynie Azure Portal masz możliwość usunięcia pojedynczego wystąpienia lub zbiorczego usuwania przez wybranie wielu wystąpień. Jeśli spróbujesz usunąć pojedyncze wystąpienie, które ma blokadę w miejscu, blokada jest przestrzegana i nie będzie można usunąć wystąpienie. Jeśli jednak zbiorczo wybierzesz wiele wystąpień, a którekolwiek z tych wystąpień ma blokadę w miejscu, blokady nie będą przestrzegane, a wszystkie wybrane wystąpienia zostaną usunięte. 
 
W usłudze Azure CLI masz tylko możliwość usunięcia pojedynczego wystąpienia. Jeśli spróbujesz usunąć pojedyncze wystąpienie, które ma blokadę w miejscu, blokada jest przestrzegana i nie będzie można usunąć tego wystąpienia. 

## <a name="extensions"></a>Rozszerzenia

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak usunąć rozszerzenie zestawu skalowania maszyny wirtualnej?

Aby usunąć rozszerzenie zestawu skalowania maszyny wirtualnej, użyj następującego przykładu programu PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Wartość extensionName można znaleźć `$vmss`w pliku .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Czy istnieje przykład szablonu zestawu skalowania maszyny wirtualnej, który integruje się z dziennikami usługi Azure Monitor?

Przykład szablonu zestawu skalowania maszyny wirtualnej, który integruje się z dziennikami usługi Azure Monitor, zobacz drugi przykład w [obszarze Wdrażanie klastra sieci szkieletowej usług Azure i włącz monitorowanie przy użyciu dzienników usługi Azure Monitor.](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak dodać rozszerzenie do wszystkich maszyn wirtualnych w zestawie skalowania maszyny wirtualnej?

Jeśli zasady aktualizacji jest ustawiona na **automatyczne,** ponowne wdrożenie szablonu z nowych właściwości rozszerzenia aktualizuje wszystkie maszyny wirtualne.

Jeśli zasady aktualizacji są ustawione na **ręczne,** najpierw zaktualizuj rozszerzenie, a następnie ręcznie zaktualizuj wszystkie wystąpienia na maszynach wirtualnych.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Jeśli rozszerzenia skojarzone z istniejącym zestawem skalowania maszyny wirtualnej są aktualizowane, czy dotyczy to istniejących maszyn wirtualnych?

Jeśli definicja rozszerzenia w modelu zestawu skalowania maszyny wirtualnej jest aktualizowana, a właściwość upgradePolicy jest ustawiona na **automatyczną,** aktualizuje maszyny wirtualne. Jeśli upgradePolicy właściwość jest ustawiona na **ręczne**, rozszerzenia są oflagowane jako nie pasujące do modelu.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Czy rozszerzenia są uruchamiane ponownie, gdy istniejący komputer jest wyleczona lub ponownie zaimaged?

Jeśli istniejąca maszyna wirtualna jest wyleczony przez usługę, pojawia się jako ponowne uruchomienie, a rozszerzenia nie są uruchamiane ponownie. Jeśli maszyna wirtualna jest ponownie zaimaged, proces jest podobny, zastępując dysk systemu operacyjnego z obrazem źródłowym. Wszystkie specjalizacji z najnowszego modelu, takich jak rozszerzenia, są uruchamiane ponownie.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak dołączyć skalę maszyny wirtualnej ustawioną do domeny usługi Active Directory?

Aby dołączyć do skali maszyny wirtualnej ustawionej do domeny usługi Active Directory (AD), można zdefiniować rozszerzenie.

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Rozszerzenie zestawu skalowania mojej maszyny wirtualnej próbuje zainstalować coś, co wymaga ponownego uruchomienia komputera.

Jeśli rozszerzenie zestawu skalowania maszyny wirtualnej próbuje zainstalować coś, co wymaga ponownego uruchomienia, można użyć rozszerzenia konfiguracji żądanego stanu automatyzacji usługi Azure Automation (Automation DSC). Jeśli systemem operacyjnym jest Windows Server 2012 R2, platforma Azure pobiera w ramach windows management framework (WMF) 5.0 konfiguracji, ponownie uruchamia, a następnie kontynuuje konfigurację.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak włączyć oprogramowanie ochrony przed złośliwym oprogramowaniem w zestawie skalowania maszyny wirtualnej?

Aby włączyć oprogramowanie ochrony przed złośliwym oprogramowaniem w zestawie skalowania maszyny wirtualnej, użyj następującego przykładu programu PowerShell:

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

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Jak wykonać niestandardowy skrypt hostowany na koncie magazynu prywatnego?

Aby wykonać niestandardowy skrypt, który jest hostowany na koncie magazynu prywatnego, należy skonfigurować chronione ustawienia z kluczem i nazwą konta magazynu. Aby uzyskać więcej informacji, zobacz [Niestandardowe rozszerzenie skryptu](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Hasła

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Jak zresetować hasło dla maszyn wirtualnych w zestawie skalowania maszyny wirtualnej?

Istnieją dwa główne sposoby zmiany hasła dla maszyn wirtualnych w zestawach skalowania.

- Bezpośrednio zmieniaj model zestawu skalowania maszyny wirtualnej. Dostępne w interfejsie API 2017-12-01 i nowszych.

    Zaktualizuj poświadczenia administratora bezpośrednio w modelu zestawu skalowania (na przykład przy użyciu Eksploratora zasobów platformy Azure, programu PowerShell lub interfejsu wiersza polecenia). Po zaktualizowaniu zestawu skalowania wszystkie nowe maszyny wirtualne mają nowe poświadczenia. Istniejące maszyny wirtualne mają tylko nowe poświadczenia, jeśli są one ponownie zaimaged.

- Resetowanie hasła przy użyciu rozszerzeń dostępu do maszyny Wirtualnej. Pamiętaj, aby postępować zgodnie z wymaganiami dotyczącymi haseł opisanymi [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).

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

## <a name="networking"></a>Networking

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Czy można przypisać sieciową grupę zabezpieczeń (NSG) do zestawu skalowania, tak aby była ona stosowana do wszystkich kart sieciowych maszyn wirtualnych w zestawie?

Tak. Sieciowej grupy zabezpieczeń można zastosować bezpośrednio do zestawu skalowania, odwołując się do niej w sekcji networkInterfaceConfigurations profilu sieciowego. Przykład:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jak zrobić zamianę vipa dla zestawów skalowania maszyny wirtualnej w tej samej subskrypcji i w tym samym regionie?

Jeśli masz dwa zestawy skalowania maszyny wirtualnej z frontonami modułu równoważenia obciążenia azure i znajdują się w tej samej subskrypcji i regionie, można cofnąć alokację publicznych adresów IP z każdego z nich i przypisać do drugiego. Zobacz [na przykład wdrożenie wymiany VIP: niebieskozielone w usłudze Azure Resource Manager.](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) Oznacza to jednak opóźnienie, ponieważ zasoby są przydzielane/przydzielane na poziomie sieci. Szybszą opcją jest użycie bramy aplikacji platformy Azure z dwiema pulami wewnętrznej bazy danych i regułą routingu. Alternatywnie można hostować aplikację za pomocą [usługi Azure App,](https://azure.microsoft.com/services/app-service/) która zapewnia obsługę szybkiego przełączania między miejscami przejściowymi i produkcyjnymi.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Jak określić zakres prywatnych adresów IP, które mają być używane do statycznej alokacji prywatnych adresów IP?

Adresy IP są wybierane z określonej podsieci.

Metoda alokacji adresów IP zestawu skalowania maszyny wirtualnej jest zawsze "dynamiczna", ale nie oznacza to, że te adresy IP mogą ulec zmianie. W takim przypadku "dynamiczny" oznacza tylko, że nie określisz adresu IP w żądaniu PUT. Określ zestaw statyczny przy użyciu podsieci.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Jak wdrożyć skalę maszyny wirtualnej ustawioną na istniejącą sieć wirtualną platformy Azure?

Aby wdrożyć skalę maszyny wirtualnej ustawioną na istniejącą sieć wirtualną platformy Azure, zobacz [Wdrażanie skali maszyny wirtualnej ustawionej na istniejącą sieć wirtualną.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Czy mogę używać zestawów skalowania z przyspieszoną siecią?

Tak. Aby użyć przyspieszonej sieci, ustaw enableAcceleratedNetworking do true w ustawieniach networkInterfaceConfigurations zestawu skalowania. Na przykład:

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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak skonfigurować serwery DNS używane przez zestaw skalowania?

Aby utworzyć zestaw skalowania maszyny wirtualnej z niestandardową konfiguracją DNS, dodaj pakiet dnsSettings JSON do sekcji skalowania zestawu sieciInterfaceConfigurations. Przykład:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak skonfigurować zestaw skalowania, aby przypisać publiczny adres IP do każdej maszyny Wirtualnej?

Aby utworzyć zestaw skalowania maszyny wirtualnej, który przypisuje publiczny adres IP do każdej maszyny wirtualnej, upewnij się, że wersja interfejsu API zasobu Microsoft.Compute/virtualMachineScaleSets jest 2017-03-30 i dodaj pakiet JSON _konfiguracji publicipaddress do_ sekcji zestaw skalowania ipConfigurations. Przykład:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Czy można skonfigurować zestaw skalowania do pracy z wieloma bramami aplikacji?

Tak. Identyfikatory zasobów dla wielu pul adresów wewnętrznej bazy danych bramy aplikacji można dodać do listy _applicationGatewayBackendAddressPools_ w sekcji _ipConfigurations_ profilu sieciowego zestawu skalowania.

## <a name="scale"></a>Skalowanie

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>W jakim przypadku należy utworzyć zestaw skalowania maszyny wirtualnej z mniej niż dwoma maszynami wirtualnymi?

Jednym z powodów, aby utworzyć zestaw skalowania maszyny wirtualnej z mniej niż dwie maszyny wirtualne byłoby użycie właściwości elastyczne zestawu skalowania maszyny wirtualnej. Na przykład można wdrożyć zestaw skalowania maszyny wirtualnej z zerową wartością maszyn wirtualnych, aby zdefiniować infrastrukturę bez płacenia kosztów bieżących maszyn wirtualnych. Następnie, gdy jesteś gotowy do wdrożenia maszyn wirtualnych, zwiększyć "pojemność" skali maszyny wirtualnej ustawionej na liczbę wystąpień produkcyjnych.

Innym powodem może utworzyć zestaw skalowania maszyny wirtualnej z mniej niż dwie maszyny wirtualne jest, jeśli masz obawy mniej o dostępność niż przy użyciu zestawu dostępności z dyskretnych maszyn wirtualnych. Zestawy skalowania maszyny wirtualnej umożliwiają pracę z niezróżnionymi jednostkami obliczeniowymi, które są wymaszone. Ta jednorodność jest kluczowym wyróżnikiem dla zestawów skalowania maszyny wirtualnej w porównaniu z zestawami dostępności. Wiele obciążeń bezstanowych nie śledzi poszczególnych jednostek. Jeśli obciążenie spadnie, można skalować w dół do jednej jednostki obliczeniowej, a następnie skalować do wielu, gdy zwiększa się obciążenie.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Jak zmienić liczbę maszyn wirtualnych w zestawie skalowania maszyny wirtualnej?

Aby zmienić liczbę maszyn wirtualnych w skali maszyny wirtualnej ustawionej w witrynie Azure portal, z sekcji właściwości zestawu skalowania maszyny wirtualnej, kliknij blok "Skalowanie" i użyj paska suwaka.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak zdefiniować niestandardowe alerty dotyczące osiągnięcia określonych progów?

Masz pewną elastyczność w sposobie obsługi alertów dla określonych progów. Na przykład można zdefiniować niestandardowe elementów webhook. Poniższy przykład elementu webhook pochodzi z szablonu Menedżera zasobów:

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


## <a name="patching-and-operations"></a>Łatanie i operacje

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Czy można utworzyć zestaw skalowania w istniejącej grupie zasobów?

Tak, można utworzyć zestaw skalowania w istniejącej grupie zasobów.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Czy mogę przenieść zestaw skalowania do innej grupy zasobów?

Tak, można przenieść zasoby zestawu skalowania do nowej subskrypcji lub grupy zasobów.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak zaktualizować skalę maszyny wirtualnej ustawioną na nowy obraz? Jak zarządzać poprawkami?

Aby zaktualizować skalę maszyny wirtualnej ustawioną na nowy obraz i zarządzać poprawkami, zobacz [Uaktualnianie zestawu skalowania maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Czy mogę użyć operacji reimage, aby zresetować maszynę wirtualną bez zmiany obrazu? (Oznacza to, że chcę zresetować maszynę wirtualną do ustawień fabrycznych, a nie do nowego obrazu.)

Tak, można użyć operacji reimage, aby zresetować maszynę wirtualną bez zmiany obrazu. Jeśli jednak zestaw skalowania maszyny wirtualnej `version = latest`odwołuje się do obrazu platformy za pomocą maszyny `reimage`wirtualnej, maszyna wirtualna może zaktualizować do późniejszego obrazu systemu operacyjnego podczas wywoływania .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Czy można zintegrować zestawy skalowania z dziennikami usługi Azure Monitor?

Tak, można zainstalować rozszerzenie usługi Azure Monitor na maszynach wirtualnych zestawu skalowania. Oto przykład interfejsu wiersza polecenia platformy Azure:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Wymagane identyfikatory obszaru roboczego i klawisz workspaceKey można znaleźć w obszarze roboczym usługi Log Analytics w witrynie Azure portal. Na stronie Przegląd kliknij kafelek Ustawienia. Kliknij kartę Połączone źródła u góry.

> [!NOTE]
> Jeśli upgrade zestawu _skalowaniaPolicy_ jest ustawiona na Ręcznie, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując uaktualnienie na nich. W cli byłoby _az vmss update-wystąpień_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak włączyć diagnostykę rozruchu?

Aby włączyć diagnostykę rozruchu, najpierw utwórz konto magazynu. Następnie umieść ten blok JSON w zestawie skalowania maszyny wirtualnej **virtualMachineProfile**i zaktualizuj zestaw skalowania maszyny wirtualnej:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Po utworzeniu nowej maszyny Wirtualnej, InstanceView właściwość maszyny Wirtualnej pokazuje szczegóły zrzutu ekranu i tak dalej. Oto przykład:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Właściwości maszyny wirtualnej

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak uzyskać informacje o właściwościach dla każdej maszyny Wirtualnej bez wykonywania wielu połączeń? Na przykład jak mogę uzyskać domeny błędów dla każdego ze 100 maszyn wirtualnych w mojej maszyny wirtualnej zestaw skalowania?

Aby uzyskać informacje o właściwościach dla każdej maszyny `ListVMInstanceViews` Wirtualnej bez `GET` wykonywania wielu wywołań, można wywołać, wykonując interfejs API REST na następującym identyfikatorze URI zasobu:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Czy można przekazać różne argumenty rozszerzenia do różnych maszyn wirtualnych w zestawie skalowania maszyny wirtualnej?

Nie, nie można przekazać różnych argumentów rozszerzenia do różnych maszyn wirtualnych w zestawie skalowania maszyny wirtualnej. Jednak rozszerzenia mogą działać na podstawie unikatowych właściwości maszyny Wirtualnej, na których są uruchomione, takich jak na nazwę komputera. Rozszerzenia mogą również kwerendy http://169.254.169.254 metadanych wystąpienia, aby uzyskać więcej informacji na temat maszyny Wirtualnej.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Dlaczego istnieją luki między nazwami maszyn wirtualnych ustawionymi na mojej maszynie wirtualnej a identyfikatorami maszyn wirtualnych? Na przykład: 0, 1, 3...

Istnieją luki między nazwami maszyn maszyny wirtualnej ustawionymi na skalę maszyny wirtualnej a identyfikatorami maszyn wirtualnych, ponieważ właściwość **overprovision** ustawiona przez właściwość skalowania maszyny wirtualnej jest ustawiona na domyślną wartość **true**. Jeśli nadmierna aprovisioning jest ustawiona na **true,** tworzona jest więcej maszyn wirtualnych niż żądano. Dodatkowe maszyny wirtualne są następnie usuwane. W takim przypadku można uzyskać zwiększoną niezawodność wdrażania, ale kosztem ciągłych nazewnictwa i ciągłych reguł translacji adresów sieciowych (NAT).

Tę właściwość można ustawić na **false**. W przypadku małych zestawów skalowania maszyny wirtualnej nie wpływa to znacząco na niezawodność wdrażania.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaka jest różnica między usunięciem maszyny wirtualnej w zestawie skalowania maszyny wirtualnej a przydzieleniem alokacji maszyny wirtualnej? Kiedy należy wybrać jeden nad drugim?

Główną różnicą między usunięciem maszyny wirtualnej w zestawie skalowania maszyny `deallocate` wirtualnej a przydzieleniem alokacji maszyny Wirtualnej jest to, że nie usuwa wirtualnych dysków twardych (VHD). Istnieją koszty magazynowania związane `stop deallocate`z uruchomieniem . Możesz użyć jednego lub drugiego z jednego z następujących powodów:

- Chcesz zatrzymać płacenie kosztów obliczeniowych, ale chcesz zachować stan dysku maszyn wirtualnych.
- Chcesz uruchomić zestaw maszyn wirtualnych szybciej niż można skalować w poziomie zestaw skalowania maszyny wirtualnej.
  - Związane z tym scenariuszem, być może utworzono własny aparat skalowania automatycznego i chcesz szybszą skalę end-to-end.
- Masz zestaw skalowania maszyny wirtualnej, który jest nierównomiernie rozłożony między domenami błędów lub domenami aktualizacji. Może to być spowodowane selektywnym usunięciem maszyn wirtualnych lub usunięciem maszyn wirtualnych po przerekrowazji. `stop deallocate` Uruchamianie, `start` a następnie na skali maszyny wirtualnej zestaw równomiernie dystrybuuje maszyny wirtualne między domenami błędów lub domen aktualizacji.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Jak wykonać migawkę wystąpienia zestawu skalowania maszyny wirtualnej?
Utwórz migawkę z wystąpienia zestawu skalowania maszyny wirtualnej.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Utwórz dysk zarządzany z migawki.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
