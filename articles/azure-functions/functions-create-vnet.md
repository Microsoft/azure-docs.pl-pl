---
title: Używanie prywatnych punktów końcowych do integrowania Azure Functions z siecią wirtualną
description: Samouczek krok po kroku pokazujący, jak połączyć funkcję z siecią wirtualną platformy Azure i zablokować ją za pomocą prywatnych punktów końcowych
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200210"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Samouczek: Integrowanie Azure Functions z siecią wirtualną platformy Azure za pomocą prywatnych punktów końcowych

W tym samouczku pokazano, jak używać Azure Functions do nawiązywania połączeń z zasobami w sieci wirtualnej platformy Azure z prywatnymi punktami końcowymi. Utworzysz funkcję z kontem magazynu zablokowanym za siecią wirtualną używającą wyzwalacza kolejki usługi Service Bus.

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji w planie Premium
> * Tworzenie zasobów platformy Azure (Service Bus, konto magazynu Virtual Network)
> * Zablokuj konto magazynu za prywatnym punktem końcowym
> * Zablokuj Service Bus za prywatnym punktem końcowym
> * Wdróż aplikację funkcji z wyzwalaczami Service Bus i HTTP.
> * Zablokuj aplikację funkcji za prywatnym punktem końcowym
> * Przetestuj, aby zobaczyć, że aplikacja funkcji jest zabezpieczona za siecią wirtualną
> * Czyszczenie zasobów

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcji w planie Premium

Najpierw należy utworzyć aplikację funkcji platformy .NET w [planie Premium] , ponieważ ten samouczek będzie używać języka C#. Inne języki są również obsługiwane w systemie Windows. Ten plan zapewnia skalę bezserwerową podczas obsługi integracji sieci wirtualnej.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **obliczeniowe**  >  **aplikacja funkcji**.

1. Na stronie **podstawowe** Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Kod | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | .NET | Ten samouczek używa platformy .NET |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

