---
title: Używanie prywatnych punktów końcowych do integrowania Azure Functions z siecią wirtualną
description: W tym samouczku pokazano, jak połączyć funkcję z siecią wirtualną platformy Azure i zablokować ją przy użyciu prywatnych punktów końcowych.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: e1ed944250f05f52860c47f6cb61130f50b08e7c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078778"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Samouczek: Integrowanie Azure Functions z siecią wirtualną platformy Azure za pomocą prywatnych punktów końcowych

W tym samouczku pokazano, jak używać usługi Azure Functions do nawiązywania połączeń z zasobami w sieci wirtualnej platformy Azure za pomocą prywatnych punktów końcowych. Utworzysz funkcję przy użyciu konta magazynu zablokowanego za siecią wirtualną. Sieć wirtualna używa wyzwalacza kolejki usługi Service Bus.

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Utwórz aplikację funkcji w planie Premium.
> * Tworzenie zasobów platformy Azure, takich jak Magistrala usług, konto magazynu i Sieć wirtualna.
> * Zablokuj konto magazynu za prywatnym punktem końcowym.
> * Zablokuj usługę Service Bus za prywatnym punktem końcowym.
> * Wdróż aplikację funkcji, która używa zarówno usługi Service Bus, jak i wyzwalaczy HTTP.
> * Zablokuj aplikację funkcji za prywatnym punktem końcowym.
> * Przetestuj, aby zobaczyć, że aplikacja funkcji jest bezpieczna wewnątrz sieci wirtualnej.
> * Wyczyść zasoby.

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcji w planie Premium

W planie Premium utworzysz aplikację funkcji platformy .NET, ponieważ w tym samouczku zostanie użyty język C#. Inne języki są również obsługiwane w systemie Windows. Plan Premium zapewnia skalę bezserwerową, jednocześnie wspierając integrację sieci wirtualnej.

1. W menu Azure Portal lub stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **obliczeniowe**  >  **aplikacja funkcji**.

1. Na stronie **podstawowe** Skorzystaj z poniższej tabeli, aby skonfigurować ustawienia aplikacji funkcji.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona nowa aplikacja funkcji. |
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której utworzysz aplikację funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Kod | Wybierz Publikowanie plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | .NET | Ten samouczek używa platformy .NET. |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w niemal innych usługach, do których dostęp ma funkcja. |

