---
title: Azure HDInsight — Tworzenie klastra — słownik błędów
description: Informacje o rozwiązywaniu problemów występujących podczas tworzenia klastrów usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 469adf1c0e5108f7a8c89a7c3a4bebba5d42b431
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183968"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Błędy tworzenia klastra

W tym artykule opisano rozwiązania błędów, które mogą występować podczas tworzenia klastrów.

> [!NOTE]
> Pierwsze trzy błędy opisane w tym artykule są błędami walidacji. Mogą występować, gdy produkt Azure HDInsight używa klasy **CsmDocument_2_0** .

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Kod błędu: DeploymentDocument "CsmDocument_2_0" nie powiodła się Walidacja

**Błąd**: "nie można uzyskać dostępu do lokalizacji akcji skryptu URI: \<SCRIPT ACTION URL\> "

### <a name="error-message-1"></a>Komunikat o błędzie 1

"Serwer zdalny zwrócił błąd: (404) nie znaleziono".

#### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może uzyskać dostępu do adresu URL akcji skryptu podanego w ramach żądania utworzenia klastra. Usługa otrzymuje poprzedni komunikat o błędzie, gdy próbuje uzyskać dostęp do akcji skryptu.

#### <a name="resolution"></a>Rozwiązanie

- W przypadku adresu URL HTTP lub HTTPS Sprawdź adres URL, próbując przejść do niego z okna przeglądarki incognito.
- W przypadku adresu URL WASB upewnij się, że skrypt istnieje na koncie magazynu podanym w żądaniu. Upewnij się również, że klucz magazynu dla tego konta magazynu jest prawidłowy.
- W przypadku adresu URL ADLS upewnij się, że skrypt istnieje na koncie magazynu.

---

### <a name="error-message-2"></a>Komunikat o błędzie 2

"Podany identyfikator URI skryptu \<SCRIPT_URI\> jest w ADLS, ale ten klaster nie ma podmiotu zabezpieczeń Data Lake Storage"

#### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może uzyskać dostępu do adresu URL akcji skryptu podanego w ramach żądania utworzenia klastra. Usługa otrzymuje poprzedni komunikat o błędzie, gdy próbuje uzyskać dostęp do akcji skryptu.

#### <a name="resolution"></a>Rozwiązanie

Dodaj odpowiednie konto Azure Data Lake Storage Gen 1 do klastra. Należy również dodać nazwę główną usługi, która uzyskuje dostęp do tego konta Data Lake Storage Gen 1 do klastra.

---

### <a name="error-message-3"></a>Komunikat o błędzie 3

"Rozmiar maszyny wirtualnej" \<CUSTOMER_SPECIFIED_VM_SIZE\> "podany w żądaniu jest nieprawidłowy lub nie jest obsługiwany dla roli" \<ROLE\> ". Prawidłowe wartości to: \<VALID_VM_SIZE_FOR_ROLE\> . "

#### <a name="cause"></a>Przyczyna

Określony rozmiar maszyny wirtualnej nie jest dozwolony dla tej roli. Ten błąd może wystąpić, ponieważ wartość rozmiaru maszyny wirtualnej nie działa zgodnie z oczekiwaniami lub nie jest odpowiednia dla roli komputera.

#### <a name="resolution"></a>Rozwiązanie

Komunikat o błędzie zawiera listę prawidłowych wartości dla rozmiaru maszyny wirtualnej. Wybierz jedną z tych wartości i ponów próbę utworzenia żądania klastra.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Kod błędu: InvalidVirtualNetworkId  

### <a name="error"></a>Błąd

"VirtualNetworkId jest nieprawidłowy. VirtualNetworkId " \<USER_VIRTUALNETWORKID\> " * "

### <a name="cause"></a>Przyczyna

Wartość **VirtualNetworkId** określona podczas tworzenia klastra nie ma poprawnego formatu.

### <a name="resolution"></a>Rozwiązanie

Upewnij się, że wartości **VirtualNetworkId** i podsieci mają poprawny format. Aby uzyskać wartość **VirtualNetworkId** :

1. Przejdź do witryny Azure Portal.
1. Wybierz sieć wirtualną.
1. Wybierz element menu **Właściwości** . Wartość właściwości **ResourceID** jest wartością **VirtualNetworkId** .

Oto przykład identyfikatora sieci wirtualnej:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Kod błędu: CustomizationFailedErrorCode

### <a name="error"></a>Błąd

"Wdrożenie klastra nie powiodło się z powodu błędu w akcji skryptu niestandardowego. Akcje zakończone niepowodzeniem: \<SCRIPT_NAME\> Przejdź do interfejsu użytkownika Ambari, aby kontynuować debugowanie błędu ".

