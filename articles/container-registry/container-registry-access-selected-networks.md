---
title: Konfigurowanie dostępu do rejestru publicznego
description: Skonfiguruj reguły adresów IP, aby umożliwić dostęp do rejestru kontenerów platformy Azure z wybranych publicznych adresów IP lub zakresów adresów.
ms.topic: article
ms.date: 03/08/2021
ms.openlocfilehash: 00912f0e66c84feff40e6439d59ccdfa82a4ab6a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785840"
---
# <a name="configure-public-ip-network-rules"></a>Konfigurowanie reguł sieci publicznych adresów IP

Rejestr kontenerów platformy Azure domyślnie akceptuje połączenia przez Internet z hostów w dowolnej sieci. W tym artykule pokazano, jak skonfigurować rejestr kontenerów, aby zezwolić na dostęp tylko z określonych publicznych adresów IP lub zakresów adresów. Podano równoważne kroki przy użyciu interfejsu wiersza polecenia Azure Portal Azure.

Reguły sieci IP są konfigurowane w punkcie końcowym rejestru publicznego. Reguły sieci IP nie mają zastosowania do prywatnych punktów końcowych skonfigurowanych przy użyciu [Private Link](container-registry-private-link.md)

Konfigurowanie reguł dostępu IP jest dostępne w warstwie **usługi rejestru** kontenerów w warstwie Premium. Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md).

Każdy rejestr obsługuje maksymalnie 100 reguł dostępu do sieci.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Dostęp z wybranej sieci publicznej — interfejs wiersza polecenia

### <a name="change-default-network-access-to-registry"></a>Zmienianie domyślnego dostępu sieciowego do rejestru