1. Wybierz pozycję **Dalej: hosting**. Na stronie **hosting** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków. Mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](./storage-considerations.md#storage-account-requirements). |
    |**System operacyjny**| Windows | Ten samouczek używa systemu Windows. |
    | **[Planowanie](./functions-scale.md)** | Premium | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Domyślnie po wybraniu opcji **Premium** zostanie utworzony nowy plan App Service. Domyślną jednostką **SKU i rozmiarem** jest **EP1**, gdzie *EP* oznacza _elastyczną usługę Premium_. Aby uzyskać więcej informacji, zobacz listę [jednostek SKU w warstwie Premium](./functions-premium-plan.md#available-instance-skus).<br/><br/>Po uruchomieniu funkcji języka JavaScript w planie Premium wybierz wystąpienie, które ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybierz pojedyncze podstawowe plany Premium](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Domyślne | Utwórz zasób Application Insights o tej samej nazwie aplikacji w najbliższym obsługiwanym regionie. Rozwiń to ustawienie, jeśli chcesz zmienić **nową nazwę zasobu** lub przechować dane w innej **lokalizacji** w obszarze [geograficznym platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** Sprawdź ustawienia. Następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. W prawym górnym rogu portalu wybierz ikonę **powiadomienia** i obejrzyj komunikat **wdrożenie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

Gratulacje! Pomyślnie utworzono aplikację funkcji Premium.

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Następnie utworzysz konto magazynu, magistralę usług i sieć wirtualną. 
### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Twoje sieci wirtualne będą potrzebować konta magazynu innego niż to, które zostało utworzone za pomocą aplikacji funkcji.

1. W menu Azure Portal lub stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na **nowej** stronie Wyszukaj *konto magazynu*. Następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Skorzystaj z poniższej tabeli, aby skonfigurować ustawienia konta magazynu. Wszystkie inne ustawienia mogą korzystać z wartości domyślnych.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Grupa zasobów utworzona za pomocą aplikacji funkcji. |
    | **Nazwa** | mysecurestorage| Nazwa konta magazynu, do którego zostanie zastosowany prywatny punkt końcowy. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Region, w którym została utworzona aplikacja funkcji. |

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

### <a name="create-a-service-bus"></a>Tworzenie usługi Service Bus

1. W menu Azure Portal lub stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na **nowej** stronie Wyszukaj *usługę Service Bus*. Następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Skorzystaj z poniższej tabeli, aby skonfigurować ustawienia usługi Service Bus. Wszystkie inne ustawienia mogą korzystać z wartości domyślnych.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. |
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Grupa zasobów utworzona za pomocą aplikacji funkcji. |
    | **Nazwa** | myServiceBus| Nazwa magistrali usług, do której zostanie zastosowany prywatny punkt końcowy. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Region, w którym została utworzona aplikacja funkcji. |
    | **Warstwa cenowa** | Premium | Wybierz tę warstwę, aby używać prywatnych punktów końcowych z Azure Service Bus. |

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zasoby platformy Azure w tym samouczku integrują się z lub są umieszczane w sieci wirtualnej. Będziesz używać prywatnych punktów końcowych do przechowywania ruchu sieciowego w sieci wirtualnej.

Samouczek tworzy dwie podsieci:
- **Domyślnie**: podsieć dla prywatnych punktów końcowych. Prywatne adresy IP są podawane z tej podsieci.
- **funkcje**: podsieć do Azure Functions integracji z siecią wirtualną. Ta podsieć jest delegowana do aplikacji funkcji.

Utwórz sieć wirtualną, z którą integruje się aplikacja funkcji:

1. W menu Azure Portal lub stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na **nowej** stronie Wyszukaj pozycję *Sieć wirtualna*. Następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Skorzystaj z poniższej tabeli, aby skonfigurować ustawienia sieci wirtualnej.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Grupa zasobów utworzona za pomocą aplikacji funkcji. |
    | **Nazwa** | myVirtualNet| Nazwa sieci wirtualnej, z którą zostanie nawiązane połączenie aplikacja funkcji. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Region, w którym została utworzona aplikacja funkcji. |

1. Na karcie **adresy IP** wybierz pozycję **Dodaj podsieć**. Skorzystaj z poniższej tabeli, aby skonfigurować ustawienia podsieci.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Zrzut ekranu przedstawiający widok konfiguracja sieci wirtualnej.":::

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa podsieci** |  — funkcje | Nazwa podsieci, z którą zostanie nawiązane połączenie aplikacja funkcji. | 
    | **Zakres adresów podsieci** | 10.0.1.0/24 | Zakres adresów podsieci. Na powyższym obrazie należy zauważyć, że przestrzeń adresów IPv4 jest 10.0.0.0/16. Jeśli wartość to 10.1.0.0/16, zalecanym zakresem adresów podsieci będzie 10.1.1.0/24. |

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

## <a name="lock-down-your-storage-account"></a>Zablokuj konto magazynu

Prywatne punkty końcowe platformy Azure są używane do nawiązywania połączenia z określonymi zasobami platformy Azure przy użyciu prywatnego adresu IP. To połączenie zapewnia, że ruch sieciowy pozostaje w wybranej sieci wirtualnej, a dostęp jest dostępny tylko dla określonych zasobów. 

Utwórz prywatne punkty końcowe dla usług Azure Files Storage i Azure Blob Storage przy użyciu konta magazynu:

1. Na nowym koncie magazynu w menu po lewej stronie wybierz pozycję **Sieć**.

1. Na karcie **połączenia prywatnego punktu końcowego** wybierz pozycję **prywatny punkt końcowy**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia prywatnych punktów końcowych dla konta magazynu.":::

1. Na karcie **podstawowe** Użyj ustawień prywatnych punktów końcowych przedstawionych w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz grupę zasobów utworzoną za pomocą aplikacji funkcji. |
    | **Nazwa** | plik — punkt końcowy | Nazwa prywatnego punktu końcowego dla plików z konta magazynu. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Wybierz region, w którym utworzono konto magazynu. |

1. Na karcie **zasób** Użyj ustawień prywatnych punktów końcowych przedstawionych w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **Typ zasobu**  | Microsoft. Storage/storageAccounts | Typ zasobu dla kont magazynu. |
    | **Zasób** | mysecurestorage | Utworzone konto magazynu. |
    | **Docelowy zasób podrzędny** |  — plik | Prywatny punkt końcowy, który będzie używany dla plików z konta magazynu. |

1. Na karcie **Konfiguracja** dla ustawienia **podsieci** wybierz pozycję **domyślne**.

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Zasoby w sieci wirtualnej mogą teraz komunikować się z plikami magazynu.

1. Utwórz inny prywatny punkt końcowy dla obiektów BLOB. Na karcie **zasoby** Użyj ustawień przedstawionych w poniższej tabeli. Dla wszystkich innych ustawień użyj tych samych wartości, które zostały użyte do utworzenia prywatnego punktu końcowego dla plików.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **Typ zasobu**  | Microsoft. Storage/storageAccounts | Typ zasobu dla kont magazynu. |
    | **Zasób** | mysecurestorage | Utworzone konto magazynu. |
    | **Docelowy zasób podrzędny** | blob | Prywatny punkt końcowy, który będzie używany dla obiektów blob z konta magazynu. |

## <a name="lock-down-your-service-bus"></a>Zablokuj usługę Service Bus

Utwórz prywatny punkt końcowy, aby zablokować usługę Service Bus:

1. W nowej magistrali usług w menu po lewej stronie wybierz pozycję **Sieć**.

1. Na karcie **połączenia prywatnego punktu końcowego** wybierz pozycję **prywatny punkt końcowy**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Zrzut ekranu przedstawiający sposób przejścia do prywatnych punktów końcowych usługi Service Bus.":::

1. Na karcie **podstawowe** Użyj ustawień prywatnych punktów końcowych przedstawionych w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Grupa zasobów utworzona za pomocą aplikacji funkcji. |
    | **Nazwa** | SB — punkt końcowy | Nazwa prywatnego punktu końcowego dla plików z konta magazynu. |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | myFunctionRegion | Region, w którym utworzono konto magazynu. |

1. Na karcie **zasób** Użyj ustawień prywatnych punktów końcowych przedstawionych w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **Typ zasobu**  | Microsoft. ServiceBus/przestrzenie nazw | Typ zasobu dla usługi Service Bus. |
    | **Zasób** | myServiceBus | Magistrala usług została utworzona wcześniej w samouczku. |
    | **Podzasób docelowy** | namespace | Prywatny punkt końcowy, który będzie używany dla przestrzeni nazw z usługi Service Bus. |

1. Na karcie **Konfiguracja** dla ustawienia **podsieci** wybierz pozycję **domyślne**.

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. 

Zasoby w sieci wirtualnej mogą teraz komunikować się z usługą Service Bus.

## <a name="create-a-file-share"></a>Tworzenie udziału plików

1. W utworzonym koncie magazynu w menu po lewej stronie wybierz pozycję **udziały plików**.

1. Wybierz pozycję **+ udziały plików**. Na potrzeby tego samouczka Nazwij *pliki* udziałów plików.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia udziału plików na koncie magazynu.":::

1. Wybierz przycisk **Utwórz**.

## <a name="get-the-storage-account-connection-string"></a>Pobierz parametry połączenia konta magazynu

1. W utworzonym koncie magazynu w menu po lewej stronie wybierz pozycję **klucze dostępu**.

1. Wybierz pozycję **Pokaż klucze**. Skopiuj i Zapisz parametry połączenia **Klucz1**. Te parametry połączenia będą potrzebne podczas konfigurowania ustawień aplikacji.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania parametrów połączenia z kontem magazynu.":::

## <a name="create-a-queue"></a>Tworzenie kolejki

Utwórz kolejkę, w której wyzwalacz usługi Service Bus Azure Functions pobierze zdarzenia:

1. W usłudze Service Bus w menu po lewej stronie wybierz **kolejno pozycje kolejki**.

1. Wybierz pozycję **zasady dostępu współdzielonego**. Na potrzeby tego samouczka Nazwij *kolejkę* list.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia kolejki usługi Service Bus.":::

1. Wybierz przycisk **Utwórz**.

## <a name="get-a-service-bus-connection-string"></a>Pobierz parametry połączenia usługi Service Bus

1. W usłudze Service Bus w menu po lewej stronie wybierz pozycję **zasady dostępu współdzielonego**.

1. Wybierz pozycję **RootManageSharedAccessKey**. Skopiuj i Zapisz **podstawowe parametry połączenia**. Te parametry połączenia będą potrzebne podczas konfigurowania ustawień aplikacji.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania parametrów połączenia usługi Service Bus.":::

## <a name="integrate-the-function-app"></a>Integrowanie aplikacji funkcji

Aby korzystać z aplikacji funkcji z sieciami wirtualnymi, należy przyłączyć ją do podsieci. Użyjesz określonej podsieci do integracji z siecią wirtualną Azure Functions. Dla innych prywatnych punktów końcowych tworzonych w tym samouczku zostanie użyta domyślna podsieć.

1. W aplikacji funkcji w menu po lewej stronie wybierz pozycję **Sieć**.

1. W obszarze integracja z siecią **wirtualną** wybierz **pozycję kliknij tutaj, aby ją skonfigurować**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Zrzut ekranu przedstawiający sposób przejścia do integracji z siecią wirtualną.":::

1. Wybierz pozycję **Dodaj sieć wirtualną**.

1. W obszarze **Virtual Network** Wybierz utworzoną wcześniej sieć wirtualną.

1. Wybierz utworzoną wcześniej podsieć **funkcji** . Aplikacja funkcji jest teraz zintegrowana z siecią wirtualną.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Zrzut ekranu przedstawiający sposób łączenia aplikacji funkcji z podsiecią.":::

## <a name="configure-your-function-app-settings"></a>Skonfiguruj ustawienia aplikacji funkcji

1. W aplikacji funkcji w menu po lewej stronie wybierz pozycję **Konfiguracja**.

1. Aby użyć aplikacji funkcji z sieciami wirtualnymi, zaktualizuj ustawienia aplikacji pokazane w poniższej tabeli. Aby dodać lub edytować ustawienie, wybierz pozycję **+ nowe ustawienie aplikacji** lub ikonę **Edytuj** w kolumnie z prawej po stronie tabeli Ustawienia aplikacji. Po zakończeniu wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania ustawień aplikacji funkcji dla prywatnych punktów końcowych.":::

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Parametry połączenia utworzonego konta magazynu. Te parametry połączenia magazynu pochodzą z sekcji [pobieranie parametrów połączenia z kontem magazynu](#get-the-storage-account-connection-string) . To ustawienie umożliwia aplikacji funkcji używanie konta bezpiecznego magazynu na potrzeby normalnych operacji w czasie wykonywania. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Parametry połączenia utworzonego konta magazynu. To ustawienie umożliwia aplikacji funkcji używanie konta bezpiecznego magazynu dla Azure Files, które jest używane podczas wdrażania. |
    | **WEBSITE_CONTENTSHARE** | files | Nazwa udziału plików utworzonego na koncie magazynu. Użyj tego ustawienia z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Utwórz to ustawienie aplikacji dla parametrów połączenia usługi Service Bus. Te parametry połączenia magazynu pochodzą z sekcji [pobieranie parametrów połączenia usługi Service Bus](#get-a-service-bus-connection-string) .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Utwórz to ustawienie aplikacji. Wartość 1 umożliwia skalowanie aplikacji funkcji, gdy konto magazynu jest ograniczone do sieci wirtualnej. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Utwórz to ustawienie aplikacji. Gdy aplikacja jest zintegrowana z siecią wirtualną, użyje tego samego serwera DNS co sieć wirtualna. Aplikacja funkcji wymaga tego ustawienia, aby można było pracować z Azure DNS strefami prywatnymi. Jest to wymagane, gdy używane są prywatne punkty końcowe. To ustawienie i WEBSITE_VNET_ROUTE_ALL będą wysyłać wszystkie wywołania wychodzące z aplikacji do sieci wirtualnej. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Utwórz to ustawienie aplikacji. Gdy aplikacja jest zintegrowana z siecią wirtualną, używa tego samego serwera DNS co sieć wirtualna. Aplikacja funkcji wymaga tego ustawienia, aby można było pracować z Azure DNS strefami prywatnymi. Jest to wymagane, gdy używane są prywatne punkty końcowe. To ustawienie i WEBSITE_DNS_SERVER będą wysyłać wszystkie wywołania wychodzące z aplikacji do sieci wirtualnej. |

1. W widoku **Konfiguracja** wybierz kartę **Ustawienia środowiska uruchomieniowego funkcji** .

1. Ustaw **monitorowanie skalowania środowiska uruchomieniowego** **na włączone**. Następnie wybierz pozycję **Zapisz**. Skalowanie sterowane przez środowisko uruchomieniowe umożliwia łączenie funkcji wyzwalaczy innych niż HTTP z usługami uruchomionymi w sieci wirtualnej.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Zrzut ekranu przedstawiający sposób włączania skalowania opartego na środowisku uruchomieniowym dla Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Wdrażanie wyzwalacza usługi Service Bus i wyzwalacza HTTP

1. W witrynie GitHub przejdź do następującego przykładowego repozytorium. Zawiera aplikację funkcji i dwie funkcje, wyzwalacz HTTP i wyzwalacz kolejki usługi Service Bus.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. W górnej części strony wybierz opcję **rozwidlenie** , aby utworzyć rozwidlenie tego repozytorium we własnym koncie usługi GitHub lub w organizacji.

1. W aplikacji funkcji w menu po lewej stronie wybierz pozycję **centrum wdrażania**. Następnie wybierz pozycję **Ustawienia**.

1. Na karcie **Ustawienia** Użyj ustawień wdrożenia przedstawionych w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Element źródłowy** | GitHub | Należy utworzyć repozytorium GitHub dla przykładowego kodu w kroku 2. | 
    | **Organizacja**  | Organizacja | Organizacja, do której jest zaewidencjonowany repozytorium. Jest to zazwyczaj Twoje konto. |
    | **Repozytorium** | moje repozytorium | Repozytorium utworzone dla przykładowego kodu. |
    | **Gałąź** | main | Główna gałąź utworzonego repozytorium. |
    | **Stos środowiska uruchomieniowego** | .NET | Przykładowy kod jest w języku C#. |

1. Wybierz pozycję **Zapisz**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Zrzut ekranu przedstawiający sposób wdrażania kodu Azure Functions za pomocą portalu.":::

1. Początkowe wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu aplikacji na karcie **dzienniki** zostanie wyświetlony komunikat o stanie **sukces (aktywny)** . W razie potrzeby Odśwież stronę.

Gratulacje! Pomyślnie wdrożono przykładową aplikację funkcji.

## <a name="lock-down-your-function-app"></a>Zablokuj aplikację funkcji

Teraz Utwórz prywatny punkt końcowy, aby zablokować aplikację funkcji. Ten prywatny punkt końcowy łączy swoją aplikację funkcji prywatnie i bezpiecznie z siecią wirtualną przy użyciu prywatnego adresu IP. 

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją prywatnego punktu końcowego](../private-link/private-endpoint-overview.md).

1. W aplikacji funkcji w menu po lewej stronie wybierz pozycję **Sieć**.

1. W obszarze **połączenia prywatnego punktu końcowego** wybierz pozycję **Konfiguruj połączenia prywatnego punktu końcowego**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający sposób przejścia do prywatnego punktu końcowego aplikacji funkcji.":::

1. Wybierz pozycję **Dodaj**.

1. W otwartym okienku Użyj następujących ustawień prywatnych punktów końcowych:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia prywatnego punktu końcowego aplikacji funkcji. Nazwa to functionapp-Endpoint. Subskrypcja to &quot;Private test sub CACHHAI&quot;. Sieć wirtualna to MyVirtualNet — samouczek. Podsieć jest domyślna.":::

1. Wybierz **przycisk OK** , aby dodać prywatny punkt końcowy. 
 
Gratulacje! Pomyślnie zainstalowano aplikację funkcji, usługę Service Bus i konto magazynu przez dodanie prywatnych punktów końcowych!

### <a name="test-your-locked-down-function-app"></a>Testowanie zablokowanej aplikacji funkcji

1. W aplikacji funkcji w menu po lewej stronie wybierz pozycję **funkcje**.

1. Wybierz pozycję **ServiceBusQueueTrigger**.

1. W menu po lewej stronie wybierz pozycję **Monitoruj**. 
 
Zobaczysz, że nie możesz monitorować aplikacji. Przeglądarka nie ma dostępu do sieci wirtualnej, dlatego nie może bezpośrednio uzyskać dostępu do zasobów w sieci wirtualnej. 
 
Oto alternatywny sposób monitorowania funkcji przy użyciu Application Insights:

1. W aplikacji funkcji w menu po lewej stronie wybierz pozycję **Application Insights**. Następnie wybierz pozycję **wyświetl Application Insights dane**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania usługi Application Insights dla aplikacji funkcji.":::

1. W menu po lewej stronie wybierz pozycję **metryki na żywo**.

1. Otwórz nową kartę. W usłudze Service Bus w menu po lewej stronie wybierz **kolejno pozycje kolejki**.

1. Wybierz kolejkę.

1. W menu po lewej stronie wybierz pozycję **eksplorator Service Bus**. W obszarze **wysyłanie** dla **typu zawartości** wybierz pozycję **tekst/zwykły**. Następnie wprowadź komunikat. 

1. Wybierz pozycję **Wyślij** , aby wysłać wiadomość.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Zrzut ekranu przedstawiający sposób wysyłania komunikatów usługi Service Bus przy użyciu portalu.":::

1. Na karcie **metryki na żywo** powinny być widoczne wyzwalacze kolejki usługi Service Bus. Jeśli nie, Wyślij ponownie komunikat z programu **Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania komunikatów przy użyciu metryk na żywo dla aplikacji funkcji.":::

Gratulacje! Pomyślnie przetestowano konfigurację aplikacji funkcji z prywatnymi punktami końcowymi.

## <a name="understand-private-dns-zones"></a>Informacje o prywatnych strefach DNS
Użyto prywatnego punktu końcowego do nawiązania połączenia z zasobami platformy Azure. Łączysz się z prywatnym adresem IP, a nie z publicznym punktem końcowym. Istniejące usługi platformy Azure są skonfigurowane do używania istniejącego systemu DNS do łączenia się z publicznym punktem końcowym. Aby nawiązać połączenie z prywatnym punktem końcowym, należy zastąpić konfigurację DNS.

Dla każdego zasobu platformy Azure, który został skonfigurowany za pomocą prywatnego punktu końcowego, jest tworzona prywatna strefa DNS. Rekord DNS jest tworzony dla każdego prywatnego adresu IP skojarzonego z prywatnym punktem końcowym.

W tym samouczku zostały utworzone następujące strefy DNS:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację funkcji premium, konto magazynu i usługę Service Bus. Wszystkie te zasoby zostały zabezpieczone za prywatne punkty końcowe. 

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o dostępnych funkcjach sieciowych:

> [!div class="nextstepaction"]
> [Opcje sieci w Azure Functions](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Plan Azure Functions Premium](./functions-premium-plan.md)