### <a name="cause"></a>Przyczyna

Skrypt niestandardowy dostarczony podczas żądania utworzenia klastra jest wykonywany po pomyślnym wdrożeniu klastra. Ten kod błędu wskazuje, że wystąpił błąd podczas wykonywania niestandardowego skryptu o nazwie \<SCRIPT_NAME\> .

### <a name="resolution"></a>Rozwiązanie

Skrypt jest skryptem niestandardowym, dlatego zalecamy rozwiązanie problemu i ponowne uruchomienie skryptu w razie potrzeby. Aby rozwiązać problem z błędem skryptu, zapoznaj się z dziennikami w folderze/var/lib/Ambari-Agent/*. Lub Otwórz stronę **operacje** w interfejsie użytkownika Ambari, a następnie wybierz operację **run_customscriptaction** , aby wyświetlić szczegóły błędu.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Kod błędu: InvalidDocumentErrorCode

### <a name="error"></a>Błąd

" \<META_STORE_TYPE\> Wersja schematu magazynu metadanych \<METASTORE_MAJOR_VERSION\> w bazie danych \<DATABASE_NAME\> jest niezgodna z wersją klastra \<CLUSTER_VERSION\> "

### <a name="cause"></a>Przyczyna

Niestandardowy magazyn metadanych jest niezgodny z wybraną wersją klastra usługi HDInsight. Obecnie klastry usługi HDInsight 4,0 obsługują tylko usługi magazynu metadanych w wersji 3,0 lub nowszej, natomiast klastry usługi HDInsight 3,6 nie obsługują magazynu metadanych w wersji 3,0 lub nowszej.

### <a name="resolution"></a>Rozwiązanie

Używaj tylko wersji magazynu metadanych obsługiwanej przez wersję klastra usługi HDInsight. Jeśli nie określisz niestandardowego magazynu metadanych, Usługa HDInsight wewnętrznie tworzy magazyn metadanych, a następnie usuwa go po usunięciu klastra.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kod błędu: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Błąd

"Nie można nawiązać połączenia z punktem końcowym zarządzania klastrem w celu wykonania operacji skalowania. Sprawdź, czy reguły zabezpieczeń sieci nie blokują dostępu zewnętrznego do klastra i czy można pomyślnie uzyskać dostęp do interfejsu użytkownika Menedżera klastra (Ambari).

### <a name="cause"></a>Przyczyna

Reguła zapory w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) blokuje komunikację z klastrem przy użyciu krytycznych usług zarządzania kondycją platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jeśli planujesz użyć grup zabezpieczeń sieci do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

- Określ region platformy Azure, który ma być używany przez usługę HDInsight.
- Zidentyfikuj adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Adresy IP zarządzania usługą HDInsight](./hdinsight-management-ip-addresses.md).
  - Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight.
  - W przypadku sieciowych grup zabezpieczeń Zezwalaj na ruch przychodzący na porcie 443 z adresów IP. Ta konfiguracja zapewnia, że usługi zarządzania usługą HDInsight mogą dotrzeć do klastra spoza sieci wirtualnej.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Kod błędu: StoragePermissionsBlockedForMsi

### <a name="error"></a>Błąd

"Zarządzana tożsamość nie ma uprawnień na koncie magazynu. Sprawdź, czy rola "właściciel danych obiektów blob magazynu" jest przypisana do zarządzanej tożsamości dla konta magazynu. Storage:/subscriptions/ \<Subscription ID\> /ResourceGroups/ \< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\> , tożsamość zarządzana:/subscriptions/ \<Subscription ID\> /resourceGroups// \< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>

### <a name="cause"></a>Przyczyna

Nie podano uprawnień wymaganych do zarządzania tożsamościami. Tożsamość zarządzana przypisana przez użytkownika nie ma roli współautor Blob Storage na koncie magazynu Azure Data Lake Storage Gen2.

### <a name="resolution"></a>Rozwiązanie

1. Otwórz witrynę Azure Portal.
1. Przejdź do swojego konta magazynu.
1. Poszukaj w obszarze **Access Control (IAM)**.
1. Upewnij się, że użytkownik ma rolę współautor danych obiektów blob magazynu lub przypisaną rolę właściciela danych obiektu blob magazynu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień dla tożsamości zarządzanej na koncie Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Kod błędu: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Błąd

"Reguły zabezpieczeń w sieciowej grupie zabezpieczeń/subscriptions/ \<SubscriptionID\> /resourceGroups/<nazwa grupy zasobów \> default/Providers/Microsoft. Network/networkSecurityGroups/ \<Network Security Group Name\> skonfigurowane z podsiecią/subscriptions/ \<SubscriptionID\> /resourceGroups/RG-westeurope-VNET-TomTom-default \<Resource Group name\> /providers/Microsoft. Network/virtualNetworks/ \<Virtual Network Name\> /Subnets/nie \<Subnet Name\> zezwala na wymagane połączenia przychodzące i/lub wychodzące. Aby uzyskać więcej informacji, odwiedź stronę [Planowanie sieci wirtualnej dla usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)lub skontaktuj się z pomocą techniczną.

### <a name="cause"></a>Przyczyna

Jeśli sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika (UDR) kontrolują ruch przychodzący do klastra usługi HDInsight, należy się upewnić, że klaster może komunikować się z krytycznymi usługami Azure Health i Management.

### <a name="resolution"></a>Rozwiązanie

Jeśli planujesz użyć grup zabezpieczeń sieci do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

- Określ region platformy Azure, który ma być używany na potrzeby usługi HDInsight, i Utwórz bezpieczną listę adresów IP dla danego regionu. Aby uzyskać więcej informacji, zobacz temat [usługi kondycji i zarządzania: określone regiony](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions).
- Zidentyfikuj adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [adresy IP zarządzania usługą HDInsight](./hdinsight-management-ip-addresses.md).
- Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight. W przypadku sieciowych grup zabezpieczeń Zezwalaj na ruch przychodzący na porcie 443 z adresów IP. Ta konfiguracja zapewnia, że usługi zarządzania usługą HDInsight mogą dotrzeć do klastra spoza sieci wirtualnej.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Kod błędu: Instalator klastra nie może zainstalować składników na jednym lub większej liczbie hostów

### <a name="error"></a>Błąd

"Instalator klastra nie może zainstalować składników na co najmniej jednym hoście. Spróbuj ponownie wykonać żądanie. "

### <a name="cause"></a>Przyczyna 

Zazwyczaj ten błąd jest generowany, gdy występuje problem przejściowy lub awaria platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Sprawdź na stronie [stanu platformy Azure](https://status.azure.com) ewentualne przestoje związane z platformą Azure, które mogą mieć wpływ na wdrożenie klastra. Jeśli nie ma przerwy w działaniu, ponów próbę wdrożenia klastra.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Kod błędu: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Błąd

Nie można nawiązać połączenia z punktem końcowym zarządzania klastrem. Spróbuj ponownie później.

### <a name="cause"></a>Przyczyna

Usługa HDInsight nie może nawiązać połączenia z klastrem podczas próby utworzenia klastra

### <a name="resolution"></a>Rozwiązanie

Jeśli używasz niestandardowej grupy zabezpieczeń sieci wirtualnej (sieciowych grup zabezpieczeń) i tras zdefiniowanych przez użytkownika (UDR), upewnij się, że klaster może komunikować się z usługami HDInsight Management. Aby uzyskać dodatkowe informacje, zobacz [adresy IP zarządzania usługą HDInsight](./hdinsight-management-ip-addresses.md).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Kod błędu: wdrożenia nie powiodły się z powodu naruszenia zasad: "zasób" <Resource URI> "jest niedozwolony przez zasady. Identyfikatory zasad: "[{" policyAssignment ": {" name ":" <Policy Name> "," ID ":"/Providers/Microsoft.Management/managementGroups/ <Management Group Name> providers/Microsoft. Authorization/policyAssignments/ <Policy Name> "}," policyDefinition ": <Policy Definition>

### <a name="cause"></a>Przyczyna

Zasady platformy Azure oparte na subskrypcjach mogą odmówić tworzenia publicznych adresów IP. Do utworzenia klastra usługi HDInsight wymagane są dwa publiczne adresy IP.

Następujące zasady mają zazwyczaj wpływ na tworzenie klastra:

* Zasady, które uniemożliwiają tworzenie adresów IP lub modułów równoważenia obciążenia w ramach subskrypcji.
* Zasady, które uniemożliwiają tworzenie kont magazynu.
* Zasady, które uniemożliwiają usuwanie zasobów sieciowych, takich jak adresy IP lub moduły równoważenia obciążenia.

### <a name="resolution"></a>Rozwiązanie

Usuń lub Wyłącz przypisanie Azure Policy opartego na subskrypcji podczas tworzenia klastra usługi HDInsight.

---

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów podczas tworzenia klastra, zobacz [Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md).