Aby ograniczyć dostęp do wybranej sieci publicznej, najpierw zmień akcję domyślną na odmawianie dostępu. Zastąp nazwę rejestru za pomocą następującego [polecenia az acr update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

Użyj polecenia [az acr network-rule add,][az-acr-network-rule-add] aby dodać do rejestru regułę sieci, która zezwala na dostęp z publicznego adresu IP lub zakresu. Na przykład zastąp nazwę rejestru kontenerów i publiczny adres IP maszyny wirtualnej w sieci wirtualnej.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Po dodaniu reguły jej efekt może potrwać kilka minut.

## <a name="access-from-selected-public-network---portal"></a>Dostęp z wybranej sieci publicznej — portal

1. W portalu przejdź do rejestru kontenerów.
1. W **obszarze Ustawienia** wybierz pozycję **Sieć.**
1. Na karcie **Dostęp publiczny** wybierz opcję zezwalania na dostęp publiczny z **wybranych sieci.**
1. W **obszarze** Zapora wprowadź publiczny adres IP, taki jak publiczny adres IP maszyny wirtualnej w sieci wirtualnej. Możesz też wprowadzić zakres adresów w notacji CIDR, która zawiera adres IP maszyny wirtualnej.
1. Wybierz pozycję **Zapisz**.

![Konfigurowanie reguły zapory dla rejestru kontenerów][acr-access-selected-networks]

> [!NOTE]
> Po dodaniu reguły jej efekt może potrwać kilka minut.

> [!TIP]
> Opcjonalnie włącz dostęp do rejestru z lokalnego komputera klienckiego lub zakresu adresów IP. Aby zezwolić na ten dostęp, potrzebny jest publiczny adres IPv4 komputera. Możesz znaleźć ten adres, wyszukując "jaki jest mój adres IP" w przeglądarce internetowej. Bieżący adres IPv4 klienta jest również wyświetlany automatycznie podczas konfigurowania ustawień zapory na **stronie Sieć** w portalu.

## <a name="disable-public-network-access"></a>Wyłączanie dostępu do sieci publicznej

Opcjonalnie wyłącz publiczny punkt końcowy w rejestrze. Wyłączenie publicznego punktu końcowego zastępuje wszystkie konfiguracje zapory. Na przykład można wyłączyć dostęp publiczny do rejestru zabezpieczonego w sieci wirtualnej przy użyciu Private Link [.](container-registry-private-link.md)

> [!NOTE]
> Jeśli rejestr jest ustawiony w sieci wirtualnej z punktem końcowym [usługi,](container-registry-vnet.md)wyłączenie dostępu do publicznego punktu końcowego rejestru powoduje również wyłączenie dostępu do rejestru w sieci wirtualnej.

### <a name="disable-public-access---cli"></a>Wyłączanie dostępu publicznego — interfejs wiersza polecenia

Aby wyłączyć dostęp publiczny przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom [narzędzie az acr update][az-acr-update] i ustaw wartość `--public-network-enabled` `false` . Argument wymaga interfejsu wiersza polecenia platformy Azure w wersji `public-network-enabled` 2.6.0 lub nowszej. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Wyłączanie dostępu publicznego — portal

1. W portalu przejdź do rejestru kontenerów i wybierz pozycję Ustawienia > **sieci.**
1. Na karcie **Dostęp publiczny** w obszarze **Zezwalaj na dostęp do sieci publicznej** wybierz pozycję **Wyłączone.** Następnie wybierz pozycję **Zapisz**.

![Wyłączanie dostępu publicznego][acr-access-disabled]


## <a name="restore-public-network-access"></a>Przywracanie dostępu do sieci publicznej

Aby ponownie włączyć publiczny punkt końcowy, zaktualizuj ustawienia sieci, aby zezwolić na dostęp publiczny. Włączenie publicznego punktu końcowego zastępuje wszystkie konfiguracje zapory. 

### <a name="restore-public-access---cli"></a>Przywracanie dostępu publicznego — interfejs wiersza polecenia

Uruchom [az acr update][az-acr-update] i ustaw wartość `--public-network-enabled` `true` . 

> [!NOTE]
> Argument wymaga interfejsu wiersza polecenia platformy Azure w wersji `public-network-enabled` 2.6.0 lub nowszej. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Przywracanie dostępu publicznego — portal

1. W portalu przejdź do rejestru kontenerów i wybierz pozycję Ustawienia > **sieci.**
1. Na karcie **Dostęp publiczny** w obszarze **Zezwalaj na dostęp do sieci publicznej** wybierz pozycję Wszystkie **sieci.** Następnie wybierz pozycję **Zapisz**.

![Dostęp publiczny ze wszystkich sieci][acr-access-all-networks]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli ustawiono regułę sieci publicznej lub odmowa dostępu publicznego do rejestru, próba zalogowania się do rejestru z niedozwolonej sieci publicznej nie powiedzie się. Dostęp klienta zza serwera proxy HTTPS również nie powiedzie się, jeśli nie ustawiono reguły dostępu dla serwera proxy. Zostanie wyświetlony komunikat o błędzie podobny do `Error response from daemon: login attempt failed with status: 403 Forbidden` lub `Looks like you don't have access to registry` .

Te błędy mogą również wystąpić, jeśli używasz serwera proxy HTTPS dozwolonego przez regułę dostępu do sieci, ale serwer proxy nie jest prawidłowo skonfigurowany w środowisku klienta. Sprawdź, czy zarówno klient platformy Docker, jak i demon platformy Docker są skonfigurowane pod uwagę zachowanie serwera proxy. Aby uzyskać szczegółowe informacje, [zobacz Serwer proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) w dokumentacji platformy Docker.


## <a name="next-steps"></a>Następne kroki

* Aby ograniczyć dostęp do rejestru przy użyciu prywatnego punktu końcowego w sieci wirtualnej, zobacz [Azure Private Link dla rejestru kontenerów platformy Azure.](container-registry-private-link.md)
* Jeśli musisz skonfigurować reguły dostępu do rejestru zza zapory klienta, zobacz Configure rules to access an Azure container registry behind a firewall (Konfigurowanie reguł dostępu do rejestru kontenerów platformy [Azure za zaporą).](container-registry-firewall-access-rules.md)

[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
