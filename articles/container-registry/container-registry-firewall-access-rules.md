---
title: Reguły dostępu zapory
description: Skonfiguruj reguły dostępu do usługi Azure Container Registry za zaporą, zezwalając na dostęp do ("listy dozwolonych") i nazw domen punktu końcowego usługi Storage oraz adresów IP specyficznych dla usług.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77168020"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą

W tym artykule wyjaśniono, jak skonfigurować reguły zapory, aby umożliwić dostęp do usługi Azure Container Registry. Na przykład urządzenie Azure IoT Edge za zaporą lub serwerem proxy może potrzebować dostępu do rejestru kontenerów w celu ściągnięcia obrazu kontenera. Serwer zablokowany w sieci lokalnej może być niezbędny do wypchnięcia obrazu.

Jeśli zamiast tego chcesz skonfigurować reguły dostępu do sieci dla ruchu przychodzącego w rejestrze kontenerów tylko w ramach sieci wirtualnej platformy Azure lub z zakresu publicznego adresu IP, zobacz [ograniczanie dostępu do usługi Azure Container Registry z sieci wirtualnej](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Informacje o punktach końcowych rejestru

Do ściągania lub wypychania obrazów lub innych artefaktów do usługi Azure Container Registry klient, taki jak demon Docker, musi korzystać z dwóch różnych punktów końcowych przy użyciu protokołu HTTPS.

* **Punkt końcowy interfejsu API REST usługi Registry** — operacje zarządzania uwierzytelnianiem i rejestrem są obsługiwane przez publiczny punkt końcowy interfejsu API REST rejestru. Ten punkt końcowy to nazwa serwera logowania rejestru lub skojarzony zakres adresów IP. 

* **Punkt końcowy magazynu** — platforma Azure [przypisuje magazyn obiektów BLOB](container-registry-storage.md) na kontach usługi Azure Storage w imieniu każdego rejestru w celu zarządzania danymi dla obrazów kontenerów i innych artefaktów. Gdy klient uzyskuje dostęp do warstw obrazu w usłudze Azure Container Registry, wysyła żądania przy użyciu punktu końcowego konta magazynu dostarczonego przez rejestr.

Jeśli rejestr jest [replikowany geograficznie](container-registry-geo-replication.md), klient może potrzebować współdziałania z punktami końcowymi REST i magazynem w określonym regionie lub w wielu replikowanych regionach.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Zezwalaj na dostęp do nazw domen REST i Storage

* **Punkt końcowy REST** — Zezwalanie na dostęp do w pełni kwalifikowanej nazwy serwera logowania rejestru, takiej jak`myregistry.azurecr.io`
* **Punkt końcowy magazynu (dane)** — Zezwalanie na dostęp do wszystkich kont usługi Azure Blob Storage za pomocą symbolu wieloznacznego`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Zezwalaj na dostęp według zakresu adresów IP

Jeśli organizacja ma zasady zezwalające na dostęp tylko do określonych adresów IP lub zakresów adresów, Pobierz [zakresy adresów IP i Tagi usług platformy Azure — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519).

Aby znaleźć zakresy adresów IP punktów końcowych usługi ACR REST, dla których należy zezwolić na dostęp, Wyszukaj **AzureContainerRegistry** w pliku JSON.

> [!IMPORTANT]
> Zakresy adresów IP dla usług platformy Azure mogą ulec zmianie, a aktualizacje są publikowane co tydzień. Regularnie Pobieraj plik JSON i wprowadź niezbędne aktualizacje w regułach dostępu. Jeśli scenariusz obejmuje skonfigurowanie reguł sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure w celu uzyskania dostępu Azure Container Registry, należy zamiast tego użyć [znacznika usługi](#allow-access-by-service-tag) **AzureContainerRegistry** .
>

### <a name="rest-ip-addresses-for-all-regions"></a>Adresy IP REST dla wszystkich regionów

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Adresy IP REST dla określonego regionu

Wyszukaj konkretny region, taki jak **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Adresy IP magazynu dla wszystkich regionów

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Adresy IP magazynu dla określonych regionów

Wyszukaj konkretny region, taki jak **Storage. AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Zezwalaj na dostęp według tagu usługi

W sieci wirtualnej platformy Azure Użyj reguł zabezpieczeń sieci do filtrowania ruchu z zasobów, takich jak maszyna wirtualna, do rejestru kontenerów. Aby uprościć tworzenie reguł sieci platformy Azure, użyj [znacznika usługi](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Tag usługi reprezentuje grupę prefiksów adresów IP, aby uzyskać dostęp do usługi platformy Azure globalnie lub dla regionu platformy Azure. Tag jest automatycznie aktualizowany, gdy adresy zmienią się. 

Można na przykład utworzyć regułę sieciowej grupy zabezpieczeń dla ruchu wychodzącego z **AzureContainerRegistryą** docelową, aby zezwolić na ruch do usługi Azure Container Registry. Aby zezwolić na dostęp do tagu usługi tylko w określonym regionie, Określ region w następującym formacie: **AzureContainerRegistry**. [*Nazwa regionu*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurowanie reguł zapory klienta dla MCR

Jeśli musisz uzyskać dostęp do programu Microsoft Container Registry (MCR) z poziomu zapory, zapoznaj się ze wskazówkami dotyczącymi konfigurowania [reguł zapory klienta MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR to podstawowy rejestr wszystkich obrazów platformy Docker opublikowanych przez firmę Microsoft, takich jak obrazy systemu Windows Server.

## <a name="next-steps"></a>Następne kroki

* Poznaj [najlepsze rozwiązania dotyczące platformy Azure dotyczące zabezpieczeń sieci](../security/fundamentals/network-best-practices.md)

* Dowiedz się więcej o [grupach zabezpieczeń](/azure/virtual-network/security-overview) w sieci wirtualnej platformy Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

