---
title: Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault
description: Dowiedz się, jak punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej, w tym scenariuszy użycia.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 36a94f780fa5c196e28730c051e161633ed3ee3d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431061"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault

Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokołu internetowego w wersji 4). Każdy użytkownik nawiązujący połączenie z magazynem kluczy spoza tych źródeł nie odmówi dostępu.

Istnieje jeden ważny wyjątek dla tego ograniczenia. Jeśli Użytkownik zezwolił na dostęp do zaufanych usług firmy Microsoft, połączenia z tych usług będą przełączane przez zaporę. Na przykład te usługi obejmują pakiet Office 365 Exchange Online, pakiet Office 365 SharePoint Online, usługa Azure COMPUTE, Azure Resource Manager i Azure Backup. Ci użytkownicy nadal muszą przedstawić prawidłowy token Azure Active Directory i muszą mieć uprawnienia (skonfigurowane jako zasady dostępu), aby wykonać żądaną operację. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenariusze użycia

Można skonfigurować [zapory Key Vault i sieci wirtualne](network-security.md) , aby odmówić dostępu do ruchu ze wszystkich sieci (łącznie z ruchem internetowym). Można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure i publicznych zakresów adresów IP, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

> [!NOTE]
> Key Vault zapory i reguły sieci wirtualnej stosują się tylko do [płaszczyzny danych](secure-your-key-vault.md#data-plane-access-control) Key Vault. Nie mają one wpływ na działania zapór i reguły sieci wirtualnej, Key Vault w wyniku operacji na płaszczyźnie kontroli (takich jak tworzenie, usuwanie i modyfikowanie operacji, Ustawianie zasad dostępu, Ustawianie zapór i reguł sieci wirtualnej oraz wdrażanie kluczy tajnych lub klucze za pomocą szablonów ARM).

Poniżej przedstawiono kilka przykładów użycia punktów końcowych usługi:

* Używasz Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów i chcesz zablokować dostęp do magazynu kluczy z publicznej sieci Internet.
* Chcesz zablokować dostęp do magazynu kluczy, tak aby tylko aplikacja lub krótka lista wystawianych hostów mogła połączyć się z magazynem kluczy.
* Aplikacja działa w sieci wirtualnej platformy Azure, a ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi nawiązać połączenie z usługą Key Vault, aby pobrać klucze tajne lub certyfikaty lub użyć kluczy kryptograficznych.

## <a name="trusted-services"></a>Usługi zaufane

Poniżej znajduje się lista zaufanych usług, które mogą uzyskiwać dostęp do magazynu kluczy, jeśli jest włączona opcja **Zezwalaj na zaufane usługi** .

|Usługa zaufana|Obsługiwane scenariusze użycia|
| --- | --- |
|Usługa wdrażania Virtual Machines platformy Azure|[Wdróż certyfikaty na maszynach wirtualnych z poziomu Key Vault zarządzanych przez klienta](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Usługa wdrażania szablonów Azure Resource Manager|[Przekazywanie bezpiecznych wartości podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption usługi szyfrowania woluminów|Zezwalaj na dostęp do klucza funkcji BitLocker (maszyna wirtualna z systemem Windows) lub hasła DM (maszyna wirtualna z systemem Linux) oraz klucz szyfrowania klucza podczas wdrażania maszyn wirtualnych. Umożliwia to [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Zezwalaj na wykonywanie kopii zapasowych i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii zapasowej w usłudze Azure Virtual Machines przy użyciu [Azure Backup](../../backup/backup-overview.md).|
|Usługa Exchange Online & SharePoint Online|Zezwalaj na dostęp do klucza klienta dla usługi Azure szyfrowanie usługi Storage przy użyciu [klucza klienta](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy dla [Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Wdróż certyfikat aplikacji sieci Web platformy Azure za pomocą Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption z obsługą Bring Your Own Key dla Azure SQL Database i usługi Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&preserve-view=true&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Szyfrowanie danych w Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) z kluczem zarządzanym przez klienta.|
|Azure Synapse Analytics|[Szyfrowanie danych przy użyciu kluczy zarządzanych przez klienta w Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Szybka, łatwa i wydajna usługa analizy oparta na Apache Sparkach](/azure/databricks/scenarios/what-is-azure-databricks)|
|Usługa Azure API Management|[Wdrażanie certyfikatów dla domeny niestandardowej z Key Vault przy użyciu pliku MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Pobieranie poświadczeń magazynu danych w Key Vault z Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Zezwalaj na dostęp do magazynu kluczy dla scenariusza kluczy zarządzanych przez klienta](../../event-hubs/configure-customer-managed-key.md)|
|Usługa Azure Service Bus|[Zezwalaj na dostęp do magazynu kluczy dla scenariusza kluczy zarządzanych przez klienta](../../service-bus-messaging/configure-customer-managed-key.md)|
|Usługa Azure Import/Export| [Korzystanie z kluczy zarządzanych przez klienta w Azure Key Vault dla usługi Import/Export](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Szyfrowanie rejestru przy użyciu kluczy zarządzanych przez klienta](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> Należy skonfigurować odpowiednie zasady dostępu Key Vault, aby umożliwić odpowiednim usługom uzyskanie dostępu do Key Vault.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](network-security.md)
- Zobacz [Omówienie zabezpieczeń Azure Key Vault](security-overview.md)
