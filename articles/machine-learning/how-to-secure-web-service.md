---
title: Zabezpieczanie usług sieci Web przy użyciu protokołu TLS
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak włączyć protokół HTTPS w celu zabezpieczenia usługi sieci Web wdrożonej za pośrednictwem Azure Machine Learning. Azure Machine Learning korzysta z protokołu TLS w wersji 1,2 do zabezpieczania modeli wdrożonych jako usługi sieci Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 52344b665b00329c80fb651657fbbd19d5ffd7a4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743085"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS


W tym artykule opisano sposób zabezpieczania usługi sieci Web, która jest wdrażana za pomocą Azure Machine Learning.

Używasz [protokołu HTTPS](https://en.wikipedia.org/wiki/HTTPS) , aby ograniczyć dostęp do usług sieci Web i zabezpieczyć dane przesyłane przez klientów. Protokół HTTPS pomaga w zabezpieczeniu komunikacji między klientem a usługą sieci Web przez szyfrowanie komunikacji między nimi. Szyfrowanie używa [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest czasami nazywany *SSL* (SSL), który był POPRZEDNIKIEM protokołu TLS.

> [!TIP]
> Zestaw Azure Machine Learning SDK używa terminu "SSL" dla właściwości, które są związane z bezpieczną komunikacją. Nie oznacza to, że usługa sieci Web nie korzysta z *protokołu TLS* . Protokół SSL jest zaledwie częściej uznawany za okres.
>
> Usługi sieci Web wdrożone za pomocą Azure Machine Learning obsługują tylko protokół TLS w wersji 1,1 dla ACI i TLS w wersji 1,2 dla AKS.

Protokoły TLS i SSL są zależne od *certyfikatów cyfrowych* , które pomagają w szyfrowaniu i weryfikacji tożsamości. Aby uzyskać więcej informacji na temat sposobu działania certyfikatów cyfrowych, zobacz temat [infrastruktura kluczy publicznych](https://en.wikipedia.org/wiki/Public_key_infrastructure)tematu witryny Wikipedia.

> [!WARNING]
> Jeśli nie używasz protokołu HTTPS dla usługi sieci Web, dane wysyłane do i z usługi mogą być widoczne dla innych osób w Internecie.
>
> Protokół HTTPS umożliwia również klientowi zweryfikowanie autentyczności serwera, z którym jest nawiązywane połączenie. Ta funkcja chroni klientów przed atakami typu [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Jest to ogólny proces zabezpieczania usługi sieci Web:

1. Pobierz nazwę domeny.

2. Pobierz certyfikat cyfrowy.

3. Wdróż lub zaktualizuj usługę sieci Web z włączonym protokołem TLS.

4. Zaktualizuj system DNS w taki sposób, aby wskazywał usługę sieci Web.

> [!IMPORTANT]
> W przypadku wdrażania w usłudze Azure Kubernetes Service (AKS) można zakupić własny certyfikat lub użyć certyfikatu dostarczonego przez firmę Microsoft. Jeśli używasz certyfikatu od firmy Microsoft, nie musisz uzyskać nazwy domeny ani certyfikatu TLS/SSL. Aby uzyskać więcej informacji, zobacz sekcję [Włączanie protokołów TLS i Deploy](#enable) w tym artykule.

Istnieją niewielkie różnice w przypadku zabezpieczania serwerów docelowych w ramach [wdrożenia](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Uzyskanie nazwy domeny

Jeśli nie masz jeszcze nazwy domeny, Kup ją w *rejestratorze nazw domen* . Proces i cena różnią się między Rejestratorami. Rejestrator udostępnia narzędzia do zarządzania nazwą domeny. Te narzędzia służą do mapowania w pełni kwalifikowanej nazwy domeny (FQDN) (takiej jak www \. contoso.com) na adres IP, który hostuje usługę sieci Web.

## <a name="get-a-tlsssl-certificate"></a>Uzyskiwanie certyfikatu TLS/SSL

Istnieje wiele sposobów uzyskiwania certyfikatu TLS/SSL (certyfikat cyfrowy). Najbardziej powszechnym celem jest zakupienie jednego z *urzędów* certyfikacji (CA). Bez względu na to, gdzie otrzymujesz certyfikat, potrzebne są następujące pliki:

* **Certyfikat** . Certyfikat musi zawierać pełny łańcuch certyfikatów i musi być "zakodowany przez PEM".
* **Klucz** . Klucz musi być również zakodowany przez PEM.

Żądając certyfikatu, należy podać nazwę FQDN adresu, który ma być używany przez usługę sieci Web (na przykład www \. contoso.com). Adres, który jest umieszczony w certyfikacie i adres używany przez klientów, jest porównywany w celu zweryfikowania tożsamości usługi sieci Web. Jeśli te adresy nie są zgodne, klient otrzymuje komunikat o błędzie.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikatu i klucza jako plików zakodowanych przez PEM, można użyć narzędzia, takiego jak [OpenSSL](https://www.openssl.org/) , aby zmienić format.

> [!WARNING]
> Używaj certyfikatów z podpisem *własnym* tylko do celów deweloperskich. Nie używaj ich w środowiskach produkcyjnych. Certyfikaty z podpisem własnym mogą spowodować problemy w aplikacjach klienckich. Aby uzyskać więcej informacji, zobacz dokumentację bibliotek sieciowych używanych przez aplikację kliencką.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Włącz protokół TLS i Wdróż

Aby wdrożyć (lub ponownie wdrożyć) usługę z włączonym protokołem TLS, należy ustawić parametr *ssl_enabled* na wartość "true" wszędzie tam, gdzie ma to zastosowanie. Dla parametru *ssl_certificate* ustaw wartość pliku *certyfikatu* . Ustaw *ssl_key* na wartość pliku *klucza* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Wdróż w AKS i programowalnej bramie (FPGA)

  > [!NOTE]
  > Informacje przedstawione w tej sekcji dotyczą również wdrażania bezpiecznej usługi sieci Web dla projektanta. Jeśli nie masz doświadczenia w korzystaniu z zestawu SDK języka Python, zobacz artykuł [co to jest zestaw Azure Machine Learning SDK dla języka Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

Podczas wdrażania programu do AKS można utworzyć nowy klaster AKS lub dołączyć istniejący. Aby uzyskać więcej informacji na temat tworzenia lub dołączania klastra, zobacz [Wdrażanie modelu w klastrze usługi Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).
  
-  W przypadku tworzenia nowego klastra należy użyć **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** .
- Jeśli dołączysz istniejący klaster, użyj **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Oba zwracają obiekt konfiguracji, który ma metodę **enable_ssl** .

Metoda **enable_ssl** może korzystać z certyfikatu dostarczonego przez firmę Microsoft lub zakupionego certyfikatu.

  * Korzystając z certyfikatu firmy Microsoft, należy użyć parametru *leaf_domain_label* . Ten parametr generuje nazwę DNS usługi. Na przykład wartość "contoso" tworzy nazwę domeny "contoso \<six-random-characters> . \<azureregion> . cloudapp.azure.com ", gdzie \<azureregion> jest regionem zawierającym usługę. Opcjonalnie można użyć parametru *overwrite_existing_domain* , aby zastąpić istniejące *leaf_domain_label* .

    Aby wdrożyć (lub ponownie wdrożyć) usługę z włączonym protokołem TLS, należy ustawić parametr *ssl_enabled* na wartość "true" wszędzie tam, gdzie ma to zastosowanie. Dla parametru *ssl_certificate* ustaw wartość pliku *certyfikatu* . Ustaw *ssl_key* na wartość pliku *klucza* .

    > [!IMPORTANT]
    > Jeśli używasz certyfikatu od firmy Microsoft, nie musisz kupować własnego certyfikatu ani nazwy domeny.

    Poniższy przykład pokazuje, jak utworzyć konfigurację, która umożliwia certyfikat TLS/SSL od firmy Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Korzystając z *zakupionego certyfikatu* , należy użyć parametrów *ssl_cert_pem_file* , *ssl_key_pem_file* i *ssl_cname* . Poniższy przykład ilustruje sposób użycia plików *PEM* do utworzenia konfiguracji korzystającej z zakupionego certyfikatu TLS/SSL:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Aby uzyskać więcej informacji na temat *enable_ssl* , zobacz [AksProvisioningConfiguration.enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) i [AksAttachConfiguration.enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Wdróż na Azure Container Instances

Podczas wdrażania programu do Azure Container Instances należy podać wartości parametrów związanych z protokołem TLS, jak pokazano w poniższym fragmencie kodu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Aby uzyskać więcej informacji, zobacz [AciWebservice.deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aktualizowanie systemu DNS

Następnie należy zaktualizować system DNS, aby wskazywał usługę sieci Web.

+ **Dla Container Instances:**

  Użyj narzędzi z rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać adres IP usługi.

  Może wystąpić opóźnienie minut lub godzin, po upływie którego klienci mogą rozpoznać nazwę domeny, w zależności od rejestratora i "czasu wygaśnięcia", który jest skonfigurowany dla nazwy domeny.

+ **Dla AKS:**

  > [!WARNING]
  > Jeśli usługa *leaf_domain_label* została użyta do utworzenia usługi przy użyciu certyfikatu firmy Microsoft, nie należy ręcznie aktualizować wartości DNS dla klastra. Wartość powinna być ustawiana automatycznie.

  Zaktualizuj serwer DNS publicznego adresu IP klastra AKS na karcie **Konfiguracja** w obszarze **Ustawienia** w okienku po lewej stronie. (Zobacz poniższy obraz). Publiczny adres IP to typ zasobu, który jest tworzony w ramach grupy zasobów zawierającej węzły agenta AKS i inne zasoby sieciowe.

  [![Azure Machine Learning: Zabezpieczanie usług sieci Web za pomocą protokołu TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Aktualizowanie certyfikatu TLS/SSL

Certyfikaty protokołu TLS/SSL wygasną i należy je odnowić. Zwykle odbywa się to co rok. Skorzystaj z informacji w poniższych sekcjach, aby zaktualizować i odnowić certyfikat dla modeli wdrożonych w usłudze Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualizowanie certyfikatu wygenerowanego przez firmę Microsoft

Jeśli certyfikat został pierwotnie wygenerowany przez firmę Microsoft (w przypadku korzystania z *leaf_domain_label* do tworzenia usługi), użyj jednego z poniższych przykładów, aby zaktualizować certyfikat:

> [!IMPORTANT]
> * Jeśli istniejący certyfikat jest nadal ważny, użyj `renew=True` (SDK) lub `--ssl-renew` (CLI), aby wymusić jego odnowienie. Na przykład jeśli istniejący certyfikat jest nadal ważny przez 10 dni i nie jest używany `renew=True` , certyfikat może nie zostać odnowiony.
> * Po pierwotnym wdrożeniu usługi `leaf_domain_label` jest używana do tworzenia nazw DNS przy użyciu wzorca `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . Aby zachować istniejącą nazwę (włącznie z wygenerowanymi 6 cyfr), użyj oryginalnej `leaf_domain_label` wartości. Nie uwzględniaj 6 cyfr, które zostały wygenerowane.

**Korzystanie z zestawu SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Korzystanie z interfejsu wiersza polecenia**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Aby uzyskać więcej informacji, zobacz następujące dokumenty referencyjne:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

### <a name="update-custom-certificate"></a>Aktualizowanie certyfikatu niestandardowego

Jeśli certyfikat został pierwotnie wygenerowany przez urząd certyfikacji, wykonaj następujące czynności:

1. Aby odnowić certyfikat, użyj dokumentacji dostarczonej przez urząd certyfikacji. Ten proces tworzy nowe pliki certyfikatów.

1. Użyj zestawu SDK lub interfejsu wiersza polecenia, aby zaktualizować usługę przy użyciu nowego certyfikatu:

    **Korzystanie z zestawu SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Korzystanie z interfejsu wiersza polecenia**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Aby uzyskać więcej informacji, zobacz następujące dokumenty referencyjne:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

## <a name="disable-tls"></a>Wyłącz protokół TLS

Aby wyłączyć protokół TLS dla modelu wdrożonego w usłudze Azure Kubernetes, Utwórz `SslConfiguration` za pomocą usługi `status="Disabled"` , a następnie wykonaj aktualizację:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Następne kroki
Instrukcje:
+ [Korzystanie z modelu uczenia maszynowego wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
+ [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)
