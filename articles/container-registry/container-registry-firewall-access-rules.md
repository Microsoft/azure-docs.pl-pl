---
title: Reguły dostępu zapory
description: Skonfiguruj reguły dostępu do rejestru kontenerów platformy Azure za zaporą, zezwalając na dostęp do interfejsu API REST i nazw domen punktów końcowych danych lub zakresów adresów IP specyficznych dla usługi.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935992"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą

W tym artykule wyjaśniono, jak skonfigurować reguły zapory, aby umożliwić dostęp do usługi Azure Container Registry. Na przykład urządzenie Azure IoT Edge za zaporą lub serwerem proxy może potrzebować dostępu do rejestru kontenerów w celu ściągnięcia obrazu kontenera. Serwer zablokowany w sieci lokalnej może być niezbędny do wypchnięcia obrazu.

Jeśli zamiast tego chcesz skonfigurować dostęp do sieci przychodzącej do rejestru kontenerów tylko w ramach sieci wirtualnej platformy Azure, zobacz [Konfigurowanie prywatnego linku platformy Azure dla usługi Azure Container Registry](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Informacje o punktach końcowych rejestru

Do ściągania lub wypychania obrazów lub innych artefaktów do usługi Azure Container Registry klient, taki jak demon Docker, musi korzystać z dwóch różnych punktów końcowych przy użyciu protokołu HTTPS. W przypadku klientów, którzy uzyskują dostęp do rejestru za zaporą, należy skonfigurować reguły dostępu dla obu punktów końcowych. Oba punkty końcowe są osiągane przez port 443.

* **Punkt końcowy interfejsu API REST usługi Registry** — operacje zarządzania uwierzytelnianiem i rejestrem są obsługiwane przez publiczny punkt końcowy interfejsu API REST rejestru. Ten punkt końcowy jest nazwą serwera logowania rejestru. Przykład: `myregistry.azurecr.io`

* **Punkt końcowy magazynu (dane)** — platforma Azure [przydziela magazyn obiektów BLOB](container-registry-storage.md) na kontach usługi Azure Storage w imieniu każdego rejestru w celu zarządzania danymi dla obrazów kontenerów i innych artefaktów. Gdy klient uzyskuje dostęp do warstw obrazu w usłudze Azure Container Registry, wysyła żądania przy użyciu punktu końcowego konta magazynu dostarczonego przez rejestr.

Jeśli rejestr jest [replikowany geograficznie](container-registry-geo-replication.md), klient może potrzebować współdziałania z punktem końcowym danych w określonym regionie lub w wielu replikowanych regionach.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Zezwalaj na dostęp do punktów końcowych REST i danych

* **Punkt końcowy REST** — Zezwalanie na dostęp do w pełni kwalifikowanej nazwy serwera logowania rejestru `<registry-name>.azurecr.io` lub SKOJARZONEGO zakresu adresów IP
* **Punkt końcowy magazynu (dane)** — umożliwia dostęp do wszystkich kont usługi Azure Blob Storage przy użyciu symbolu wieloznacznego `*.blob.core.windows.net` lub SKOJARZONEGO zakresu adresów IP.
> [!NOTE]
> Azure Container Registry wprowadza [dedykowane punkty końcowe danych](#enable-dedicated-data-endpoints), co pozwala na ścisłe Określanie reguł zapory klienta dla magazynu rejestru. Opcjonalnie można włączyć punkty końcowe danych we wszystkich regionach, w których znajduje się rejestr lub zreplikowane, przy użyciu formularza `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Zezwalaj na dostęp według zakresu adresów IP

Jeśli organizacja ma zasady zezwalające na dostęp tylko do określonych adresów IP lub zakresów adresów, Pobierz [zakresy adresów IP i Tagi usług platformy Azure — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519).

Aby znaleźć zakresy adresów IP punktów końcowych usługi ACR REST, dla których należy zezwolić na dostęp, Wyszukaj **AzureContainerRegistry** w pliku JSON.

> [!IMPORTANT]
> Zakresy adresów IP dla usług platformy Azure mogą ulec zmianie, a aktualizacje są publikowane co tydzień. Regularnie Pobieraj plik JSON i wprowadź niezbędne aktualizacje w regułach dostępu. Jeśli scenariusz obejmuje skonfigurowanie reguł sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure lub używasz zapory platformy Azure, zamiast tego użyj  [znacznika usługi](#allow-access-by-service-tag) AzureContainerRegistry.
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

W sieci wirtualnej platformy Azure Użyj reguł zabezpieczeń sieci do filtrowania ruchu z zasobów, takich jak maszyna wirtualna, do rejestru kontenerów. Aby uprościć tworzenie reguł sieci platformy Azure, użyj [znacznika usługi](../virtual-network/network-security-groups-overview.md#service-tags) **AzureContainerRegistry** . Tag usługi reprezentuje grupę prefiksów adresów IP, aby uzyskać dostęp do usługi platformy Azure globalnie lub dla regionu platformy Azure. Tag jest automatycznie aktualizowany, gdy adresy zmienią się. 

Można na przykład utworzyć regułę sieciowej grupy zabezpieczeń dla ruchu wychodzącego z **AzureContainerRegistryą** docelową, aby zezwolić na ruch do usługi Azure Container Registry. Aby zezwolić na dostęp do tagu usługi tylko w określonym regionie, Określ region w następującym formacie: **AzureContainerRegistry**. [*Nazwa regionu*].

## <a name="enable-dedicated-data-endpoints"></a>Włączanie dedykowanych punktów końcowych danych 

> [!WARNING]
> Jeśli wcześniej skonfigurowano dostęp zapory klienta do istniejących `*.blob.core.windows.net` punktów końcowych, przełączenie do dedykowanych punktów końcowych danych będzie miało wpływ na łączność klienta, co powoduje błędy ściągnięcia. Aby zapewnić klientom spójny dostęp, Dodaj nowe reguły punktu końcowego danych do reguł zapory klienta. Po zakończeniu Włącz dedykowane punkty końcowe danych dla rejestrów przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi.

Dedykowane punkty końcowe danych to opcjonalna funkcja warstwy kontenera usługi **Premium** Registry. Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md). 

Dedykowane punkty końcowe danych można włączyć przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure. Punkty końcowe danych są zgodne ze wzorcem regionalnym `<registry-name>.<region>.data.azurecr.io` . W rejestrze z replikacją geograficzną włączenie punktów końcowych danych umożliwia korzystanie z punktów końcowych we wszystkich regionach replik.

### <a name="portal"></a>Portal

Aby włączyć punkty końcowe danych przy użyciu portalu:

1. Przejdź do rejestru kontenerów.
1. Wybierz pozycję **Sieć**  >  **dostęp publiczny**.
1. Zaznacz pole wyboru **Włącz dedykowany punkt końcowy danych** .
1. Wybierz pozycję **Zapisz**.

Punkt końcowy danych lub punkty końcowe pojawiają się w portalu.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Dedykowane punkty końcowe danych w portalu":::

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby włączyć punkty końcowe danych przy użyciu interfejsu wiersza polecenia platformy Azure, użyj interfejsu wiersza polecenia platformy Azure w wersji 2.4.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Następujące polecenie [AZ ACR Update][az-acr-update] umożliwia korzystanie z dedykowanych punktów końcowych danych *w rejestrze rejestru.* 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Aby wyświetlić punkty końcowe danych, użyj polecenia [AZ ACR show-Endpoints][az-acr-show-endpoints] :

```azurecli
az acr show-endpoints --name myregistry
```

Dane wyjściowe dla celów demonstracyjnych pokazują dwa regionalne punkty końcowe

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Po skonfigurowaniu dedykowanych punktów końcowych danych dla rejestru można włączyć reguły dostępu zapory klienta dla punktów końcowych danych. Włącz reguły dostępu do punktów końcowych dla wszystkich wymaganych regionów rejestru.

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurowanie reguł zapory klienta dla MCR

Jeśli musisz uzyskać dostęp do programu Microsoft Container Registry (MCR) z poziomu zapory, zapoznaj się ze wskazówkami dotyczącymi konfigurowania [reguł zapory klienta MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR to podstawowy rejestr wszystkich obrazów platformy Docker opublikowanych przez firmę Microsoft, takich jak obrazy systemu Windows Server.

## <a name="next-steps"></a>Następne kroki

* Poznaj [najlepsze rozwiązania dotyczące platformy Azure dotyczące zabezpieczeń sieci](../security/fundamentals/network-best-practices.md)

* Dowiedz się więcej o [grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md) w sieci wirtualnej platformy Azure

* Dowiedz się więcej o konfigurowaniu [prywatnego linku](container-registry-private-link.md) do rejestru kontenerów

* Dowiedz się więcej na temat [dedykowanych punktów końcowych danych](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) dla Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints