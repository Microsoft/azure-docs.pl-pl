---
title: Reguły dostępu zapory
description: Skonfiguruj reguły dostępu do rejestru kontenerów platformy Azure zza zapory, zezwalając na dostęp do interfejsu API REST i nazw domen punktu końcowego danych lub zakresów adresów IP specyficznych dla usługi.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 6aea4415468eb21e8d010b74597fc68e4ebf573f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783939"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurowanie reguł dostępu do rejestru kontenerów platformy Azure za zaporą

W tym artykule wyjaśniono, jak skonfigurować reguły w zaporze, aby zezwolić na dostęp do rejestru kontenerów platformy Azure. Na przykład Azure IoT Edge za zaporą lub serwerem proxy może być konieczne uzyskanie dostępu do rejestru kontenerów w celu ściągnięcie obrazu kontenera. Lub zablokowany serwer w sieci lokalnej może wymagać dostępu do wypychania obrazu.

Jeśli zamiast tego chcesz skonfigurować dostęp sieciowy dla ruchu przychodzącego do rejestru kontenerów tylko w ramach sieci wirtualnej platformy Azure, zobacz Configure Azure Private Link for an Azure container registry (Konfigurowanie dostępu Azure Private Link dla rejestru [kontenerów platformy Azure).](container-registry-private-link.md)

## <a name="about-registry-endpoints"></a>Informacje o punktach końcowych rejestru

Aby ściągać lub wypychać obrazy lub inne artefakty do rejestru kontenerów platformy Azure, klient, taki jak demon platformy Docker, musi wchodzić w interakcje za pośrednictwem protokołu HTTPS z dwoma oddzielnymi punktami końcowymi. W przypadku klientów, którzy mają dostęp do rejestru zza zapory, należy skonfigurować reguły dostępu dla obu punktów końcowych. Oba punkty końcowe są dostępne za pośrednictwem portu 443.

* **Punkt końcowy interfejsu API REST rejestru** — operacje uwierzytelniania i zarządzania rejestrem są obsługiwane za pośrednictwem publicznego punktu końcowego interfejsu API REST rejestru. Ten punkt końcowy jest nazwą serwera logowania rejestru. Przykład: `myregistry.azurecr.io`

* **Punkt końcowy magazynu (danych)** — platforma Azure przydziela magazyn [obiektów blob](container-registry-storage.md) na kontach usługi Azure Storage w imieniu każdego rejestru w celu zarządzania danymi dla obrazów kontenerów i innych artefaktów. Gdy klient uzyskuje dostęp do warstw obrazu w rejestrze kontenerów platformy Azure, wysyła żądania przy użyciu punktu końcowego konta magazynu dostarczonego przez rejestr.

Jeśli rejestr jest [replikowany geograficznie,](container-registry-geo-replication.md)klient może wymagać interakcji z punktem końcowym danych w określonym regionie lub w wielu zreplikowanych regionach.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Zezwalaj na dostęp do usługi REST i punktów końcowych danych

