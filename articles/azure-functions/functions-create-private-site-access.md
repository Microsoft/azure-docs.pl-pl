---
title: Włącz dostęp do lokacji prywatnej do Azure Functions
description: Dowiedz się, jak skonfigurować dostęp do prywatnej witryny sieci wirtualnej platformy Azure dla Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937046"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Samouczek: ustanawianie Azure Functions dostępu do lokacji prywatnej

W tym samouczku przedstawiono sposób włączania [dostępu do lokacji prywatnej](./functions-networking-options.md#private-endpoint-connections) przy użyciu Azure Functions. Korzystając z dostępu do lokacji prywatnej, można wymagać, aby kod funkcji był wyzwalany tylko z określonej sieci wirtualnej.

Dostęp do lokacji prywatnej jest użyteczny w scenariuszach, gdy dostęp do aplikacji funkcji musi być ograniczony do określonej sieci wirtualnej. Na przykład aplikacja funkcji może dotyczyć tylko pracowników określonej organizacji lub usług należących do określonej sieci wirtualnej (takich jak inna funkcja platformy Azure, maszyna wirtualna platformy Azure lub klaster AKS).

Jeśli aplikacja funkcji musi uzyskać dostęp do zasobów platformy Azure w ramach sieci wirtualnej lub połączyć się za pośrednictwem [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md), wymagana jest [integracja z siecią wirtualną](./functions-create-vnet.md) .

W tym samouczku dowiesz się, jak skonfigurować dostęp do lokacji prywatnej dla aplikacji funkcji:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej
> * Tworzenie usługi Azure bastionu
> * Tworzenie aplikacji funkcji platformy Azure
> * Konfigurowanie punktu końcowego usługi sieci wirtualnej
> * Tworzenie i wdrażanie funkcji platformy Azure
> * Wywołaj funkcję spoza i wewnątrz sieci wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="topology"></a>Topologia

Na poniższym diagramie przedstawiono architekturę rozwiązania, które ma zostać utworzone:

![Diagram architektury wysokiego poziomu dla rozwiązania dostępu do lokacji prywatnej](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku ważne jest zrozumienie adresów IP i podsieci. Możesz rozpocząć od [tego artykułu, który obejmuje podstawowe informacje dotyczące adresowania i podsieci](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Wiele dodatkowych artykułów i filmów wideo jest dostępnych w trybie online.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Pierwszym krokiem w tym samouczku jest utworzenie nowej maszyny wirtualnej w sieci wirtualnej.  Maszyna wirtualna zostanie użyta w celu uzyskania dostępu do funkcji po ograniczeniu dostępu do niej tylko z poziomu sieci wirtualnej.

1. Wybierz przycisk **Utwórz zasób** .

1. W polu wyszukiwania wpisz **Windows Server**, a następnie w wynikach wyszukiwania wybierz pozycję **Windows Server** .

1. Wybierz pozycję **Windows server 2019 Datacenter** z listy opcji systemu Windows Server, a następnie naciśnij przycisk **Utwórz** .

1. Na karcie _podstawy_ Użyj ustawień maszyny wirtualnej określonych w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Karta podstawy dla nowej maszyny wirtualnej z systemem Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | _Subskrypcja_ | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. |
    | [_Grupa zasobów_](../azure-resource-manager/management/overview.md) | myResourceGroup | Wybierz grupę zasobów, aby zawierała wszystkie zasoby dla tego samouczka.  Korzystanie z tej samej grupy zasobów ułatwia czyszczenie zasobów po zakończeniu pracy z tym samouczkiem. |
    | _Nazwa maszyny wirtualnej_ | myVM | Nazwa maszyny wirtualnej musi być unikatowa w grupie zasobów |
    | [_Region (Region)_](https://azure.microsoft.com/regions/) | Prześlij Północno-środkowe stany USA | Wybierz region w sąsiedztwie lub w prawie funkcji do uzyskania dostępu. |
    | _Publiczne porty ruchu przychodzącego_ | Brak | Wybierz opcję **Brak** , aby upewnić się, że nie ma połączenia przychodzącego z maszyną wirtualną z Internetu. Dostęp zdalny do maszyny wirtualnej zostanie skonfigurowany za pośrednictwem usługi Azure bastionu. |

1. Wybierz kartę _Sieć_ i wybierz pozycję **Utwórz nową** , aby skonfigurować nową sieć wirtualną.

    >[!div class="mx-imgBorder"]
    >![Zrzut ekranu pokazujący kartę "Sieć" z akcją "Utwórz nowy" wyróżnioną w sekcji "Sieć wirtualna".](./media/functions-create-private-site-access/create-vm-networking.png)

1. W obszarze _Utwórz sieć wirtualną_ Użyj ustawień w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Utwórz nową sieć wirtualną dla nowej maszyny wirtualnej](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | _Nazwa_ | Moja resourceName — Sieć wirtualna | Możesz użyć domyślnej nazwy wygenerowanej dla sieci wirtualnej. |
    | _Zakres adresów_ | 10.10.0.0/16 | Użyj jednego zakresu adresów dla sieci wirtualnej. |
    | _Nazwa podsieci_ | Samouczek | Nazwa podsieci. |
    | _Zakres adresów_ (podsieci) | 10.10.1.0/24 | Rozmiar podsieci definiuje liczbę interfejsów, które można dodać do podsieci. Ta podsieć jest używana przez maszynę wirtualną. Podsieć/24 zawiera adresy hosta 254. |

1. Wybierz **przycisk OK** , aby utworzyć sieć wirtualną.
1. Na karcie _Sieć_ upewnij się, że dla _publicznego adresu IP_ nie wybrano opcji **Brak** .
1. Wybierz kartę _Zarządzanie_ , a następnie w obszarze _konto magazynu diagnostycznego_ wybierz pozycję **Utwórz nowy** , aby utworzyć nowe konto magazynu.
1. Pozostaw wartości domyślne dla sekcji _tożsamość_, _automatyczne zamykanie_ i _kopia zapasowa_ .
1. Wybierz pozycję _Przejrzyj i utwórz_. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Proces tworzenia maszyny wirtualnej trwa kilka minut.

## <a name="configure-azure-bastion"></a>Konfigurowanie usługi Azure bastionu

[Azure bastionu](https://azure.microsoft.com/services/azure-bastion/) to w pełni zarządzana usługa platformy Azure, która zapewnia bezpieczny dostęp RDP i SSH do maszyn wirtualnych bezpośrednio z Azure Portal. Użycie usługi Azure bastionu eliminuje konieczność konfigurowania ustawień sieciowych związanych z dostępem do protokołu RDP.

1. W portalu wybierz pozycję **Dodaj** w górnej części widoku grupy zasobów.
1. W polu wyszukiwania wpisz **bastionu**.
1. W wynikach wyszukiwania wybierz pozycję **bastionu** .
1. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia nowego zasobu usługi Azure bastionu. Zobaczysz komunikat o błędzie w sekcji _Sieć wirtualna_ , ponieważ nie ma jeszcze podsieci AzureBastionSubnet. Podsieć jest tworzona w poniższych krokach. Użyj ustawień w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Rozpoczęcie tworzenia usługi Azure bastionu](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | _Nazwa_ | myBastion | Nazwa nowego zasobu bastionu |
    | _Region_ | Północno-środkowe stany USA | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |
    | _Sieć wirtualna_ | Moja resourceName — Sieć wirtualna | Sieć wirtualna, w której zostanie utworzony zasób bastionu |
    | _Podsieć_ | AzureBastionSubnet | Podsieć w sieci wirtualnej, w której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy użyciu wartości Name **AzureBastionSubnet**. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Należy użyć podsieci o wartości co najmniej **/27** lub większej (/27,/26 itd.). |

    > [!NOTE]
    > Szczegółowe instrukcje krok po kroku dotyczące tworzenia zasobów usługi Azure bastionu można znaleźć w samouczku [Tworzenie hosta usługi Azure bastionu](../bastion/tutorial-create-host-portal.md) .

1. Utwórz podsieć, w której platforma Azure może udostępnić hosta usługi Azure bastionu. Wybranie opcji **Zarządzaj konfiguracją podsieci** powoduje otwarcie nowego okienka, w którym można zdefiniować nową podsieć.  Wybierz pozycję **+ podsieć** , aby utworzyć nową podsieć.
1. Podsieć musi mieć nazwę **AzureBastionSubnet** , a prefiks podsieci musi mieć wartość co najmniej **/27**.  Wybierz **przycisk OK** , aby utworzyć podsieć.

    >[!div class="mx-imgBorder"]
    >![Utwórz podsieć dla hosta usługi Azure bastionu](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. Na stronie _Tworzenie bastionu_ wybierz nowo utworzony **AzureBastionSubnet** z listy dostępnych podsieci.

    >[!div class="mx-imgBorder"]
    >![Tworzenie hosta usługi Azure bastionu z określoną podsiecią](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Wybierz pozycję **przegląd & Utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Utworzenie zasobu usługi Azure bastionu może potrwać kilka minut.

## <a name="create-an-azure-functions-app"></a>Tworzenie aplikacji funkcji platformy Azure

Następnym krokiem jest utworzenie aplikacji funkcji na platformie Azure przy użyciu [planu zużycia](consumption-plan.md). Kod funkcji jest wdrażany w tym zasobie w dalszej części tego samouczka.

1. W portalu wybierz pozycję **Dodaj** w górnej części widoku grupy zasobów.
1. Wybierz **> obliczeniowe aplikacja funkcji**
1. W sekcji _podstawy_ Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | _Grupa zasobów_ | myResourceGroup | Wybierz grupę zasobów, aby zawierała wszystkie zasoby dla tego samouczka.  Korzystanie z tej samej grupy zasobów dla aplikacji funkcji i maszyny wirtualnej ułatwia czyszczenie zasobów po zakończeniu pracy z tym samouczkiem. |
    | _Nazwa aplikacji funkcji_ | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to a-z (bez uwzględniania wielkości liter), 0-9 i-. |
    | _Publikowanie_ | Kod | Opcja publikowania plików kodu lub kontenera Docker. |
    | _Stos środowiska uruchomieniowego_ | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. |
    | _Region_ | Północno-środkowe stany USA | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

    Wybierz przycisk **Dalej: Hosting >** .
1. W sekcji _hosting_ wybierz odpowiednie _konto magazynu_, _system operacyjny_ i _Planowanie_ zgodnie z opisem w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | _Konto magazynu_ | Nazwa unikatowa w skali globalnej | Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements). |
    | _System operacyjny_ | Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. |
    | _Planowanie_ | Zużycie | [Plan hostingu](./functions-scale.md) określa, jak aplikacja funkcji jest skalowana i jakie zasoby są dostępne dla każdego wystąpienia. |
1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.
1. Wybierz pozycję **Utwórz**, aby zaaprowizować i wdrożyć aplikację funkcji.

## <a name="configure-access-restrictions"></a>Konfigurowanie ograniczeń dostępu

Następnym krokiem jest skonfigurowanie [ograniczeń dostępu](../app-service/app-service-ip-restrictions.md) w celu zapewnienia, że tylko zasoby w sieci wirtualnej mogą wywoływać funkcję.

Dostęp do [lokacji prywatnej](functions-networking-options.md#private-endpoint-connections) jest włączony przez utworzenie [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Virtual Network w ramach aplikacji funkcji i określonej sieci wirtualnej. Ograniczenia dostępu są implementowane za pośrednictwem punktów końcowych usługi. Punkty końcowe usługi zapewniają dostęp do wyznaczonych zasobów tylko ruch pochodzący z określonej sieci wirtualnej. W takim przypadku wydzielonym zasobem jest funkcja platformy Azure.

1. W aplikacji funkcji wybierz link **Sieć** w nagłówku sekcji _Ustawienia_ .
1. Strona _sieci_ jest punktem wyjścia do konfigurowania drzwi platformy Azure, Azure CDN, a także ograniczeń dostępu.
1. Wybierz pozycję **Konfiguruj ograniczenia dostępu** , aby skonfigurować dostęp do lokacji prywatnej.
1. Na stronie _ograniczenia dostępu_ zobaczysz tylko domyślne ograniczenie w miejscu. Domyślnie nie są stosowane żadne ograniczenia dostępu do aplikacji funkcji.  Wybierz pozycję **Dodaj regułę** , aby utworzyć konfigurację ograniczeń dostępu do lokacji prywatnej.
1. W okienku _Dodaj ograniczenie dostępu_ Podaj _nazwę_, _priorytet_ i _Opis_ nowej reguły.
1. Wybierz pozycję **Virtual Network** w polu listy rozwijanej _Typ_ , a następnie wybierz wcześniej utworzoną sieć wirtualną, a następnie wybierz podsieć **samouczka** . 
    > [!NOTE]
    > Włączenie punktu końcowego usługi może potrwać kilka minut.
1. Na stronie _ograniczenia dostępu_ wyświetlane są teraz nowe ograniczenia. Zmiana _stanu punktu końcowego_ z wyłączonego na włączony może potrwać kilka sekund.

    >[!IMPORTANT]
    > Każda aplikacja funkcji ma [witrynę zaawansowanego narzędzia (kudu)](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) , która jest używana do zarządzania wdrożeniami aplikacji funkcji. Dostęp do tej witryny uzyskuje się za pomocą adresu URL, takiego jak: `<FUNCTION_APP_NAME>.scm.azurewebsites.net` . Włączenie ograniczeń dostępu w witrynie kudu uniemożliwia wdrożenie kodu projektu z lokalnej stacji roboczej dla deweloperów, a następnie wymaga agenta w ramach sieci wirtualnej do wdrożenia.

## <a name="access-the-functions-app"></a>Dostęp do aplikacji Functions

1. Wróć do wcześniej utworzonej aplikacji funkcji.  W sekcji _Przegląd_ Skopiuj adres URL.

    >[!div class="mx-imgBorder"]
    >![Pobierz adres URL aplikacji funkcji](./media/functions-create-private-site-access/access-function-overview.png)

    Jeśli użytkownik próbuje uzyskać dostęp do aplikacji funkcji z komputera poza siecią wirtualną, zostanie wyświetlona strona HTTP 403 wskazująca, że dostęp jest zabroniony.
1. Wróć do grupy zasobów i wybierz wcześniej utworzoną maszynę wirtualną. Aby można było uzyskać dostęp do witryny z poziomu maszyny wirtualnej, należy nawiązać połączenie z maszyną wirtualną za pośrednictwem usługi Azure bastionu.
1. Wybierz pozycję **Połącz** , a następnie wybierz pozycję **bastionu**.
1. Podaj wymaganą nazwę użytkownika i hasło, aby zalogować się do maszyny wirtualnej.
1. Wybierz pozycję **Połącz**. Nowe okno przeglądarki zostanie wyświetlona, aby umożliwić pracę z maszyną wirtualną.
Istnieje możliwość uzyskania dostępu do witryny z przeglądarki sieci Web na maszynie wirtualnej, ponieważ maszyna wirtualna uzyskuje dostęp do witryny za pośrednictwem sieci wirtualnej.  Gdy lokacja jest dostępna tylko z poziomu wyznaczonych sieci wirtualnych, pozostanie publiczny wpis DNS.

## <a name="create-a-function"></a>Tworzenie funkcji

Następnym krokiem w tym samouczku jest utworzenie funkcji platformy Azure wyzwalanej przez protokół HTTP. Wywoływanie funkcji za pośrednictwem protokołu HTTP GET lub POST powinno skutkować odpowiedzią "Hello, {Name}".  

1. Wykonaj jeden z następujących przewodników Szybki Start, aby utworzyć i wdrożyć aplikację Azure Functions.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Wiersz polecenia](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Podczas publikowania Azure Functions projektu wybierz zasób aplikacji funkcji, który został utworzony wcześniej w tym samouczku.
1. Sprawdź, czy funkcja została wdrożona.

    >[!div class="mx-imgBorder"]
    >![Wdrożona funkcja na liście funkcji](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Wywołaj funkcję bezpośrednio

1. Aby przetestować dostęp do funkcji, należy skopiować adres URL funkcji. Wybierz wdrożoną funkcję, a następnie wybierz pozycję **Pobierz adres URL funkcji**. Następnie kliknij przycisk **Kopiuj** , aby skopiować adres URL do Schowka.

    >[!div class="mx-imgBorder"]
    >![Skopiuj adres URL funkcji](./media/functions-create-private-site-access/get-function-url.png)

1. Wklej adres URL do przeglądarki sieci Web. Gdy użytkownik próbuje uzyskać dostęp do aplikacji funkcji z komputera poza siecią wirtualną, otrzymasz odpowiedź HTTP 403 informującą o tym, że dostęp do aplikacji jest zabroniony.

## <a name="invoke-the-function-from-the-virtual-network"></a>Wywołaj funkcję z sieci wirtualnej

Uzyskiwanie dostępu do funkcji za pośrednictwem przeglądarki sieci Web (przy użyciu usługi Azure bastionu) na skonfigurowanej maszynie wirtualnej w tej sieci spowoduje sukces!

>[!div class="mx-imgBorder"]
>![Dostęp do funkcji platformy Azure za pośrednictwem usługi Azure bastionu](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki


> [!div class="nextstepaction"]
> [Dowiedz się więcej o opcjach sieci w funkcjach](./functions-networking-options.md)