1. Wybierz pozycję **Dalej: hosting**. Na stronie **hosting** wprowadź następujące ustawienia:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](./storage-considerations.md#storage-account-requirements). |
    |**System operacyjny**| Windows | Ten samouczek używa systemu Windows |
    | **[Planowanie](./functions-scale.md)** | Premium | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Wybierz pozycję **Premium**. Domyślnie tworzony jest nowy plan App Service. Domyślną jednostką **SKU i rozmiarem** jest **EP1**, gdzie EP oznacza _elastyczną usługę Premium_. Aby dowiedzieć się więcej, zobacz [listę jednostek SKU w warstwie Premium](./functions-premium-plan.md#available-instance-skus).<br/>Podczas uruchamiania funkcji JavaScript w planie Premium należy wybrać wystąpienie, które ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybierz pojedyncze podstawowe plany Premium](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Domyślne | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację w lokalizacji** [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) do przechowywania danych. |

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i obejrzyj komunikat **wdrażanie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

1. Gratulacje! Pomyślnie utworzono aplikację funkcji Premium.

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Dla sieci wirtualnych wymagana jest osobne konto magazynu utworzone w ramach początkowego tworzenia aplikacji funkcji.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie nowy Wyszukaj pozycję **konto magazynu** i wybierz pozycję **Utwórz** .

1. Na karcie **podstawy** Ustaw ustawienia zgodnie z opisem w poniższej tabeli. Resztę można pozostawić jako domyślną:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz grupę zasobów utworzoną za pomocą aplikacji funkcji. |
    | **Nazwa** | mysecurestorage| Nazwa konta magazynu, do którego zostanie zastosowany prywatny punkt końcowy. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wybierz region, w którym została utworzona aplikacja funkcji. |

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

### <a name="create-a-service-bus"></a>Tworzenie Service Bus

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie nowy Wyszukaj **Service Bus** a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawy** Ustaw ustawienia zgodnie z opisem w poniższej tabeli. Resztę można pozostawić jako domyślną:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. |
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz grupę zasobów utworzoną za pomocą aplikacji funkcji. |
    | **Nazwa** | myServiceBus| Nazwa usługi Service Bus, do której zostanie zastosowany prywatny punkt końcowy. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wybierz region, w którym została utworzona aplikacja funkcji. |
    | **Warstwa cenowa** | Premium | Wybierz tę warstwę, aby używać prywatnych punktów końcowych z Service Bus. |

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zasoby platformy Azure w tym samouczku integrują się z lub są umieszczane w sieci wirtualnej. Będziesz używać prywatnych punktów końcowych, aby zachować ruch sieciowy zawarty w sieci wirtualnej.

Samouczek tworzy dwie podsieci:
- **Domyślnie**: podsieć dla prywatnych punktów końcowych. Prywatne adresy IP są podawane z tej podsieci.
- **funkcje**: podsieć do Azure Functions integracji z siecią wirtualną. Ta podsieć jest delegowana do aplikacji funkcji.

Teraz Utwórz sieć wirtualną, do której jest zintegrowana aplikacja funkcji.

1. W menu witryny Azure Portal lub na stronie głównej wybierz pozycję **Utwórz zasób**.

1. Na stronie nowy Wyszukaj **Virtual Network** a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Użyj ustawień sieci wirtualnej, jak określono poniżej:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz grupę zasobów utworzoną za pomocą aplikacji funkcji. |
    | **Nazwa** | myVirtualNet| Nazwa sieci wirtualnej, z którą zostanie nawiązane połączenie aplikacja funkcji. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wybierz region, w którym została utworzona aplikacja funkcji. |

1. Na karcie **adresy IP** wybierz pozycję **Dodaj podsieć**. Użyj ustawień określonych poniżej podczas dodawania podsieci:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Zrzut ekranu przedstawiający widok konfiguracja sieci wirtualnej.":::

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa podsieci** |  — funkcje | Nazwa podsieci, z którą zostanie nawiązane połączenie aplikacja funkcji. | 
    | **Zakres adresów podsieci** | 10.0.1.0/24 | Zwróć uwagę na to, że przestrzeń adresowa IPv4 na powyższym obrazie to 10.0.0.0/16. W przypadku 10.1.0.0/16 zalecanym *zakresem adresów podsieci* będzie 10.1.1.0/24. |

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Zablokuj konto magazynu z prywatnymi punktami końcowymi

Prywatne punkty końcowe platformy Azure są używane do nawiązywania połączenia z określonymi zasobami platformy Azure przy użyciu prywatnego adresu IP. To połączenie zapewnia, że ruch sieciowy pozostaje w wybranej sieci wirtualnej, a dostęp jest dostępny tylko dla określonych zasobów. Teraz Utwórz prywatne punkty końcowe dla usługi Azure File Storage i usługi Azure Blob Storage za pomocą konta magazynu.

1. Na nowym koncie magazynu wybierz pozycję **Sieć** w menu po lewej stronie.

1. Wybierz kartę **połączenia prywatne punktu końcowego** , a następnie wybierz pozycję **prywatny punkt końcowy**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania po tworzeniu prywatnych punktów końcowych dla konta magazynu.":::

1. Na karcie **podstawowe** Użyj ustawień prywatnego punktu końcowego zgodnie z poniższym opisem:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz grupę zasobów utworzoną za pomocą aplikacji funkcji. | |
    | **Nazwa** | plik — punkt końcowy | Nazwa prywatnego punktu końcowego dla plików z konta magazynu. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wybierz region, w którym utworzono konto magazynu. |

1. Na karcie **zasób** Użyj ustawień prywatnego punktu końcowego zgodnie z poniższym opisem:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **Typ zasobu**  | Microsoft. Storage/storageAccounts | Jest to typ zasobu dla kont magazynu. |
    | **Zasób** | mysecurestorage | Utworzone konto magazynu |
    | **Docelowy zasób podrzędny** |  — plik | Ten prywatny punkt końcowy będzie używany dla plików z konta magazynu. |

1. Na karcie **Konfiguracja** wybierz opcję **domyślne** ustawienia podsieci.

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Zasoby w sieci wirtualnej mogą teraz komunikować się z plikami magazynu.

1. Utwórz inny prywatny punkt końcowy dla obiektów BLOB. Na karcie **zasoby** Użyj poniższych ustawień. W przypadku wszystkich innych ustawień należy użyć tych samych ustawień w ramach kroków tworzenia prywatnego punktu końcowego pliku.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **Typ zasobu**  | Microsoft. Storage/storageAccounts | Jest to typ zasobu dla kont magazynu. |
    | **Zasób** | mysecurestorage | Utworzone konto magazynu |
    | **Docelowy zasób podrzędny** | blob | Ten prywatny punkt końcowy będzie używany dla obiektów blob z konta magazynu. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Zablokuj usługę Service Bus za pomocą prywatnego punktu końcowego

Teraz Utwórz prywatny punkt końcowy dla Azure Service Bus.

1. W nowej magistrali usług wybierz pozycję **Sieć** w menu po lewej stronie.

1. Wybierz kartę **połączenia prywatne punktu końcowego** , a następnie wybierz pozycję **prywatny punkt końcowy**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do prywatnych punktów końcowych usługi Service Bus.":::

1. Na karcie **podstawowe** Użyj ustawień prywatnego punktu końcowego zgodnie z poniższym opisem:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz grupę zasobów utworzoną za pomocą aplikacji funkcji. |
    | **Nazwa** | SB — punkt końcowy | Nazwa prywatnego punktu końcowego dla plików z konta magazynu. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wybierz region, w którym utworzono konto magazynu. |

1. Na karcie **zasób** Użyj ustawień prywatnego punktu końcowego zgodnie z poniższym opisem:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **Typ zasobu**  | Microsoft. ServiceBus/przestrzenie nazw | Jest to typ zasobu dla Service Bus. |
    | **Zasób** | myServiceBus | Service Bus utworzony wcześniej w samouczku. |
    | **Podzasób docelowy** | namespace | Ten prywatny punkt końcowy będzie używany dla przestrzeni nazw usługi Service Bus. |

1. Na karcie **Konfiguracja** wybierz opcję **domyślne** ustawienia podsieci.

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Zasoby w sieci wirtualnej mogą teraz komunikować się z usługą Service Bus.

## <a name="create-a-file-share"></a>Tworzenie udziału plików

1. W utworzonym koncie magazynu wybierz pozycję **udziały plików** w menu po lewej stronie.

1. Wybierz pozycję **+ udziały plików**. Podaj **pliki** jako nazwę udziału plików na potrzeby tego samouczka.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia udziału plików na koncie magazynu.":::

## <a name="get-storage-account-connection-string"></a>Pobierz parametry połączenia konta magazynu

1. W utworzonym koncie magazynu wybierz pozycję **klucze dostępu** w menu po lewej stronie.

1. Wybierz pozycję **Pokaż klucze**. Skopiuj parametry połączenia Klucz1 i Zapisz je. Te parametry połączenia będą potrzebne później podczas konfigurowania ustawień aplikacji.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania parametrów połączenia z kontem magazynu.":::

## <a name="create-a-queue"></a>Tworzenie kolejki

Będzie to kolejka, dla której wyzwalacz Service Bus Azure Functions będzie pobierać zdarzenia.

1. W usłudze Service Bus wybierz pozycję **Queues (kolejki** ) w menu po lewej stronie.

1. Wybierz pozycję **zasady dostępu współdzielonego**. Podaj **kolejkę** jako nazwę kolejki na potrzeby tego samouczka.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia kolejki usługi Service Bus.":::

## <a name="get-service-bus-connection-string"></a>Pobierz parametry połączenia usługi Service Bus

1. W usłudze Service Bus wybierz pozycję **zasady dostępu współdzielonego** w menu po lewej stronie.

1. Wybierz pozycję **RootManageSharedAccessKey**. Skopiuj **podstawowe parametry połączenia** i Zapisz je. Te parametry połączenia będą potrzebne później podczas konfigurowania ustawień aplikacji.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania parametrów połączenia usługi Service Bus.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Integruj aplikację funkcji z siecią wirtualną

Aby użyć aplikacji funkcji z sieciami wirtualnymi, musisz dołączyć ją do podsieci. Używana jest określona podsieć dla integracji z siecią wirtualną Azure Functions i domyślna podsieć dla wszystkich innych prywatnych punktów końcowych utworzonych w tym samouczku.

1. W aplikacji funkcji wybierz pozycję **Sieć** w menu po lewej stronie.

1. Wybierz **pozycję kliknij tutaj, aby skonfigurować** w obszarze integracja z siecią wirtualną.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do integracji z siecią wirtualną.":::

1. Wybierz pozycję **Dodaj sieć wirtualną**

1. W bloku otwartym w obszarze **Virtual Network** Wybierz utworzoną wcześniej sieć wirtualną.

1. Wybierz **podsieć** , która została utworzona wcześniej o nazwie **Functions**. Aplikacja funkcji jest teraz zintegrowana z siecią wirtualną.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Zrzut ekranu przedstawiający sposób łączenia aplikacji funkcji z podsiecią.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Skonfiguruj ustawienia aplikacji funkcji dla prywatnych punktów końcowych

1. W aplikacji funkcji wybierz pozycję **Konfiguracja** w menu po lewej stronie.

1. Aby można było korzystać z aplikacji funkcji z sieciami wirtualnymi, należy zaktualizować następujące ustawienia aplikacji. W razie potrzeby wybierz opcję **+ nowe ustawienie aplikacji** lub ołówek, **edytując** w kolumnie z prawej stronie tabeli Ustawienia aplikacji. Po zakończeniu wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania ustawień aplikacji funkcji dla prywatnych punktów końcowych.":::

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Parametry połączenia utworzonego konta magazynu. To są parametry połączenia magazynu z [pobieranymi parametrami połączenia konta magazynu](#get-storage-account-connection-string). Zmiana tego ustawienia spowoduje, że aplikacja funkcji użyje teraz konta bezpiecznego magazynu do normalnych operacji w czasie wykonywania. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Parametry połączenia utworzonego konta magazynu. Zmiana tego ustawienia spowoduje, że aplikacja funkcji użyje teraz konta bezpiecznego magazynu dla Azure Files, które są używane podczas wdrażania. |
    | **WEBSITE_CONTENTSHARE** | files | Nazwa udziału plików utworzonego na koncie magazynu. To ustawienie aplikacji jest używane w połączeniu z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Utwórz ustawienie aplikacji dla parametrów połączenia usługi Service Bus. To są parametry połączenia magazynu z [parametrów połączenia z usługą Service Bus](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Utwórz to ustawienie aplikacji. Wartość 1 umożliwia skalowanie aplikacji funkcji, gdy konto magazynu jest ograniczone do sieci wirtualnej. To ustawienie należy włączyć w przypadku ograniczania konta magazynu do sieci wirtualnej. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Utwórz to ustawienie aplikacji. Gdy aplikacja zostanie zintegrowana z siecią wirtualną, użyje tego samego serwera DNS co sieć wirtualna. Jest to jedno z dwóch ustawień, które wymagają, aby aplikacja funkcji działała z Azure DNS strefami prywatnymi i jest wymagana w przypadku korzystania z prywatnych punktów końcowych. Te ustawienia będą wysyłać wszystkie wywołania wychodzące z aplikacji do sieci wirtualnej. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Utwórz to ustawienie aplikacji. Gdy aplikacja zostanie zintegrowana z siecią wirtualną, użyje tego samego serwera DNS co sieć wirtualna. Jest to jedno z dwóch ustawień, które wymagają, aby aplikacja funkcji działała z Azure DNS strefami prywatnymi i jest wymagana w przypadku korzystania z prywatnych punktów końcowych. Te ustawienia będą wysyłać wszystkie wywołania wychodzące z aplikacji do sieci wirtualnej. |

1. W widoku **Konfiguracja** wybierz kartę **Ustawienia środowiska uruchomieniowego funkcji** .

1. Ustaw pozycję **monitorowanie skalowania w czasie wykonywania** **na wartość włączone**, a następnie wybierz pozycję **Zapisz**. Skalowanie sterowane przez środowisko uruchomieniowe umożliwia łączenie funkcji wyzwalaczy innych niż HTTP z usługami uruchomionymi w sieci wirtualnej.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Zrzut ekranu przedstawiający sposób włączania skalowania sterowanego przez środowisko uruchomieniowe dla Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Wdrażanie wyzwalacza usługi Service Bus i wyzwalacza http w aplikacji funkcji

1. W usłudze GitHub przejdź do następującego przykładowego repozytorium, które zawiera aplikację funkcji z dwoma funkcjami, wyzwalaczem HTTP i wyzwalaczem Service Bus kolejki.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. W górnej części strony wybierz przycisk **rozwidlenie** , aby utworzyć rozwidlenie tego repozytorium we własnym koncie usługi GitHub lub w organizacji.

1. W aplikacji funkcji wybierz pozycję **centrum wdrażania** w menu po lewej stronie. Następnie wybierz pozycję **Ustawienia**.

1. Na karcie **Ustawienia** Użyj ustawień wdrożenia określonych poniżej:

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Element źródłowy** | GitHub | Należy utworzyć repozytorium GitHub z przykładowym kodem w kroku 2. | 
    | **Organizacja**  | Organizacja | Jest to organizacja, do której repozytorium jest sprawdzane, zazwyczaj Twoje konto. |
    | **Repozytorium** | moje repozytorium | Repozytorium utworzone za pomocą przykładowego kodu. |
    | **Gałąź** | main | To właśnie utworzone repozytorium, więc Użyj głównej gałęzi. |
    | **Stos środowiska uruchomieniowego** | .NET | Przykładowy kod jest w języku C#. |

1. Wybierz pozycję **Zapisz**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Zrzut ekranu przedstawiający sposób wdrażania kodu Azure Functions za pomocą portalu.":::

1. Początkowe wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu aplikacji zostanie wyświetlony komunikat o stanie **powodzenie (aktywny)** na karcie **dzienniki** . W razie konieczności Odśwież stronę. 

1. Gratulacje! Pomyślnie wdrożono przykładową aplikację funkcji.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Zablokuj aplikację funkcji za pomocą prywatnego punktu końcowego

Teraz Utwórz prywatny punkt końcowy dla aplikacji funkcji. Ten prywatny punkt końcowy łączy swoją aplikację funkcji prywatnie i bezpiecznie z siecią wirtualną przy użyciu prywatnego adresu IP. Aby uzyskać więcej informacji na temat prywatnych punktów końcowych, przejdź do [dokumentacji prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. W aplikacji funkcji wybierz pozycję **Sieć** w menu po lewej stronie.

1. Wybierz **pozycję kliknij tutaj, aby skonfigurować** w obszarze połączenia prywatnego punktu końcowego.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do aplikacja funkcji prywatnego punktu końcowego.":::

1. Wybierz pozycję **Dodaj**.

1. W wyświetlonym menu Użyj ustawień prywatnego punktu końcowego zgodnie z poniższym opisem:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia aplikacja funkcji prywatnego punktu końcowego.":::

1. Wybierz **przycisk OK** , aby dodać prywatny punkt końcowy. Gratulacje! Pomyślnie zainstalowano aplikację funkcji, usługę Service Bus i konto magazynu z prywatnymi punktami końcowymi!

### <a name="test-your-locked-down-function-app"></a>Przetestuj zablokowaną aplikację funkcji

1. W aplikacji funkcji wybierz pozycję **funkcje** w menu po lewej stronie.

1. Wybierz **ServiceBusQueueTrigger**.

1. Z menu po lewej stronie wybierz pozycję **Monitoruj**. zobaczysz, że nie możesz monitorować aplikacji. Dzieje się tak, ponieważ przeglądarka nie ma dostępu do sieci wirtualnej, dlatego nie może bezpośrednio uzyskać dostępu do zasobów w sieci wirtualnej. Teraz zobaczymy kolejną metodę, za pomocą której można nadal monitorować funkcję, Application Insights.

1. W aplikacji funkcji wybierz pozycję **Application Insights** z menu po lewej stronie i wybierz pozycję **Wyświetl Application Insights dane**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania usługi Application Insights dla aplikacja funkcji.":::

1. Wybierz pozycję **metryki na żywo** z menu po lewej stronie.

1. Otwórz nową kartę. W Service Bus wybierz **kolejno pozycje kolejki** z menu po lewej stronie.

1. Wybierz kolejkę.

1. Z menu po lewej stronie wybierz pozycję **eksplorator Service Bus** . W obszarze **Wyślij** wybierz opcję **tekst/zwykły** jako **Typ zawartości** i wprowadź komunikat. 

1. Wybierz pozycję **Wyślij** , aby wysłać wiadomość.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Zrzut ekranu przedstawiający sposób wysyłania komunikatów Service Bus przy użyciu portalu.":::

1. Na karcie z otwartymi **metrykami na żywo** zobaczysz, że wyzwalacz Service Bus kolejki został wyzwolony. Jeśli nie, Wyślij ponownie komunikat z programu **Service Bus Explorer**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania komunikatów przy użyciu metryk na żywo dla aplikacji funkcji.":::

1. Gratulacje! Pomyślnie przetestowano aplikację funkcji skonfigurowaną z prywatnymi punktami końcowymi!

### <a name="private-dns-zones"></a>Strefy Prywatna strefa DNS
Połączenie z zasobami platformy Azure przy użyciu prywatnego punktu końcowego oznacza połączenie z prywatnym adresem IP, a nie publicznym punktem końcowym. Istniejące usługi platformy Azure są skonfigurowane do łączenia się z publicznym punktem końcowym za pomocą istniejącego systemu DNS. Aby nawiązać połączenie z prywatnym punktem końcowym, należy zastąpić konfigurację DNS.

Dla każdego zasobu platformy Azure skonfigurowanego za pomocą prywatnego punktu końcowego utworzono prywatną strefę DNS. Tworzony jest rekord DNS A dla każdego prywatnego adresu IP skojarzonego z prywatnym punktem końcowym.

W tym samouczku zostały utworzone następujące strefy DNS:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz aplikację funkcji premium, konto magazynu i Service Bus, a wszystkie elementy są zabezpieczone wszystkimi za prywatnymi punktami końcowymi. Dowiedz się więcej o różnych funkcjach sieciowych dostępnych poniżej:

> [!div class="nextstepaction"]
> [Dowiedz się więcej o opcjach sieci w funkcjach](./functions-networking-options.md)

[Plan Premium]: functions-premium-plan.md
