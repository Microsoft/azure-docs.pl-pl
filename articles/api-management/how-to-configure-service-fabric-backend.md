---
title: Konfigurowanie Service Fabric zaplecza w usłudze Azure API Management | Microsoft Docs
description: Jak utworzyć zaplecza usługi Service Fabric w usłudze Azure API Management przy użyciu Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815886"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Konfigurowanie zaplecza Service Fabric w API Management przy użyciu Azure Portal

W tym artykule przedstawiono sposób konfigurowania usługi [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) jako niestandardowego zaplecza interfejsu API przy użyciu Azure Portal. W celach demonstracyjnych pokazano, jak skonfigurować podstawową bez stanową usługę ASP.NET Core Reliable Service jako Service Fabric zaplecza.

Aby uzyskać podstawowe informacje, zobacz [Zaplecza w API Management](backends.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące konfigurowania przykładowej usługi w klastrze Service Fabric systemem Windows jako niestandardowego zaplecza:

* **Środowisko projektowe systemu Windows** — [zainstaluj Visual Studio 2019](https://www.visualstudio.com) r. oraz obciążenia deweloperska platformy **Azure,** tworzenia aplikacji ASP.NET i tworzenia aplikacji internetowych **oraz** **międzyplatformowych** aplikacji dla platformy .NET Core. Następnie skonfiguruj [środowisko deweloperskie platformy .NET](../service-fabric/service-fabric-get-started.md).

* **Service Fabric klastra —** zobacz [Samouczek: wdrażanie klastra Service Fabric z systemem Windows w sieci wirtualnej platformy Azure.](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md) Możesz utworzyć klaster z istniejącym certyfikatem X.509 lub w celach testowych utworzyć nowy certyfikat z podpisem własnym. Klaster jest tworzony w sieci wirtualnej.

* **Przykładowa Service Fabric —** tworzenie aplikacji interfejsu API sieci Web i wdrażanie w klastrze Service Fabric zgodnie z opisem w te API Management z Service Fabric [platformie Azure.](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

    Te kroki tworzą podstawową bez stanową usługę ASP.NET Core Reliable Service przy użyciu domyślnego szablonu projektu internetowego interfejsu API. Później uwidocznisz punkt końcowy HTTP dla tej usługi za pośrednictwem usługi Azure API Management.

    Zanotuj nazwę aplikacji, na przykład `fabric:/myApplication/myService` . 

* **API Management — istniejące** lub nowe wystąpienie API Management w warstwie  **Premium** lub Deweloper i w tym samym regionie co Service Fabric klastra. Jeśli jest potrzebny, [utwórz wystąpienie API Management .](get-started-create-service-instance.md)

* **Sieć wirtualna** — dodaj API Management do sieci wirtualnej utworzonej dla klastra Service Fabric wirtualnego. API Management wymaga dedykowanej podsieci w sieci wirtualnej.

  Aby uzyskać instrukcje dotyczące włączania łączności z siecią wirtualną dla wystąpienia API Management, zobacz Jak używać usługi [Azure API Management z sieciami wirtualnymi.](api-management-using-with-vnet.md)

## <a name="create-backend---portal"></a>Tworzenie zaplecza — portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Dodawanie Service Fabric klastra klastra do API Management

Certyfikat Service Fabric klastra jest przechowywany i zarządzany w magazynie kluczy platformy Azure skojarzonym z klastrem. Dodaj ten certyfikat do API Management jako certyfikat klienta.

Aby uzyskać instrukcje dotyczące dodawania certyfikatu do wystąpienia API Management, zobacz Jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta w [usłudze Azure API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Zalecamy dodanie certyfikatu do usługi API Management odwołaniu się do certyfikatu magazynu kluczy. 

### <a name="add-service-fabric-backend"></a>Dodawanie Service Fabric zaplecza

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W **obszarze Interfejsy API** wybierz pozycję **Zaplecza**  >  **+ Dodaj**.
1. Wprowadź nazwę zaplecza i opcjonalny opis
1. W **typie** wybierz **pozycję Service Fabric**.
1. W **polu Adres URL** środowiska uruchomieniowego wprowadź nazwę usługi Service Fabric, do API Management będzie przesyłać dalej żądania. Przykład: `fabric:/myApplication/myService`. 
1. W **maksymalną liczbę ponownych** prób rozpoznawania partycji , wprowadź liczbę z wartości od 0 do 10.
1. Wprowadź punkt końcowy zarządzania Service Fabric klastra. Ten punkt końcowy to adres URL klastra na porcie `19080` , na przykład `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. W **sekcji Certyfikat** klienta wybierz certyfikat Service Fabric dodany do wystąpienia API Management w poprzedniej sekcji.
1. W **metodzie autoryzacji punktu końcowego** zarządzania wprowadź odcisk palca lub nazwę X509 serwera certyfikatu używanego przez usługę zarządzania klastrem Service Fabric do komunikacji przy Service Fabric TLS.
1. Włącz ustawienia **Weryfikuj łańcuch certyfikatów** i **Zweryfikuj nazwę** certyfikatu.
1. W chmurze Poświadczenia **autoryzacji** podaj poświadczenia, jeśli to konieczne, aby dotrzeć do skonfigurowanej usługi zaplecza w Service Fabric. W przypadku przykładowej aplikacji używanej w tym scenariuszu poświadczenia autoryzacji nie są wymagane.
1. Wybierz przycisk **Utwórz**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Tworzenie zaplecza usługi Service Fabric":::

## <a name="use-the-backend"></a>Korzystanie z zaplecza

Aby użyć niestandardowego zaplecza, odwołuj się do niego przy użyciu [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zasad. Te zasady przekształcają domyślny adres URL bazy usługi zaplecza przychodzącego żądania interfejsu API do określonego zaplecza, w tym przypadku Service Fabric zaplecza. 

Zasady mogą być przydatne w przypadku istniejącego interfejsu API do przekształcania żądania przychodzącego do innego zaplecza niż określone `set-backend-service` w ustawieniach interfejsu API. W celach demonstracyjnych w tym artykule utwórz testowy interfejs API i ustaw zasady, aby kierować żądania interfejsu API do Service Fabric zaplecza. 

### <a name="create-api"></a>Tworzenie interfejsu API

Wykonaj kroki opisane w te [tematu Ręczne dodawanie interfejsu API,](add-api-manually.md) aby utworzyć pusty interfejs API.

* W ustawieniach interfejsu API pozostaw pusty **adres URL usługi** internetowej.
* Dodaj **sufiks adresu URL interfejsu API,** taki jak *sieć szkieletowa*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Tworzenie pustego interfejsu API":::

### <a name="add-get-operation-to-the-api"></a>Dodawanie operacji GET do interfejsu API

Jak pokazano w Service Fabric usługi Service Fabric, przykładowa usługa ASP.NET Core wdrożona w klastrze usługi Service Fabric obsługuje pojedynczą operację HTTP GET w ścieżce adresu URL [](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service) `/api/values` .

Domyślną odpowiedzią dla tej ścieżki jest tablica JSON z dwoma ciągami:

```json
["value1", "value2"]
```

Aby przetestować integrację API Management z klastrem, dodaj odpowiednią operację GET do interfejsu API w ścieżce `/api/values` :

1. Wybierz interfejs API utworzony w poprzednim kroku.
1. Wybierz opcję **+ Dodaj operację**.
1. W **oknie Frontend** wprowadź następujące wartości i wybierz pozycję **Zapisz**.

     | Ustawienie             | Wartość                             | 
    |---------------------|-----------------------------------|
    | **Nazwa wyświetlana**    | *Testowanie zaplecza*                       |  
    | **Adres URL** | GET                               | 
    | **Adres URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Dodawanie operacji GET do interfejsu API":::

### <a name="configure-set-backend-policy"></a>Konfigurowanie `set-backend` zasad

Dodaj zasady [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) do testowego interfejsu API.

1. Na karcie **Projekt** w sekcji **Przetwarzanie danych przychodzących** wybierz ikonę edytora kodu ( **</>** ). 
1. Umieść kursor wewnątrz elementu **&lt; inbound &gt;**
1. Dodaj następującą instrukcje zasad. W `backend-id` pliku zastąp nazwę Service Fabric zaplecza.

   To `sf-resolve-condition` warunek ponawiania, jeśli partycja klastra nie została rozpoznana. Liczba ponownych prób została ustawiona podczas konfigurowania zaplecza.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Konfigurowanie zasad usługi set-backend-service":::

### <a name="test-backend-api"></a>Testowanie interfejsu API zaplecza

1. Na karcie **Test** wybierz operację **GET** utworzoną w poprzedniej sekcji.
1. Wybierz pozycję **Wyślij**.

Po prawidłowym skonfigurowaniu odpowiedź HTTP pokazuje kod powodzenia HTTP i wyświetla kod JSON zwrócony z usługi Service Fabric zaplecza.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testowanie Service Fabric zaplecza":::

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [skonfigurować zasady przekazywania](api-management-advanced-policies.md) żądań do zaplecza
* Zaplecza można również skonfigurować przy użyciu interfejsu [API REST](/rest/api/apimanagement/2020-06-01-preview/backend)API Management, Azure PowerShell lub Azure Resource Manager [szablonów](../service-fabric/service-fabric-tutorial-deploy-api-management.md) [](/powershell/module/az.apimanagement/new-azapimanagementbackend)

