---
title: Konfigurowanie reguł zapory usługi
description: Skonfiguruj reguły adresów IP, aby umożliwić dostęp do usługi Azure Container Registry z wybranych publicznych adresów IP lub zakresów adresów.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984618"
---
# <a name="configure-public-ip-network-rules"></a>Konfigurowanie reguł sieci publicznych adresów IP

Usługa Azure Container Registry domyślnie akceptuje połączenia przez Internet z hostów w dowolnej sieci. W tym artykule pokazano, jak skonfigurować rejestr kontenerów w taki sposób, aby zezwalał na dostęp tylko z określonych publicznych adresów IP lub zakresów adresów. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

Reguły sieci IP są konfigurowane w punkcie końcowym rejestru publicznego. Reguły sieci IP nie mają zastosowania do prywatnych punktów końcowych skonfigurowanych za pomocą [linku prywatnego](container-registry-private-link.md)

Konfigurowanie reguł dostępu do adresów IP jest dostępne w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstw](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Dostęp z wybranej sieci publicznej — interfejs wiersza polecenia

### <a name="change-default-network-access-to-registry"></a>Zmień domyślny dostęp sieciowy do rejestru

Aby ograniczyć dostęp do wybranej sieci publicznej, najpierw Zmień domyślną akcję na Odmów dostępu. Zastąp nazwę rejestru następującym poleceniem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

Użyj polecenia [AZ ACR Network-Rule Add][az-acr-network-rule-add] , aby dodać regułę sieci do rejestru, który umożliwia dostęp z publicznego adresu IP lub zakresu. Na przykład Zastąp nazwę rejestru kontenera i publiczny adres IP maszyny wirtualnej w sieci wirtualnej.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Po dodaniu reguły zastosowanie reguły trwa kilka minut.

## <a name="access-from-selected-public-network---portal"></a>Dostęp z wybranej publicznej sieci — Portal

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia**wybierz pozycję **Sieć**.
1. Na karcie **dostęp publiczny** wybierz opcję, aby zezwolić na publiczny dostęp z **wybranych sieci**.
1. W obszarze **Zapora**wprowadź publiczny adres IP, taki jak publiczny adres IP maszyny wirtualnej w sieci wirtualnej. Lub wprowadź zakres adresów w notacji CIDR, który zawiera adres IP maszyny wirtualnej.
1. Wybierz pozycję **Zapisz**.

![Konfigurowanie reguły zapory dla rejestru kontenerów][acr-access-selected-networks]

> [!NOTE]
> Po dodaniu reguły zastosowanie reguły trwa kilka minut.

> [!TIP]
> Opcjonalnie można włączyć dostęp do rejestru z lokalnego komputera klienckiego lub z zakresu adresów IP. Aby zezwolić na ten dostęp, potrzebny jest publiczny adres IPv4 komputera. Ten adres można znaleźć, wyszukując pozycję "co to jest mój adres IP" w przeglądarce internetowej. Bieżący adres IPv4 klienta jest również wyświetlany automatycznie podczas konfigurowania ustawień zapory na stronie **sieci** w portalu.

## <a name="disable-public-network-access"></a>Wyłącz dostęp do sieci publicznej

Aby ograniczyć ruch do sieci wirtualnych przy użyciu [linku prywatnego](container-registry-private-link.md), należy wyłączyć publiczny punkt końcowy w rejestrze. Wyłączenie publicznego punktu końcowego zastępuje wszystkie konfiguracje zapory.

### <a name="disable-public-access---portal"></a>Wyłącz dostęp publiczny — Portal

1. W portalu przejdź do rejestru kontenerów, a następnie wybierz pozycję **ustawienia > sieci**.
1. Na karcie **dostęp publiczny** w obszarze **Zezwalaj na dostęp publiczny**wybierz pozycję **wyłączone**. Następnie wybierz pozycję **Zapisz**.

![Wyłącz dostęp publiczny][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Przywróć domyślny dostęp do rejestru

Aby przywrócić rejestr w celu domyślnego zezwalania na dostęp, zaktualizuj akcję domyślną. 

### <a name="restore-default-registry-access---portal"></a>Przywróć domyślny dostęp do rejestru — Portal

1. W portalu przejdź do rejestru kontenerów, a następnie wybierz pozycję **ustawienia > sieci**.
1. W obszarze **Zapora**wybierz każdy zakres adresów, a następnie wybierz ikonę Usuń.
1. Na karcie **dostęp publiczny** w obszarze **Zezwalaj na dostęp publiczny**wybierz pozycję **wszystkie sieci**. Następnie wybierz pozycję **Zapisz**.

![Publiczny dostęp ze wszystkich sieci][acr-access-all-networks]

## <a name="next-steps"></a>Następne kroki

* Aby ograniczyć dostęp do rejestru przy użyciu prywatnego punktu końcowego w sieci wirtualnej, zobacz [Konfigurowanie prywatnego linku platformy Azure dla usługi Azure Container Registry](container-registry-private-link.md).
* Jeśli konieczne jest skonfigurowanie reguł dostępu do rejestru za zaporą klienta, zobacz [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](container-registry-firewall-access-rules.md).

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
