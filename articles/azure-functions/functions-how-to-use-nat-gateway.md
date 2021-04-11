---
title: Kontrola Azure Functions wychodzącego adresu IP z bramą NAT sieci wirtualnej platformy Azure
description: Samouczek krok po kroku pokazujący sposób konfigurowania translatora adresów sieciowych dla funkcji połączonej z siecią wirtualną platformy Azure
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658161"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Samouczek: sterowanie Azure Functions wychodzącym adresem IP przy użyciu bramy NAT sieci wirtualnej platformy Azure

Translator adresów sieciowych (NAT) upraszcza połączenia z Internetem tylko w sieci wirtualnej. W przypadku skonfigurowania w podsieci wszystkie połączenia wychodzące korzystają z określonych statycznych publicznych adresów IP. Translator adresów sieciowych może być przydatny w przypadku Azure Functions lub Web Apps, które wymagają użycia usługi innej firmy, która używa dozwolonych adresu IP jako miary zabezpieczeń. Aby dowiedzieć się więcej, zobacz [co to jest Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

W tym samouczku pokazano, jak używać translatora adresów sieciowych sieci wirtualnych do kierowania ruchu wychodzącego z funkcji wyzwalanej przez protokół HTTP. Ta funkcja umożliwia sprawdzenie własnego wychodzącego adresu IP. W ramach tego samouczka:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie aplikacji funkcji planu Premium
> * Tworzenie publicznego adresu IP
> * Tworzenie bramy translatora adresów sieciowych
> * Skonfiguruj aplikację funkcji do kierowania ruchu wychodzącego przez bramę translatora adresów sieciowych

## <a name="topology"></a>Topologia

Na poniższym diagramie przedstawiono architekturę tworzonego rozwiązania:

![Interfejs użytkownika dla integracji z bramą translatora adresów sieciowych](./media/functions-how-to-use-nat-gateway/topology.png)

Funkcje działające w planie Premium mają takie same możliwości hostingu jak aplikacje sieci Web w Azure App Service, które obejmują funkcję integracji z siecią wirtualną. Aby dowiedzieć się więcej o integracji sieci wirtualnej, w tym o rozwiązywaniu problemów i konfiguracji zaawansowanej, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku ważne jest zrozumienie adresów IP i podsieci. Możesz rozpocząć od [tego artykułu, który obejmuje podstawowe informacje dotyczące adresowania i podsieci](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Wiele dodatkowych artykułów i filmów wideo jest dostępnych w trybie online.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Jeśli funkcja integracji została już ukończona z samouczkiem [sieci wirtualnej platformy Azure](./functions-create-vnet.md) , możesz pominąć, aby [utworzyć funkcję wyzwalacza http](#create-function).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. W portalu Azure Marketplace wybierz pozycję **Sieć**  >  **Sieć wirtualna**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz ustawienia określone w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Nazwa | Wprowadź *adres* w sieci wirtualnej. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**.|

1. Wybierz pozycję **Dalej: adresy IP**, a dla **przestrzeni adresów IPv4** wprowadź *10.10.0.0/16*.

1. Wybierz pozycję **Dodaj podsieć**, a następnie wprowadź *polecenie Samouczek-NET* dla **nazwy podsieci** i *10.10.1.0/24* dla **zakresu adresów podsieci**.

    ![Karta adresy IP do tworzenia sieci wirtualnej](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Przegląd + Utwórz**. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.

1. W obszarze **Utwórz sieć wirtualną** wybierz pozycję **Utwórz**.

Następnie utworzysz aplikację funkcji w [planie Premium](functions-premium-plan.md). Ten plan zapewnia skalę bezserwerową podczas obsługi integracji sieci wirtualnej.

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcji w planie Premium

> [!NOTE]  
> Aby uzyskać najlepsze środowisko w tym samouczku, wybierz pozycję .NET for Runtime Stack i wybierz pozycję Windows dla systemu operacyjnego. Ponadto Utwórz aplikację funkcji w tym samym regionie, w którym znajduje się Twoja sieć wirtualna.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Łączenie aplikacji funkcji z siecią wirtualną

Teraz możesz połączyć swoją aplikację funkcji z siecią wirtualną.

1. W aplikacji funkcji wybierz pozycję Sieć w menu po lewej stronie, a następnie w obszarze **integracja** z **siecią** wirtualną wybierz **pozycję kliknij tutaj, aby ją skonfigurować**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Wybieranie sieci w aplikacji funkcji":::

1. Na stronie **integracja z siecią wirtualną** wybierz pozycję **Dodaj sieć wirtualną**.

1. W obszarze **stan funkcji sieci** Użyj ustawień w tabeli poniżej obrazu:

    ![Zdefiniuj sieć wirtualną aplikacji funkcji](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | Moja resourceName — Sieć wirtualna | Ta sieć wirtualna jest utworzoną wcześniej. |
    | **Podsieć** | Utwórz nową podsieć | Utwórz podsieć w sieci wirtualnej, która ma być używana przez aplikację funkcji. Integracja sieci wirtualnej musi być skonfigurowana do używania pustej podsieci. |
    | **Nazwa podsieci** | Function-Net | Nazwa nowej podsieci. |
    | **Blok adresów sieci wirtualnej** | 10.10.0.0/16 | Powinien być zdefiniowany tylko jeden blok adresu. |
    | **Blok adresów podsieci** | 10.10.2.0/24   | Rozmiar podsieci ogranicza łączną liczbę wystąpień, do których można skalować aplikację funkcji planu Premium. W tym przykładzie używa się `/24` podsieci z 254 dostępnych adresów hosta. Ta podsieć jest nadmiernie obsługiwana, ale prosta do obliczenia. |

1. Wybierz **przycisk OK** , aby dodać podsieć. Zamknij strony **Integracja sieci wirtualnej** i **stan funkcji sieciowych** , aby powrócić do strony aplikacji funkcji.

Aplikacja funkcji może teraz uzyskiwać dostęp do sieci wirtualnej. Następnie dodasz funkcję wyzwalaną przez protokół HTTP do aplikacji funkcji.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalacza HTTP

1. W menu po lewej stronie okna **funkcje** wybierz pozycję **funkcje**, a następnie wybierz pozycję **Dodaj** z górnego menu. 
 
1. W oknie **Nowa funkcja** wybierz pozycję **wyzwalacz http** i zaakceptuj nazwę domyślną dla **nowej funkcji** lub wprowadź nową nazwę. 

1. W **kodzie + test** Zastąp wygenerowany przez szablon kod skryptu C# (. CSX) następującym kodem: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Ten kod wywołuje zewnętrzną witrynę sieci Web, która zwraca adres IP obiektu wywołującego, w tym przypadku jest to funkcja. Ta metoda pozwala łatwo określić wychodzący adres IP używany przez aplikację funkcji.

Teraz możesz uruchomić funkcję i sprawdzić bieżące wychodzące adresy IP.

## <a name="verify-current-outbound-ips"></a>Weryfikuj bieżące wychodzące adresy IP

Teraz można uruchomić funkcję. Najpierw należy zaewidencjonować Portal i zobaczyć, jakie wychodzące adresy IP są używane przez aplikację funkcji.  

1. W aplikacji funkcji wybierz pozycję **Właściwości** i sprawdź pole **wychodzące adresy IP** .

    ![Wyświetl wychodzące adresy IP aplikacji funkcji](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Teraz wróć do funkcji wyzwalacza HTTP, wybierz pozycję **Code + test** , a następnie **Test/Run**.

    ![Funkcja testowa](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Wybierz pozycję **Uruchom** , aby wykonać funkcję, a następnie przejdź do **danych wyjściowych**. 

    ![Dane wyjściowe funkcji testowej](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Sprawdź, czy adres IP w treści odpowiedzi HTTP to jedna z wartości z wychodzących adresów IP, które były wyświetlane wcześniej.

Teraz można utworzyć publiczny adres IP i użyć bramy translatora adresów sieciowych w celu zmodyfikowania tego wychodzącego adresu IP.

## <a name="create-public-ip"></a>Utwórz publiczny adres IP

1. W grupie zasobów wybierz pozycję **Dodaj**, Wyszukaj w witrynie Azure Marketplace pozycję **publiczny adres IP**, a następnie wybierz pozycję **Utwórz**. Użyj ustawień w tabeli poniżej obrazu:

    ![Utwórz publiczny adres IP](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Ustawienie      | Sugerowana wartość  |
    | ------------ | ---------------- |
    | **Wersja protokołu IP** | Protokół IPv4 |
    | **SKU** | Standardowa (Standard) |
    | **Warstwa** | Regionalne |
    | **Nazwa** | Ruch wychodzący — IP |
    | **Subskrypcja** | Upewnij się, że Twoja subskrypcja jest wyświetlana | 
    | **Grupa zasobów** | Grupa zasobów (lub nazwa przypisana do tej grupy) |
    | **Lokalizacja** | Wschodnie stany USA (lub lokalizacja przypisana do innych zasobów) |
    | **Strefa dostępności** | Brak strefy |

1. Wybierz pozycję **Utwórz** , aby przesłać wdrożenie.

1. Po zakończeniu wdrażania przejdź do nowo utworzonego zasobu publicznego adresu IP i Wyświetl adres IP w **przeglądzie**.

    ![Wyświetl publiczny adres IP](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

Teraz Utwórzmy bramę translatora adresów sieciowych. Po rozpoczęciu pracy z [poprzednim samouczkiem dotyczącym sieci wirtualnych](functions-create-vnet.md) `Function-Net` była sugerowaną nazwą podsieci i `MyResourceGroup-vnet` była sugerowaną nazwą sieci wirtualnej w tym samouczku.

1. W grupie zasobów wybierz pozycję **Dodaj**, Wyszukaj pozycję Azure Marketplace for **translator adresów sieciowych**, a następnie wybierz pozycję **Utwórz**. Użyj ustawień z tabeli poniżej obrazu, aby wypełnić kartę **podstawowe** :

    ![Tworzenie bramy translatora adresów sieciowych](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Ustawienie      | Sugerowana wartość  |
    | ------------ | ---------------- |  
    | **Subskrypcja** | Twoja subskrypcja | 
    | **Grupa zasobów** | Grupa zasobów (lub nazwa przypisana do tej grupy) |
    | **Nazwa bramy translatora adresów sieciowych** | myNatGateway |
    | **Region** | Wschodnie stany USA (lub lokalizacja przypisana do innych zasobów) |
    | **Strefa dostępności** | Brak |

1. Wybierz pozycję **Dalej: wychodzący adres IP**. W polu **publiczne adresy IP** wybierz wcześniej utworzony publiczny adres IP. Pozostaw niewybrane **prefiksy publicznych adresów IP** .

1. Wybierz pozycję **Dalej: podsieć**. Wybierz zasób *zasoby sieci wirtualnej w* polu **Sieć wirtualna** i podsieć *funkcji* .

    ![Wybierz podsieć](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Wybierz kolejno pozycje **Przegląd + Utwórz i** **Utwórz, aby przesłać** wdrożenie.

Po zakończeniu wdrożenia Brama translatora adresów sieciowych będzie gotowa do kierowania ruchem z podsieci aplikacji funkcji do Internetu.

## <a name="update-function-configuration"></a>Aktualizuj konfigurację funkcji

Teraz należy dodać ustawienie aplikacji `WEBSITE_VNET_ROUTE_ALL` ustawione na wartość `1` .  To ustawienie wymusza ruch wychodzący za pomocą sieci wirtualnej i skojarzonej bramy translatora adresów sieciowych. Bez tego ustawienia ruch internetowy nie jest kierowany przez zintegrowaną sieć wirtualną i zobaczysz te same wychodzące adresy IP. 

1. Przejdź do aplikacji funkcji w Azure Portal a następnie wybierz pozycję **Konfiguracja** w menu po lewej stronie.

1. W obszarze **Ustawienia aplikacji** wybierz pozycję **+ nowe ustawienie aplikacji** i wypełnij pola, używając następujących wartości:

    |Nazwa pola  |Wartość |
    |---|---|
    |**Nazwa**    |WEBSITE_VNET_ROUTE_ALL|
    |**Wartość**   |1|

1. Wybierz **przycisk OK** , aby zamknąć okno dialogowe nowe ustawienie aplikacji.

1. Wybierz pozycję **Zapisz** , a następnie **Kontynuuj** , aby zapisać ustawienia.

Aplikacja funkcji jest teraz skonfigurowana do kierowania ruchem za pomocą skojarzonej z nim sieci wirtualnej.

## <a name="verify-new-outbound-ips"></a>Weryfikuj nowe wychodzące adresy IP

Powtórz [te kroki,](#verify-current-outbound-ips) aby ponownie uruchomić funkcję. Powinien być teraz widoczny wychodzący adres IP skonfigurowany w translatorze adresów sieciowych przedstawionym w danych wyjściowych funkcji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby zostały utworzone w celu ukończenia tego samouczka. Opłaty zostaną naliczone za te zasoby, w zależności od [stanu konta](https://azure.microsoft.com/account/) i [cen usług](https://azure.microsoft.com/pricing/). Aby uniknąć ponoszenia dodatkowych kosztów, Usuń zasoby, jeśli już nie będą potrzebne. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Opcje sieciowe usługi Azure Functions](functions-networking-options.md)
