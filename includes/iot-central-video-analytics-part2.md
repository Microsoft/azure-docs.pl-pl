---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763430"
---
## <a name="deploy-and-configure-azure-media-services"></a>Wdróż i skonfiguruj Azure Media Services

Rozwiązanie używa konta Azure Media Services do przechowywania klipów wideo wykrywania obiektów wykonanych przez urządzenie bramy IoT Edge Gateway.

Podczas tworzenia konta Media Services:

- Musisz podać nazwę konta, subskrypcję platformy Azure, lokalizację, grupę zasobów i konto magazynu. Utwórz nowe konto magazynu przy użyciu ustawień domyślnych podczas tworzenia konta Media Services.

- Wybierz region **Wschodnie stany USA** dla lokalizacji.

- Utwórz nową grupę zasobów o nazwie *LVA-RG* w regionie **Wschodnie stany usa** dla kont Media Services i magazynu. Po zakończeniu samouczków można łatwo usunąć wszystkie zasoby, usuwając grupę zasobów *LVA-RG* .

Utwórz [konto Media Services w Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Te samouczki korzystają z regionu **Wschodnie stany USA** we wszystkich przykładach. Jeśli wolisz, możesz użyć najbliższego regionu.

Zanotuj nazwę konta **Media Services** w pliku *scratchpad.txt* .

Po zakończeniu wdrażania Otwórz Cloud Shell i uruchom następujące polecenie, aby pobrać **Identyfikator zasobu** dla konta usługi multimediów:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Użyj Cloud Shell, aby uzyskać identyfikator zasobu":::

Zanotuj **Identyfikator zasobu** w pliku *scratchpad.txt* , użyjesz tej wartości później podczas konfigurowania modułu IoT Edge.

Następnie skonfiguruj nazwę główną usługi Azure Active Directory dla zasobu Media Services. Wybierz opcję **dostęp do interfejsu API** , a następnie pozycję **uwierzytelnianie nazwy głównej usługi**. Utwórz nową aplikację Azure Active Directory o takiej samej nazwie jak zasób Media Services i Utwórz wpis tajny z opisem *IoT Edge dostęp*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Konfigurowanie aplikacji Azure A D dla Azure Media Services":::

Po wygenerowaniu wpisu tajnego przewiń w dół do sekcji **Kopiowanie poświadczeń w celu połączenia z aplikacją główną usługi** . Następnie wybierz pozycję **JSON**. Wszystkie informacje o poświadczeniach można skopiować z tego miejsca w jednym miejscu. Zanotuj te informacje w pliku *scratchpad.txt* , użyjesz go później podczas konfigurowania urządzenia IoT Edge.

> [!WARNING]
> Jest to jedyna okazja do wyświetlania i zapisywania klucza tajnego. Jeśli utracisz tę opcję, musisz wygenerować kolejny klucz tajny.

## <a name="create-the-azure-iot-central-application"></a>Tworzenie aplikacji IoT Central platformy Azure

W tej sekcji utworzysz nową aplikację usługi Azure IoT Central na podstawie szablonu. Ta aplikacja jest używana w całej serii samouczków, aby skompilować kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Zaloguj się przy użyciu poświadczeń używanych w celu uzyskania dostępu do subskrypcji platformy Azure.

1. Aby rozpocząć tworzenie nowej aplikacji IoT Central platformy Azure, wybierz pozycję **Nowa aplikacja** na stronie **kompilacja** .

1. Wybierz pozycję **sprzedaż detaliczna**. Na stronie detalicznej są wyświetlane kilka szablonów aplikacji sieci Web.

Aby utworzyć nową aplikację do analizy wideo:

1. Wybierz szablon aplikacja do **analizy filmów wideo — obiekt i ruch** . Ten szablon zawiera szablony urządzeń dla urządzeń używanych w samouczku. Szablon zawiera przykładowe pulpity nawigacyjne, których mogą używać operatory do wykonywania zadań, takich jak monitorowanie i zarządzanie kamerami.

1. Opcjonalnie możesz wybrać przyjazną **nazwę aplikacji**. Ta aplikacja jest oparta na fikcyjnym sklepie detalicznym o nazwie Northwind Traders. W tym samouczku zostanie użyta **Nazwa aplikacji** *Northwind Traders Video Analytics*.

    > [!NOTE]
    > Jeśli używasz przyjaznej **nazwy aplikacji**, nadal musisz użyć unikatowej wartości dla **adresu URL** aplikacji.

1. Jeśli masz subskrypcję platformy Azure, wybierz swój **katalog**, **subskrypcję platformy azure**, a **Stany Zjednoczone** jako **lokalizację**. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe. W tym samouczku są używane trzy urządzenia — dwie aparaty fotograficzne i urządzenie IoT Edge — Jeśli nie będziesz korzystać z bezpłatnej wersji próbnej, opłaty będą naliczane za użycie.

    Aby uzyskać więcej informacji na temat katalogów, subskrypcji i lokalizacji, zobacz [Tworzenie aplikacji — szybki start](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Strona Tworzenie aplikacji usługi Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Pobieranie danych konfiguracyjnych

W dalszej części tego samouczka podczas konfigurowania bramy IoT Edge wymagane są pewne dane konfiguracyjne z aplikacji IoT Central. Urządzenie IoT Edge potrzebuje tych informacji do zarejestrowania się w usłudze i nawiązania połączenia z aplikacją.

W sekcji **Administracja** wybierz **aplikację** i zanotuj **adres URL aplikacji** oraz **Identyfikator aplikacji** w pliku *scratchpad.txt* :

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Zrzut ekranu przedstawia okienko Administracja na stronie Analiza wideo z wyróżnioną aplikacją U R L i aplikacją I D.":::

Wybierz pozycję **tokeny interfejsu API** i Wygeneruj nowy token o nazwie **LVAEdgeToken** dla roli **operatora** :

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Generuj token":::

Zanotuj token w pliku *scratchpad.txt* w przyszłości. Po zamknięciu okna dialogowego nie można ponownie wyświetlić tokenu.

W sekcji **Administracja** wybierz pozycję **połączenie z urządzeniem**, a następnie wybierz pozycję **SAS-IoT-Devices**.

Zanotuj **klucz podstawowy** dla urządzeń w pliku *scratchpad.txt* . Ten *token sygnatury dostępu współdzielonego* jest używany później podczas konfigurowania urządzenia IoT Edge.
