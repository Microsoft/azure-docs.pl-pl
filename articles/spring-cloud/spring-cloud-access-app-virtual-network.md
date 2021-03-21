---
title: Aplikacja dostępu do chmury ze sprężyną Azure w sieci wirtualnej
description: Uzyskaj dostęp do aplikacji w chmurze Azure wiosną w sieci wirtualnej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576535"
---
# <a name="access-your-application-in-a-private-network"></a>Uzyskiwanie dostępu do aplikacji w sieci prywatnej

W tym dokumencie wyjaśniono, jak uzyskać dostęp do punktu końcowego aplikacji w sieci prywatnej.  Aby uzyskać dostęp, musisz utworzyć **strefę usługi Azure prywatna strefa DNS** w ramach subskrypcji, aby przetłumaczyć/rozwiązać prywatną w pełni kwalifikowaną nazwę domeny (FQDN) na adres IP.

Po wdrożeniu **punktu końcowego** dla aplikacji w usłudze Azure wiosennej usługi w chmurze w sieci wirtualnej jest on używany jako prywatna nazwa FQDN. Domena jest dostępna tylko w sieci prywatnej. Aplikacje i usługi korzystają z punktu końcowego aplikacji. Obejmują one **punkt końcowy testu** opisany w temacie [wyświetlanie aplikacji i wdrożeń](spring-cloud-howto-staging-environment.md#view-apps-and-deployments). Funkcja **przesyłania strumieniowego dzienników** opisana w artykule [przesyłanie strumieniowe dzienników aplikacji w chmurze Azure sprężyny w czasie rzeczywistym](spring-cloud-howto-log-streaming.md)działa również tylko w ramach sieci prywatnej.

## <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

Poniższa procedura tworzy prywatną strefę DNS dla aplikacji w sieci prywatnej.

1. Otwórz witrynę Azure Portal. W górnym polu wyszukiwania Wyszukaj pozycję **prywatna strefa DNS strefy**, a następnie wybierz pozycję **strefy prywatna strefa DNS** z wyniku.

2. Na stronie **strefy prywatna strefa DNS** wybierz pozycję **+ Dodaj**.

3. Wypełnij formularz na stronie **Tworzenie strefy prywatna strefa DNS** . Wprowadź **<span>Private.azuremicroservices.IO</span>** jako **nazwę** strefy.

4. Wybierz pozycję **Przejrzyj i utwórz**.

5. Wybierz przycisk **Utwórz**.

Tworzenie strefy może potrwać kilka minut.

## <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnej

Aby połączyć prywatną strefę DNS z siecią wirtualną, należy utworzyć łącze sieci wirtualnej.

1. Wybierz zasób prywatnej strefy DNS utworzony powyżej: **<span>Private.azuremicroservices.IO</span>** 

2. W okienku po lewej stronie wybierz pozycję **linki sieci wirtualnej**.

3. Wybierz pozycję **Dodaj**.

4. W polu **Nazwa łącza** wprowadź wartość **Azure-sprężyn-Cloud-DNS-link** .

5. W obszarze **Sieć wirtualna** wybierz sieć wirtualną utworzoną w sposób opisany w temacie [Wdrażanie chmury Azure wiosennej w sieci wirtualnej platformy Azure](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Dodaj łącze sieci wirtualnej](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Kliknij przycisk **OK**.

## <a name="create-dns-record"></a>Utwórz rekord DNS

Aby użyć prywatnej strefy DNS do translacji/rozwiązywania nazw DNS, należy utworzyć rekord typu "A" w strefie.

1. Wybierz zasób sieci wirtualnej, który został utworzony zgodnie z opisem w temacie [Wdrażanie chmury Azure wiosennej w sieci wirtualnej platformy Azure](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. W polu wyszukiwania **podłączone urządzenia** wpisz *Kubernetes-Internal*.

3. W filtrowanym wyniku Znajdź **urządzenie** połączone z **podsiecią** środowiska uruchomieniowego usługi w ramach wystąpienia usługi i skopiuj jego **adres IP**. W tym przykładzie adres IP to *10.1.0.7*.

    [![Utwórz rekord ](media/spring-cloud-access-app-vnet/create-dns-record.png) DNS](media/spring-cloud-access-app-vnet/create-dns-record.png)

Możesz też pobrać adres IP przy użyciu następującego polecenia AZ CLI Command:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Wybierz zasób prywatnej strefy DNS utworzony powyżej: **<span>Private.azuremicroservices.IO</span>**.

5. Wybierz pozycję **+ Zestaw rekordów**.

6. W obszarze **Dodaj zestaw rekordów** wprowadź lub wybierz następujące informacje:

    |Ustawienie     |Wartość                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Nazwa        |Wejść *\**                                                                 |
    |Typ        |Wybierz                                                                |
    |TTL         |Wprowadź *1*                                                                  |
    |Jednostka TTL    |Wybierz **godziny**                                                           |
    |Adres IP  |Wprowadź adres IP skopiowany w kroku 3. W przykładzie wprowadź *10.1.0.7*.    |

    Następnie wybierz przycisk **OK**.

    ![Dodaj prywatny rekord strefy DNS](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Przypisywanie prywatnej nazwy FQDN dla aplikacji

Po wykonaniu procedury opisanej w temacie [Tworzenie i wdrażanie aplikacji mikrousług](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)można przypisać prywatną nazwę FQDN dla aplikacji.

1. Wybierz wystąpienie usługi w chmurze Azure wiosennej wdrożone w sieci wirtualnej i Otwórz kartę **aplikacje** w menu po lewej stronie.

2. Wybierz aplikację, aby wyświetlić stronę **Przegląd** .

3. Wybierz pozycję **Przypisz punkt końcowy** , aby przypisać prywatną nazwę FQDN do aplikacji. Może to potrwać kilka minut.

    ![Przypisz prywatny punkt końcowy](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. Przypisana prywatna nazwa FQDN ( **adres URL** z etykietą) jest teraz dostępna. Dostęp do niego można uzyskać tylko w sieci prywatnej, ale nie w Internecie.

## <a name="access-application-private-fqdn"></a>Dostęp do prywatnej nazwy FQDN aplikacji

Po przypisaniu możesz uzyskać dostęp do prywatnej nazwy FQDN aplikacji w sieci prywatnej. Można na przykład utworzyć maszynę serwera przesiadkowego w tej samej sieci wirtualnej lub równorzędnej sieci wirtualnej, a na tym komputerze serwera przesiadkowego jest dostępna prywatna nazwa FQDN.

![Dostęp do prywatnego punktu końcowego w sieci wirtualnej](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Następne kroki

- [Uwidacznianie aplikacji do korzystania z Internetu Application Gateway i zapory platformy Azure](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Zobacz też

- [Rozwiązywanie problemów z chmurą wiosenną platformy Azure w sieci wirtualnej](spring-cloud-troubleshooting-vnet.md)
- [Obowiązki klientów do uruchamiania chmury Azure wiosny w sieci wirtualnej](spring-cloud-vnet-customer-responsibilities.md)