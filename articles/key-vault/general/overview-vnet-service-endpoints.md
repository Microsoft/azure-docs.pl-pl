---
title: Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej, w tym scenariuszy użycia.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 9cbce00e2c2743aec57cd857b6f38d20bce33698
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532911"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault

Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokołu internetowego w wersji 4). Każdy użytkownik nawiązujący połączenie z magazynem kluczy spoza tych źródeł nie odmówi dostępu.

Istnieje jeden ważny wyjątek dla tego ograniczenia. Jeśli Użytkownik zezwolił na dostęp do zaufanych usług firmy Microsoft, połączenia z tych usług będą przełączane przez zaporę. Na przykład te usługi obejmują pakiet Office 365 Exchange Online, pakiet Office 365 SharePoint Online, usługa Azure COMPUTE, Azure Resource Manager i Azure Backup. Ci użytkownicy nadal muszą przedstawić prawidłowy token Azure Active Directory i muszą mieć uprawnienia (skonfigurowane jako zasady dostępu), aby wykonać żądaną operację. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenariusze użycia

Można skonfigurować [zapory Key Vault i sieci wirtualne](network-security.md) , aby odmówić dostępu do ruchu ze wszystkich sieci (łącznie z ruchem internetowym). Można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure i publicznych zakresów adresów IP, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

> [!NOTE]
> Key Vault zapory i reguły sieci wirtualnej stosują się tylko do [płaszczyzny danych](secure-your-key-vault.md#data-plane-access-control) Key Vault. Nie mają one wpływ na działania dotyczące płaszczyzny sterowania Key Vault (takie jak tworzenie, usuwanie i modyfikowanie operacji, Ustawianie zasad dostępu, Ustawianie zapór i reguł sieci wirtualnej).

Poniżej przedstawiono kilka przykładów użycia punktów końcowych usługi:

* Używasz Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów i chcesz zablokować dostęp do magazynu kluczy z publicznej sieci Internet.
* Chcesz zablokować dostęp do magazynu kluczy, tak aby tylko aplikacja lub krótka lista wystawianych hostów mogła połączyć się z magazynem kluczy.
* Aplikacja działa w sieci wirtualnej platformy Azure, a ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi nawiązać połączenie z usługą Key Vault, aby pobrać klucze tajne lub certyfikaty lub użyć kluczy kryptograficznych.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór i sieci wirtualnych usługi Key Vault

Poniżej przedstawiono kroki wymagane do skonfigurowania zapór i sieci wirtualnych. Te kroki mają zastosowanie, niezależnie od tego, czy używasz programu PowerShell, interfejsu wiersza polecenia platformy Azure, czy Azure Portal.

1. Włącz [rejestrowanie Key Vault](logging.md) , aby wyświetlić szczegółowe dzienniki dostępu. Pomaga to w diagnostyce, gdy zapory i reguły sieci wirtualnej uniemożliwiają dostęp do magazynu kluczy. (Ten krok jest opcjonalny, ale zdecydowanie zalecany).
2. Włącz **punkty końcowe usługi dla magazynu kluczy** dla docelowych sieci wirtualnych i podsieci.
3. Ustawianie zapór i reguł sieci wirtualnej dla magazynu kluczy w celu ograniczenia dostępu do tego magazynu kluczy z określonych sieci wirtualnych, podsieci i zakresów adresów IPv4.
4. Jeśli ten magazyn kluczy musi być dostępny dla wszystkich zaufanych usług firmy Microsoft, Włącz opcję zezwalania **zaufanym usługom platformy Azure** na łączenie się z usługą Key Vault.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](network-security.md).

> [!IMPORTANT]
> Po zastosowaniu reguł zapory użytkownicy mogą wykonywać Key Vault operacje [płaszczyzny danych](secure-your-key-vault.md#data-plane-access-control) tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do Key Vault z Azure Portal. Mimo że użytkownicy mogą przechodzić do magazynu kluczy z Azure Portal, mogą nie być w stanie wyświetlać kluczy, wpisów tajnych ani certyfikatów, jeśli ich maszyny klienckie nie znajdują się na liście dozwolonych. Ma to również wpływ na wybór Key Vault przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają ich komputerom klienckim.


> [!NOTE]
> Należy pamiętać o następujących ograniczeniach konfiguracji:
> * Dozwolone są maksymalnie 127 reguły sieci wirtualnej i 127. 
> * Małe zakresy adresów, które używają rozmiarów prefiksu "/31" lub "/32", nie są obsługiwane. Zamiast tego należy skonfigurować te zakresy przy użyciu poszczególnych reguł adresów IP.
> * Reguły sieci IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od **10.**, **172.16-31** i **192,168.** 
> * W tej chwili są obsługiwane tylko adresy IPv4.

## <a name="trusted-services"></a>Usługi zaufane

Poniżej znajduje się lista zaufanych usług, które mogą uzyskiwać dostęp do magazynu kluczy, jeśli jest włączona opcja **Zezwalaj na zaufane usługi** .

|Usługa zaufana|Obsługiwane scenariusze użycia|
| --- | --- |
|Usługa wdrażania Virtual Machines platformy Azure|[Wdróż certyfikaty na maszynach wirtualnych z poziomu Key Vault zarządzanych przez klienta](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Usługa wdrażania szablonów Azure Resource Manager|[Przekazywanie bezpiecznych wartości podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Jednostka SKU platformy Azure Application Gateway v2|[Zakończenie protokołu TLS z certyfikatami Key Vault](../../application-gateway/key-vault-certs.md)|
|Azure Disk Encryption usługi szyfrowania woluminów|Zezwalaj na dostęp do klucza funkcji BitLocker (maszyna wirtualna z systemem Windows) lub hasła DM (maszyna wirtualna z systemem Linux) oraz klucz szyfrowania klucza podczas wdrażania maszyn wirtualnych. Umożliwia to [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Zezwalaj na wykonywanie kopii zapasowych i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii zapasowej w usłudze Azure Virtual Machines przy użyciu [Azure Backup](../../backup/backup-overview.md).|
|Usługa Exchange Online & SharePoint Online|Zezwalaj na dostęp do klucza klienta dla usługi Azure szyfrowanie usługi Storage przy użyciu [klucza klienta](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy dla [Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Wdróż certyfikat aplikacji sieci Web platformy Azure za pomocą Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption z obsługą Bring Your Own Key dla Azure SQL Database i usługi Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Szyfrowanie danych w Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) z kluczem zarządzanym przez klienta.|
|Azure Databricks|[Szybka, łatwa i wydajna usługa analizy oparta na Apache Sparkach](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Wdrażanie certyfikatów dla domeny niestandardowej z Key Vault przy użyciu pliku MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Pobieranie poświadczeń magazynu danych w Key Vault z Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Zezwalaj na dostęp do magazynu kluczy dla scenariusza kluczy zarządzanych przez klienta](../../event-hubs/configure-customer-managed-key.md)|
|Usługa Azure Service Bus|[Zezwalaj na dostęp do magazynu kluczy dla scenariusza kluczy zarządzanych przez klienta](../../service-bus-messaging/configure-customer-managed-key.md)|
|Usługa Azure Import/Export| [Korzystanie z kluczy zarządzanych przez klienta w Azure Key Vault dla usługi Import/Export](../../storage/common/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Szyfrowanie rejestru przy użyciu kluczy zarządzanych przez klienta](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> Należy skonfigurować odpowiednie zasady dostępu Key Vault, aby umożliwić odpowiednim usługom uzyskanie dostępu do Key Vault.

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie magazynu kluczy](secure-your-key-vault.md)
* [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](network-security.md)
