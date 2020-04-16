---
title: Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Omówienie punktów końcowych usługi sieci wirtualnej dla usługi Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 2a68a50a5d15b9f38407c19494a39a14abfa0a5a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432075"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault

Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów Protokołu IPv4 (protokół internetowy w wersji 4). Każdemu użytkownikowi łączącemu się z magazynem kluczy spoza tych źródeł odmówiono dostępu.

Istnieje jeden ważny wyjątek od tego ograniczenia. Jeśli użytkownik wyraził zgodę na zezwalanie na zaufane usługi firmy Microsoft, połączenia z tych usług są przepuszczane przez zaporę. Na przykład te usługi obejmują usługę Office 365 Exchange Online, usługę Office 365 SharePoint Online, obliczenia platformy Azure, usługę Azure Resource Manager i usługę Azure Backup. Tacy użytkownicy nadal muszą przedstawić prawidłowy token usługi Azure Active Directory i muszą mieć uprawnienia (skonfigurowane jako zasady dostępu) do wykonania żądanej operacji. Aby uzyskać więcej informacji, zobacz [Punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenariusze użytkowania

Zapory [usługi Key Vault i sieci wirtualne](network-security.md) można skonfigurować w celu domyślnego odmawiania dostępu do ruchu ze wszystkich sieci (w tym ruchu internetowego). Można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure i publicznych zakresów adresów IP w Internecie, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

> [!NOTE]
> Zapory magazynu kluczy i reguły sieci wirtualnej mają zastosowanie tylko do [płaszczyzny danych](secure-your-key-vault.md#data-plane-access-control) usługi Key Vault. Zapory i reguły sieci wirtualnej nie mają wpływu na operacje związane z tworzeniem, usuwaniem i modyfikowania, ustawianiem zasad dostępu, ustawianiem zapór i regułami sieci wirtualnej).

Oto kilka przykładów użycia punktów końcowych usługi:

* Usługa Key Vault jest obsługiwana do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów oraz do blokowania dostępu do magazynu kluczy z publicznego Internetu.
* Chcesz zablokować dostęp do magazynu kluczy, aby tylko aplikacja lub krótka lista wyznaczonych hostów mogła połączyć się z magazynem kluczy.
* Masz aplikację uruchomiona w sieci wirtualnej platformy Azure, a ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi połączyć się z magazynem kluczy, aby pobrać wpisy tajne lub certyfikaty lub użyć kluczy kryptograficznych.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór i sieci wirtualnych usługi Key Vault

Poniżej przedstawiono kroki wymagane do skonfigurowania zapór i sieci wirtualnych. Te kroki dotyczą niezależnie od tego, czy używasz programu PowerShell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal.

1. Włącz [rejestrowanie magazynu kluczy](logging.md)), aby wyświetlić szczegółowe dzienniki dostępu. Pomaga to w diagnostyce, gdy zapory i reguły sieci wirtualnej uniemożliwiają dostęp do magazynu kluczy. (Ten krok jest opcjonalny, ale wysoce zalecane.)
2. Włącz **punkty końcowe usługi dla magazynu kluczy** dla docelowych sieci wirtualnych i podsieci.
3. Ustaw zapory i reguły sieci wirtualnej dla magazynu kluczy, aby ograniczyć dostęp do tego magazynu kluczy z określonych sieci wirtualnych, podsieci i zakresów adresów IPv4.
4. Jeśli ta przechowalnia kluczy musi być dostępna dla zaufanych usług firmy Microsoft, włącz opcję **zezwalania zaufanym usługom platformy Azure** na łączenie się z usługą Key Vault.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych](network-security.md).

> [!IMPORTANT]
> Po wprowadzeniu reguł zapory użytkownicy mogą wykonywać operacje [na płaszczyznach danych](secure-your-key-vault.md#data-plane-access-control) usługi Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do usługi Key Vault z witryny Azure portal. Mimo że użytkownicy mogą przeglądać do magazynu kluczy z witryny Azure portal, mogą nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli ich komputer kliencki nie znajduje się na liście dozwolonych. Dotyczy to również selektora magazynu kluczy przez inne usługi platformy Azure. Użytkownicy mogą być w stanie zobaczyć listę magazynów kluczy, ale nie klucze listy, jeśli reguły zapory uniemożliwiają ich komputera klienckiego.


> [!NOTE]
> Należy pamiętać o następujących ograniczeniach konfiguracji:
> * Dozwolone jest maksymalnie 127 reguł sieci wirtualnej i 127 reguł IPv4. 
> * Małe zakresy adresów, które używają rozmiarów prefiksów "/31" lub "/32", nie są obsługiwane. Zamiast tego należy skonfigurować te zakresy przy użyciu reguł poszczególnych adresów IP.
> * Reguły sieci IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w RFC 1918) nie są dozwolone w regułach IP. Sieci prywatne obejmują adresy zaczynające się od **10.**, **172.16-31**i **192.168.**. 
> * W tej chwili obsługiwane są tylko adresy IPv4.

## <a name="trusted-services"></a>Zaufane usługi

Oto lista zaufanych usług, które mogą uzyskać dostęp do magazynu kluczy, jeśli opcja **Zezwalaj na zaufane usługi** jest włączona.

|Zaufana usługa|Obsługiwane scenariusze użycia|
| --- | --- |
|Usługa wdrażania maszyn wirtualnych platformy Azure|[Wdrażanie certyfikatów na maszynach wirtualnych z usługi Key Vault zarządzanej przez klienta](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Usługa wdrażania szablonów usługi Azure Resource Manager|[Przekazywanie bezpiecznych wartości podczas wdrażania](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Usługa szyfrowania woluminów szyfrowania dysków Azure|Zezwalaj na dostęp do klucza funkcją BitLocker (Maszyny Wirtualnej systemu Windows) lub dm passphrase (Linux VM) i klucza szyfrowania klucza podczas wdrażania maszyny wirtualnej. Umożliwia to [szyfrowanie dysków platformy Azure](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Zezwalaj na tworzenie kopii zapasowych i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure przy użyciu [usługi Azure Backup](../../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePoint Online|Zezwól na dostęp do klucza klienta dla szyfrowania usługi Azure Storage z [kluczem klienta](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy dla [usługi Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Wdrażanie certyfikatu aplikacji sieci Web platformy Azure za pośrednictwem usługi Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Przezroczyste szyfrowanie danych z obsługą bring your own key dla usługi Azure SQL Database and Data Warehouse](../../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Szyfrowanie usługi magazynu przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Szyfrowanie danych w sklepie Usługi Azure Data Lake za](../../data-lake-store/data-lake-store-encryption.md) pomocą klucza zarządzanego przez klienta.|
|Azure Databricks|[Szybka, łatwa i oparta na współpracy usługa analityczna Oparta na apache Spark](../../azure-databricks/what-is-azure-databricks.md)|
|Usługa Azure API Management|[Wdrażanie certyfikatów dla domeny niestandardowej z usługi Key Vault przy użyciu usługi MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Pobieranie poświadczeń magazynu danych w magazynie kluczy z fabryki danych](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Zezwalaj na dostęp do magazynu kluczy dla scenariusza kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Zezwalaj na dostęp do magazynu kluczy dla scenariusza kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Usługa Azure Import/Export| [Używanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault do importowania/eksportowania usługi](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)

> [!NOTE]
> Należy skonfigurować odpowiednie zasady dostępu usługi Key Vault, aby umożliwić odpowiednim usługom uzyskanie dostępu do usługi Key Vault.

## <a name="next-steps"></a>Następne kroki

* [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md))
* [Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych](network-security.md)
