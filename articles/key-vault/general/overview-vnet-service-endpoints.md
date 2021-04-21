---
title: Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault
description: Dowiedz się, jak punkty końcowe usługi dla sieci Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej, w tym do scenariuszy użycia.
services: key-vault
author: amitbapat
ms.author: ambapat
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 985cbe1128d1dd64fda86ef062750dc5dd068ffe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751737"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault

Punkty końcowe usługi dla sieci wirtualnej Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokół internetowy w wersji 4). Każdy użytkownik łączący się z magazynem kluczy spoza tych źródeł ma odmówiony dostępu.

Istnieje jeden ważny wyjątek od tego ograniczenia. Jeśli użytkownik wybrał opcję zezwalania na dostęp zaufany usługi firmy Microsoft, połączenia z tych usług są przepuszczane przez zaporę. Na przykład te usługi obejmują usługi Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager i Azure Backup. Tacy użytkownicy nadal muszą przedstawić prawidłowy token Azure Active Directory i muszą mieć uprawnienia (skonfigurowane jako zasady dostępu), aby wykonać żądaną operację. Aby uzyskać więcej informacji, zobacz [Virtual network service endpoints (Punkty końcowe usługi dla sieci wirtualnej).](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="usage-scenarios"></a>Scenariusze użycia

Możesz skonfigurować zapory [Key Vault wirtualne,](network-security.md) aby domyślnie odmawiać dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego). Możesz udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure i zakresów publicznych internetowych adresów IP, umożliwiając tworzenie bezpiecznych granic sieci dla aplikacji.

> [!NOTE]
> Key Vault zapory i reguły sieci wirtualnej dotyczą tylko [płaszczyzny danych](security-overview.md#privileged-access) Key Vault. Key Vault operacji płaszczyzny sterowania (takich jak tworzenie, usuwanie i modyfikowanie operacji, ustawianie zasad dostępu, ustawianie zapór i reguł sieci wirtualnej oraz wdrażanie wpisów tajnych lub kluczy za pomocą szablonów usługi ARM) nie mają wpływu na zapory i reguły sieci wirtualnej.

Oto kilka przykładów użycia punktów końcowych usługi:

* Używasz usługi Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów, a chcesz zablokować dostęp do magazynu kluczy z publicznego Internetu.
* Chcesz zablokować dostęp do magazynu kluczy, aby tylko aplikacja lub krótka lista wyznaczonych hostów łączyła się z magazynem kluczy.
* Twoja aplikacja działa w sieci wirtualnej platformy Azure i ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi nawiązać połączenie z Key Vault, aby pobrać wpisy tajne lub certyfikaty, lub użyć kluczy kryptograficznych.

## <a name="trusted-services"></a>Zaufane usługi

Poniżej znajduje się lista zaufanych usług, które mogą uzyskać dostęp do magazynu kluczy, jeśli opcja **Zezwalaj na** zaufane usługi jest włączona.

|Zaufana usługa|Obsługiwane scenariusze użycia|
| --- | --- |
|Usługa wdrażania Virtual Machines Azure|[Wdrażanie certyfikatów na maszynach wirtualnych z zarządzanych przez klienta Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager wdrażania szablonu|[Przekaż bezpieczne wartości podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption usługi szyfrowania woluminów|Zezwalaj na dostęp do klucza funkcji BitLocker (maszyny wirtualnej z systemem Windows) lub hasła DM (maszyna wirtualna z systemem Linux) i klucza szyfrowania klucza podczas wdrażania maszyny wirtualnej. Umożliwia to [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Zezwalaj na tworzenie kopii zapasowych i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii Virtual Machines Azure przy użyciu [Azure Backup](../../backup/backup-overview.md).|
|Exchange Online & SharePoint Online|Zezwalaj na dostęp do klucza klienta dla usługi Azure szyfrowanie usługi Storage [kluczem klienta.](/microsoft-365/compliance/customer-key-overview)|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy [dla Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Wdrażanie certyfikatu aplikacji internetowej platformy Azure za pośrednictwem Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption z Bring Your Own Key obsługą Azure SQL Database i Azure Synapse Analytics.](../../azure-sql/database/transparent-data-encryption-byok-overview.md)|
|Azure Storage|[szyfrowanie usługi Storage kluczy zarządzanych przez klienta w programie Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Szyfrowanie danych w usłudze Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) przy użyciu klucza zarządzanego przez klienta.|
|Azure Synapse Analytics|[Szyfrowanie danych przy użyciu kluczy zarządzanych przez klienta w Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Szybka, łatwa i oparta na Apache Spark oparta na współpracy usługa analizy](/azure/databricks/scenarios/what-is-azure-databricks)|
|Usługa Azure API Management|[Wdrażanie certyfikatów dla Custom Domain z Key Vault przy użyciu msi](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Pobieranie poświadczeń magazynu danych w Key Vault z Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Scenariusz zezwalania na dostęp do magazynu kluczy dla kluczy zarządzanych przez klienta](../../event-hubs/configure-customer-managed-key.md)|
|Usługa Azure Service Bus|[Scenariusz zezwalania na dostęp do magazynu kluczy dla kluczy zarządzanych przez klienta](../../service-bus-messaging/configure-customer-managed-key.md)|
|Usługa Azure Import/Export| [Używanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault dla usługi Import/Export](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Szyfrowanie rejestru przy użyciu kluczy zarządzanych przez klienta](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Używanie Key Vault dla odbiorników z włączoną obsługą protokołu HTTPS](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> Należy skonfigurować odpowiednie zasady dostępu Key Vault, aby umożliwić odpowiednim usługom uzyskiwanie dostępu do Key Vault.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać instrukcje krok po kroku, zobacz [Configure Azure Key Vault firewalls and virtual networks (Konfigurowanie zapór i](network-security.md) sieci wirtualnych)
- Zobacz omówienie [Azure Key Vault zabezpieczeń](security-overview.md)
