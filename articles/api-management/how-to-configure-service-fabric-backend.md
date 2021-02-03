---
title: Konfigurowanie Service Fabric zaplecza na platformie Azure API Management | Microsoft Docs
description: Jak utworzyć Service Fabric zaplecza usługi na platformie Azure API Management przy użyciu Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500632"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Skonfiguruj Service Fabric zaplecza w API Management przy użyciu Azure Portal

W tym artykule pokazano, jak skonfigurować usługę [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) jako niestandardową zaplecze interfejsu API przy użyciu Azure Portal. W celach demonstracyjnych pokazano, jak skonfigurować podstawową ASP.NET Core bezstanową usługę jako zaplecze Service Fabric.

W przypadku tła zobacz [aukcje zakończyło się w API Management](backends.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące konfigurowania przykładowej usługi w klastrze Service Fabric z systemem Windows jako niestandardowego zaplecza:

* **Środowisko projektowe systemu Windows** — zainstaluj [program Visual Studio 2019](https://www.visualstudio.com) i **Międzyplatformowe obciążenia programistyczne** **platformy Azure**, **ASP.NET i Web Development** oraz platformy .NET Core. Następnie skonfiguruj [środowisko deweloperskie platformy .NET](../service-fabric/service-fabric-get-started.md).

* **Klaster Service Fabric** — zobacz [Samouczek: Wdrażanie klastra Service Fabric z systemem Windows w sieci wirtualnej platformy Azure](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Można utworzyć klaster z istniejącym certyfikatem X. 509 lub dla celów testowych Utwórz nowy certyfikat z podpisem własnym. Klaster jest tworzony w sieci wirtualnej.

* **Przykładowa aplikacja Service Fabric** — Tworzenie aplikacji internetowego interfejsu API i wdrażanie jej w klastrze Service Fabric zgodnie z opisem w artykule [integracja API Management z Service Fabric na platformie Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Te kroki tworzą podstawową usługę bezstanową ASP.NET Core niezawodne przy użyciu szablonu projektu domyślnego interfejsu API sieci Web. Później można uwidocznić punkt końcowy protokołu HTTP dla tej usługi za pośrednictwem platformy Azure API Management.

    Zanotuj nazwę aplikacji, na przykład `fabric:/myApplication/myService` . 

* **Wystąpienie API Management** — istniejące lub nowe API Management wystąpienie w warstwie **Premium** lub  **Developer** oraz w tym samym regionie, w którym znajduje się klaster Service Fabric. Jeśli potrzebujesz, [Utwórz wystąpienie API Management](get-started-create-service-instance.md).

* **Sieć wirtualna** — dodaj wystąpienie API Management do sieci wirtualnej utworzonej dla klastra Service Fabric. API Management wymaga dedykowanej podsieci w sieci wirtualnej.

  Aby uzyskać instrukcje dotyczące włączania łączności sieci wirtualnej dla wystąpienia API Management, zobacz [jak korzystać z usługi Azure API Management z sieciami wirtualnymi](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Tworzenie zaplecza — Portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Dodaj Service Fabric certyfikat klastra do API Management

Certyfikat klastra Service Fabric jest przechowywany i zarządzany w magazynie kluczy platformy Azure skojarzonym z klastrem. Dodaj ten certyfikat do wystąpienia API Management jako certyfikat klienta.

Aby dowiedzieć się, jak dodać certyfikat do wystąpienia API Management, zobacz [jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Zalecamy dodanie certyfikatu do API Management, odwołując się do certyfikatu magazynu kluczy. 

### <a name="add-service-fabric-backend"></a>Dodawanie Service Fabric zaplecza

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. W obszarze **interfejsy API** wybierz pozycję **nadkończenie**  >  **i Dodaj**.
1. Wprowadź nazwę zaplecza i opcjonalny opis
1. W polu **Typ** wybierz pozycję **Service Fabric**.
1. W polu **adres URL środowiska uruchomieniowego** wprowadź nazwę usługi Service Fabric zaplecza, do której API Management będą przesyłać żądania dalej. Przykład: `fabric:/myApplication/myService`. 
1. W polu **Maksymalna liczba ponownych prób rozpoznawania partycji** wprowadź liczbę z zakresu od 0 do 10.
1. Wprowadź punkt końcowy zarządzania klastra Service Fabric. Ten punkt końcowy jest adresem URL klastra na porcie `19080` , na przykład `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. W obszarze **certyfikat klienta** wybierz certyfikat klastra Service Fabric, który został dodany do wystąpienia API Management w poprzedniej sekcji.
1. W polu **Metoda autoryzacji punktu końcowego zarządzania** wprowadź nazwę certyfikatu odcisku palca lub x509 serwera, który jest używany przez usługę zarządzania klastrami Service Fabric na potrzeby komunikacji TLS.
1. Włącz opcję **Weryfikowanie łańcucha certyfikatów** i **Sprawdzanie poprawności ustawień nazwy certyfikatu** .
1. W polu **poświadczenia autoryzacji** podaj poświadczenia, jeśli to konieczne, aby uzyskać dostęp do skonfigurowanej usługi zaplecza w Service Fabric. W przypadku przykładowej aplikacji używanej w tym scenariuszu poświadczenia autoryzacji nie są potrzebne.
1. Wybierz przycisk **Utwórz**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Tworzenie zaplecza usługi Service Fabric":::

## <a name="use-the-backend"></a>Korzystanie z zaplecza

Aby użyć niestandardowego zaplecza, odwołuje się do niego przy użyciu [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zasad. Ta zasada przekształca domyślny podstawowy adres URL usługi wewnętrznej bazy danych przychodzącego żądania API do określonego zaplecza, w tym przypadku Service Fabric zaplecza. 

`set-backend-service`Zasady mogą być przydatne w przypadku istniejącego interfejsu API do przekształcania żądania przychodzącego do innego zaplecza niż określony w ustawieniach interfejsu API. W celach demonstracyjnych w tym artykule należy utworzyć testowy interfejs API i ustawić zasady, aby kierować żądania interfejsu API do Service Fabric zaplecza. 

### <a name="create-api"></a>Tworzenie interfejsu API

Wykonaj kroki opisane w sekcji [Dodawanie interfejsu API ręcznie](add-api-manually.md) , aby utworzyć pusty interfejs API.

* W ustawieniach interfejsu API pozostaw pusty **adres URL usługi sieci Web** .
* Dodaj **sufiks adresu URL interfejsu API**, taki jak *Sieć szkieletowa*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Tworzenie pustego interfejsu API":::

### <a name="add-get-operation-to-the-api"></a>Dodawanie operacji GET do interfejsu API

Jak pokazano w temacie [wdrażanie Service Fabric zaplecza](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), przykładowa usługa ASP.NET Core wdrożona w klastrze Service Fabric obsługuje pojedynczą OPERACJĘ HTTP GET na ścieżce URL `/api/values` .

Domyślna odpowiedź w tej ścieżce jest tablicą JSON zawierającą dwa ciągi:

```json
["value1", "value2"]
```

Aby przetestować integrację API Management z klastrem, Dodaj odpowiednią operację GET do interfejsu API na ścieżce `/api/values` :

1. Wybierz interfejs API utworzony w poprzednim kroku.
1. Wybierz opcję **+ Dodaj operację**.
1. W oknie **frontonu** wprowadź następujące wartości, a następnie wybierz pozycję **Zapisz**.

     | Ustawienie             | Wartość                             | 
    |---------------------|-----------------------------------|
    | **Nazwa wyświetlana**    | *Zaplecze testu*                       |  
    | **Adres URL** | GET                               | 
    | **Adres URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Dodawanie operacji GET do interfejsu API":::

### <a name="configure-set-backend-policy"></a>Konfigurowanie `set-backend` zasad

Dodaj [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zasady do testowego interfejsu API.

1. Na karcie **projektowanie** w sekcji **Przetwarzanie przychodzące** wybierz ikonę Edytor kodu ( **</>** ). 
1. Umieść kursor wewnątrz elementu **&lt; przychodzącego &gt;**
1. Dodaj następującą instrukcję zasad. W programie Zastąp `backend-id` nazwę zaplecze Service Fabric.

   `sf-resolve-condition`Jest warunkiem ponowienia próby, jeśli partycja klastra nie zostanie rozpoznana. Liczba ponowień została ustawiona podczas konfigurowania zaplecza.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Konfigurowanie zasad usługi dla zaplecza":::

### <a name="test-backend-api"></a>Testowanie interfejsu API zaplecza

1. Na karcie **test** wybierz operację **Pobierz** utworzoną w poprzedniej sekcji.
1. Wybierz pozycję **Wyślij**.

Po poprawnym skonfigurowaniu odpowiedź HTTP pokazuje kod powodzenia HTTP i wyświetla dane JSON zwrócone z usługi Service Fabric zaplecza.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testowanie zaplecza Service Fabric":::

## <a name="next-steps"></a>Następne kroki

* Informacje o [konfigurowaniu zasad](api-management-advanced-policies.md) do przesyłania dalej żądań do zaplecza
* Frontony można również skonfigurować przy użyciu API Management szablonów [interfejsu API REST](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)lub [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