* **Punkt końcowy REST** — umożliwia dostęp do w pełni kwalifikowanej nazwy serwera logowania rejestru lub `<registry-name>.azurecr.io` skojarzonego zakresu adresów IP
* **Punkt końcowy magazynu (danych)** — umożliwia dostęp do wszystkich kont usługi Azure Blob Storage przy użyciu symbolu wieloznacznego `*.blob.core.windows.net` lub skojarzonego zakresu adresów IP.
> [!NOTE]
> Azure Container Registry wprowadzono [dedykowane](#enable-dedicated-data-endpoints)punkty końcowe danych, dzięki czemu można ściśle ujednolić zakres reguł zapory klienta dla magazynu rejestru. Opcjonalnie możesz włączyć punkty końcowe danych we wszystkich regionach, w których znajduje się lub replikowany rejestr, przy użyciu formularza `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Zezwalaj na dostęp według zakresu adresów IP

Jeśli Twoja organizacja ma zasady zezwalania na dostęp tylko do określonych adresów IP lub zakresów adresów, pobierz zakresy adresów IP platformy Azure i tagi usług [— chmura publiczna.](https://www.microsoft.com/download/details.aspx?id=56519)

Aby znaleźć zakresy adresów IP punktu końcowego REST usługi ACR, dla których musisz zezwolić na dostęp, wyszukaj pozycję **AzureContainerRegistry** w pliku JSON.

> [!IMPORTANT]
> Zakresy adresów IP dla usług platformy Azure mogą ulec zmianie, a aktualizacje są publikowane co tydzień. Regularnie pobieraj plik JSON i dopowiądaj niezbędne aktualizacje w swoich zasadach dostępu. Jeśli twój scenariusz obejmuje konfigurowanie reguł sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure lub używasz Azure Firewall, zamiast tego użyj [tagu](#allow-access-by-service-tag) usługi **AzureContainerRegistry.**
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

Wyszukaj określony region, taki jak **AzureContainerRegistry.AustraliaEast.**

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

Wyszukaj określony region, na przykład **Storage.AustraliaCentral.**

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

W sieci wirtualnej platformy Azure użyj reguł zabezpieczeń sieci, aby filtrować ruch z zasobu, takiego jak maszyna wirtualna, do rejestru kontenerów. Aby uprościć tworzenie reguł sieci platformy Azure, użyj tagu usługi **AzureContainerRegistry.** [](../virtual-network/network-security-groups-overview.md#service-tags) Tag usługi reprezentuje grupę prefiksów adresów IP w celu uzyskania dostępu do usługi platformy Azure globalnie lub dla każdego regionu świadczenia usługi Azure. Tag jest automatycznie aktualizowany po zmianie adresów. 

Na przykład utwórz regułę sieciowej grupy zabezpieczeń dla ruchu wychodzącego z docelową usługą **AzureContainerRegistry,** aby zezwolić na ruch do rejestru kontenerów platformy Azure. Aby zezwolić na dostęp do tagu usługi tylko w określonym regionie, określ region w następującym formacie: **AzureContainerRegistry**. [*nazwa regionu*].

## <a name="enable-dedicated-data-endpoints"></a>Włączanie dedykowanych punktów końcowych danych 

> [!WARNING]
> Jeśli wcześniej skonfigurowano dostęp zapory klienta do istniejących punktów końcowych, przełączenie na dedykowane punkty końcowe danych będzie miało wpływ na łączność `*.blob.core.windows.net` klienta, powodując błędy ściągania. Aby zapewnić klientom spójny dostęp, dodaj nowe reguły punktu końcowego danych do reguł zapory klienta. Po zakończeniu włącz dedykowane punkty końcowe danych dla rejestrów przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi.

Dedykowane punkty końcowe danych to opcjonalna funkcja warstwy **usługi rejestru** kontenerów w warstwie Premium. Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md). 

Dedykowane punkty końcowe danych można włączyć przy użyciu interfejsu Azure Portal lub interfejsu wiersza polecenia platformy Azure. Punkty końcowe danych są zgodne ze wzorcem regionalnym, `<registry-name>.<region>.data.azurecr.io` . W rejestrze z replikacją geograficzną włączenie punktów końcowych danych umożliwia korzystanie z punktów końcowych we wszystkich regionach repliki.

### <a name="portal"></a>Portal

Aby włączyć punkty końcowe danych przy użyciu portalu:

1. Przejdź do rejestru kontenerów.
1. Wybierz **pozycję Sieć** Publiczny  >  **dostęp.**
1. Zaznacz pole **wyboru Włącz dedykowany punkt końcowy** danych.
1. Wybierz pozycję **Zapisz**.

Punkt końcowy danych lub punkty końcowe są wyświetlane w portalu.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Dedykowane punkty końcowe danych w portalu":::

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby włączyć punkty końcowe danych przy użyciu interfejsu wiersza polecenia platformy Azure, użyj interfejsu wiersza polecenia platformy Azure w wersji 2.4.0 lub wyższej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Następujące polecenie [az acr update][az-acr-update] włącza dedykowane punkty końcowe danych w rejestrze *myregistry*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Aby wyświetlić punkty końcowe danych, użyj [polecenia az acr show-endpoints:][az-acr-show-endpoints]

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

Po skonfigurowaniu dedykowanych punktów końcowych danych dla rejestru można włączyć reguły dostępu zapory klienta dla punktów końcowych danych. Włącz reguły dostępu do punktu końcowego danych dla wszystkich wymaganych regionów rejestru.

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurowanie reguł zapory klienta dla mcr

Jeśli musisz uzyskać dostęp do aplikacji Microsoft Container Registry (MCR) zza zapory, zapoznaj się ze wskazówkami dotyczącymi konfigurowania reguł zapory klienta [MCR.](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) McR to podstawowy rejestr dla wszystkich obrazów platformy Docker opublikowanych przez firmę Microsoft, takich jak obrazy systemu Windows Server.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o najlepszych rozwiązaniach dotyczących zabezpieczeń sieci na platformie Azure](../security/fundamentals/network-best-practices.md)

* Dowiedz się więcej o [grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md) w sieci wirtualnej platformy Azure

* Dowiedz się więcej o [konfigurowaniu Private Link](container-registry-private-link.md) dla rejestru kontenerów

* Dowiedz się więcej o [dedykowanych punktach końcowych](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) danych dla Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-show-endpoints]: /cli/azure/acr#az_acr_show_endpoints