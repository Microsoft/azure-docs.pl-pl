---
title: Włącz dostęp do lokacji prywatnej do Azure Functions
description: Dowiedz się, jak skonfigurować dostęp do prywatnej witryny sieci wirtualnej platformy Azure dla Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851314"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Samouczek: ustanawianie Azure Functions dostępu do lokacji prywatnej

W tym samouczku przedstawiono sposób włączania [dostępu do lokacji prywatnej](./functions-networking-options.md#private-site-access) przy użyciu Azure Functions. Korzystając z dostępu do lokacji prywatnej, można wymagać, aby kod funkcji był wyzwalany tylko z określonej sieci wirtualnej.

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

2. W polu wyszukiwania wpisz `Windows Server`, a następnie w wynikach wyszukiwania wybierz pozycję **Windows Server** .

3. Wybierz pozycję **Windows server 2019 Datacenter** z listy opcji systemu Windows Server, a następnie naciśnij przycisk **Utwórz** .

4. Na karcie **podstawy** Użyj ustawień maszyny wirtualnej określonych w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Karta podstawy dla nowej maszyny wirtualnej z systemem Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. |
    | [**Grupa zasobów**](../azure-resource-manager/management/overview.md) | myResourceGroup | Wybierz grupę zasobów, aby zawierała wszystkie zasoby dla tego samouczka.  Korzystanie z tej samej grupy zasobów ułatwia czyszczenie zasobów po zakończeniu pracy z tym samouczkiem. |
    | **Nazwa maszyny wirtualnej** | myVM | Nazwa maszyny wirtualnej musi być unikatowa w grupie zasobów |
    | [**Region**](https://azure.microsoft.com/regions/) | Prześlij Północno-środkowe stany USA | Wybierz region w sąsiedztwie lub w prawie funkcji do uzyskania dostępu. |
    | **Publiczne porty wejściowe** | Brak | Wybierz opcję **Brak** , aby upewnić się, że nie ma połączenia przychodzącego z maszyną wirtualną z Internetu. Dostęp zdalny do maszyny wirtualnej zostanie skonfigurowany za pośrednictwem usługi Azure bastionu. |

5. Wybierz kartę **Sieć** i wybierz pozycję **Utwórz nową** , aby skonfigurować nową sieć wirtualną.

    >[!div class="mx-imgBorder"]
    >![Utwórz nową sieć wirtualną dla nowej maszyny wirtualnej](./media/functions-create-private-site-access/create-vm-networking.png)

6. W obszarze **Utwórz sieć wirtualną**Użyj ustawień w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Utwórz nową sieć wirtualną dla nowej maszyny wirtualnej](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | Moja resourceName — Sieć wirtualna | Możesz użyć domyślnej nazwy wygenerowanej dla sieci wirtualnej. |
    | **Zakres adresów** | 10.10.0.0/16 | Użyj jednego zakresu adresów dla sieci wirtualnej. |
    | **Nazwa podsieci** | Samouczek | Nazwa podsieci. |
    | **Zakres adresów** (podsieci) | 10.10.1.0/24 | Rozmiar podsieci definiuje liczbę interfejsów, które można dodać do podsieci. Ta podsieć jest używana przez maszynę wirtualną. `/24` Podsieć zawiera adresy hostów 254. |

7. Wybierz **przycisk OK** , aby utworzyć sieć wirtualną.
8. Na karcie **Sieć** upewnij się, że dla **publicznego adresu IP**nie wybrano opcji **Brak** .
9. Wybierz kartę **Zarządzanie** , a następnie w obszarze **konto magazynu diagnostycznego**wybierz pozycję **Utwórz nowy** , aby utworzyć nowe konto magazynu.
10. Pozostaw wartości domyślne dla sekcji **tożsamość**, **automatyczne zamykanie**i **kopia zapasowa** .
11. Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Proces tworzenia maszyny wirtualnej trwa kilka minut.

## <a name="configure-azure-bastion"></a>Konfigurowanie usługi Azure bastionu

[Azure bastionu](https://azure.microsoft.com/services/azure-bastion/) to w pełni zarządzana usługa platformy Azure, która zapewnia bezpieczny dostęp RDP i SSH do maszyn wirtualnych bezpośrednio z Azure Portal. Użycie usługi Azure bastionu eliminuje konieczność konfigurowania ustawień sieciowych związanych z dostępem do protokołu RDP.

1. W portalu wybierz pozycję **Dodaj** w górnej części widoku grupy zasobów.
2. W polu wyszukiwania wpisz ciąg "bastionu".  Wybierz pozycję "bastionu".
3. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia nowego zasobu usługi Azure bastionu. W sekcji **Sieć wirtualna** zostanie wyświetlony komunikat o błędzie, ponieważ nie istnieje jeszcze `AzureBastionSubnet` podsieć. Podsieć jest tworzona w poniższych krokach. Użyj ustawień w tabeli poniżej obrazu:

    >[!div class="mx-imgBorder"]
    >![Rozpoczęcie tworzenia usługi Azure bastionu](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | myBastion | Nazwa nowego zasobu bastionu |
    | **Region** | Północno-środkowe stany USA | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |
    | **Sieć wirtualna** | Moja resourceName — Sieć wirtualna | Sieć wirtualna, w której zostanie utworzony zasób bastionu |
    | **Podsieci** | AzureBastionSubnet | Podsieć w sieci wirtualnej, w której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy użyciu wartości `AzureBastionSubnet`nazwa. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Należy użyć podsieci z co najmniej `/27` lub większą (`/27`, `/26`itd.). |

    > [!NOTE]
    > Szczegółowe instrukcje krok po kroku dotyczące tworzenia zasobów usługi Azure bastionu można znaleźć w samouczku [Tworzenie hosta usługi Azure bastionu](../bastion/bastion-create-host-portal.md) .

4. Utwórz podsieć, w której platforma Azure może udostępnić hosta usługi Azure bastionu. Wybranie opcji **Zarządzaj konfiguracją podsieci** powoduje otwarcie nowego okienka, w którym można zdefiniować nową podsieć.  Wybierz pozycję **+ podsieć** , aby utworzyć nową podsieć.
5. Podsieć musi mieć nazwę `AzureBastionSubnet` , a prefiks podsieci musi być co najmniej. `/27`  Wybierz **przycisk OK** , aby utworzyć podsieć.

    >[!div class="mx-imgBorder"]
    >![Utwórz podsieć dla hosta usługi Azure bastionu](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Na stronie **Tworzenie bastionu** wybierz nowo utworzone `AzureBastionSubnet` z listy dostępnych podsieci.

    >[!div class="mx-imgBorder"]
    >![Tworzenie hosta usługi Azure bastionu z określoną podsiecią](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Wybierz pozycję **przegląd & Utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Utworzenie zasobu usługi Azure bastionu może potrwać kilka minut.

## <a name="create-an-azure-functions-app"></a>Tworzenie aplikacji funkcji platformy Azure

Następnym krokiem jest utworzenie aplikacji funkcji na platformie Azure przy użyciu [planu zużycia](functions-scale.md#consumption-plan). Kod funkcji jest wdrażany w tym zasobie w dalszej części tego samouczka.

1. W portalu wybierz pozycję **Dodaj** w górnej części widoku grupy zasobów.
2. Wybierz **> obliczeniowe aplikacja funkcji**
3. W sekcji **podstawy** Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Grupa zasobów** | myResourceGroup | Wybierz grupę zasobów, aby zawierała wszystkie zasoby dla tego samouczka.  Korzystanie z tej samej grupy zasobów dla aplikacji funkcji i maszyny wirtualnej ułatwia czyszczenie zasobów po zakończeniu pracy z tym samouczkiem. |
    | **Nazwa aplikacja funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to a-z (bez uwzględniania wielkości liter), 0-9 i-. |
    | **Publikowanie** | Code | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. |
    | **Region** | Północno-środkowe stany USA | Wybierz [region](https://azure.microsoft.com/regions/) w swojej okolicy lub w pobliżu innych usług, do których Twoje funkcje uzyskują dostęp. |

    Wybierz przycisk **Dalej: Hosting >** .
4. W sekcji **hosting** wybierz odpowiednie **konto magazynu**, **system operacyjny**i **Zaplanuj** opisane w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Konto magazynu** | Nazwa unikatowa w skali globalnej | Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](./functions-scale.md#storage-account-requirements). |
    | **System operacyjny** | Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. |
    | **Planowanie** | Zużycie | [Plan hostingu](./functions-scale.md) określa, jak aplikacja funkcji jest skalowana i jakie zasoby są dostępne dla każdego wystąpienia. |
5. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.
6. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

## <a name="configure-access-restrictions"></a>Konfigurowanie ograniczeń dostępu

Następnym krokiem jest skonfigurowanie [ograniczeń dostępu](../app-service/app-service-ip-restrictions.md) w celu zapewnienia, że tylko zasoby w sieci wirtualnej mogą wywoływać funkcję.

Dostęp do [lokacji prywatnej](functions-networking-options.md#private-site-access) jest włączony przez utworzenie [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Virtual Network w ramach aplikacji funkcji i określonej sieci wirtualnej. Ograniczenia dostępu są implementowane za pośrednictwem punktów końcowych usługi. Punkty końcowe usługi zapewniają dostęp do wyznaczonych zasobów tylko ruch pochodzący z określonej sieci wirtualnej. W takim przypadku wydzielonym zasobem jest funkcja platformy Azure.

1. W aplikacji funkcji należy przejoć do karty **funkcje platformy** . Wybierz łącze **sieci** w nagłówku sekcji *Sieć* , aby otworzyć sekcję stan funkcji sieci.
2. Strona **stanu funkcji sieci** jest punktem wyjścia do konfigurowania drzwi platformy Azure, Azure CDN, a także ograniczeń dostępu. Wybierz pozycję **Konfiguruj ograniczenia dostępu** , aby skonfigurować dostęp do lokacji prywatnej.
3. Na stronie **ograniczenia dostępu** zobaczysz tylko domyślne ograniczenie w miejscu. Domyślnie nie są stosowane żadne ograniczenia dostępu do aplikacji funkcji.  Wybierz pozycję **Dodaj regułę** , aby utworzyć konfigurację ograniczeń dostępu do lokacji prywatnej.
4. W okienku **Dodaj ograniczenie dostępu** wybierz pozycję **Virtual Network** w polu listy rozwijanej **Typ** , a następnie wybierz wcześniej utworzoną sieć wirtualną i podsieć.
5. Na stronie **ograniczenia dostępu** wyświetlane są teraz nowe ograniczenia. Zmiana **stanu punktu końcowego** na "z `Disabled` `Provisioning` " może potrwać kilka sekund `Enabled`.

    >[!IMPORTANT]
    > Każda aplikacja funkcji ma [witrynę zaawansowanego narzędzia (kudu)](../app-service/app-service-ip-restrictions.md#scm-site) , która jest używana do zarządzania wdrożeniami aplikacji funkcji. Dostęp do tej witryny uzyskuje się za pomocą `<FUNCTION_APP_NAME>.scm.azurewebsites.net`adresu URL, takiego jak:. Ze względu na to, że w tej lokacji wdrożenia nie włączono ograniczeń dostępu, można nadal wdrażać kod projektu z lokalnej stacji roboczej lub usługi kompilacji bez konieczności aprowizacji agenta w sieci wirtualnej.

## <a name="access-the-functions-app"></a>Dostęp do aplikacji Functions

1. Wróć do wcześniej utworzonej aplikacji funkcji.  W sekcji **Przegląd** Skopiuj adres URL.

    >[!div class="mx-imgBorder"]
    >![Pobierz adres URL aplikacji funkcji](./media/functions-create-private-site-access/access-function-overview.png)

2. Jeśli użytkownik próbuje uzyskać dostęp do aplikacji funkcji z komputera poza siecią wirtualną, zostanie wyświetlona strona HTTP 403 informująca o zatrzymaniu aplikacji.  Aplikacja nie jest zatrzymana. Odpowiedź jest w rzeczywistości stanem niedostępności protokołu HTTP 403.
3. Teraz będziesz uzyskiwać dostęp do funkcji z wcześniej utworzonej maszyny wirtualnej, która jest połączona z siecią wirtualną. Aby można było uzyskać dostęp do witryny z poziomu maszyny wirtualnej, należy nawiązać połączenie z maszyną wirtualną za pośrednictwem usługi Azure bastionu.  Najpierw wybierz pozycję **Połącz** , a następnie wybierz pozycję **bastionu**.
4. Podaj wymaganą nazwę użytkownika i hasło, aby zalogować się do maszyny wirtualnej.  Wybierz przycisk **Połącz**. Nowe okno przeglądarki zostanie wyświetlona, aby umożliwić pracę z maszyną wirtualną.
5. Ponieważ ta maszyna wirtualna uzyskuje dostęp do funkcji za pośrednictwem sieci wirtualnej, można uzyskać dostęp do witryny z przeglądarki sieci Web na maszynie wirtualnej.  Należy pamiętać, że podczas gdy aplikacja funkcji jest dostępna tylko z poziomu wyznaczonych sieci wirtualnych, pozostanie publiczny wpis DNS. Jak pokazano powyżej, próba uzyskania dostępu do witryny spowoduje odpowiedź HTTP 403.

## <a name="create-a-function"></a>Tworzenie funkcji

Następnym krokiem w tym samouczku jest utworzenie funkcji platformy Azure wyzwalanej przez protokół HTTP. Wywoływanie funkcji za pośrednictwem protokołu HTTP GET lub POST powinno skutkować odpowiedzią "Hello, {Name}".  

1. Wykonaj jeden z następujących przewodników Szybki Start, aby utworzyć i wdrożyć aplikację Azure Functions.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Wiersz polecenia](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Podczas publikowania Azure Functions projektu wybierz zasób aplikacji funkcji, który został utworzony wcześniej w tym samouczku.
3. Sprawdź, czy funkcja została wdrożona.

    >[!div class="mx-imgBorder"]
    >![Wdrożona funkcja na liście funkcji](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Wywołaj funkcję bezpośrednio

1. Aby przetestować dostęp do funkcji, należy skopiować adres URL funkcji. Wybierz wdrożoną funkcję, a następnie wybierz pozycję **</> Pobierz adres URL funkcji**. Następnie kliknij przycisk **Kopiuj** , aby skopiować adres URL do Schowka.

    >[!div class="mx-imgBorder"]
    >![Skopiuj adres URL funkcji](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Gdy funkcja zostanie uruchomiona, zobaczysz błąd czasu wykonywania w portalu informujący o tym, że nie można uruchomić środowiska uruchomieniowego funkcji. Pomimo tekstu komunikatu aplikacja funkcji jest w rzeczywistości uruchomiona. Ten błąd jest wynikiem nowych ograniczeń dostępu, które uniemożliwiają przeprowadzenie zapytania przez portal w celu sprawdzenia środowiska uruchomieniowego.

2. Wklej adres URL do przeglądarki sieci Web. Gdy użytkownik próbuje uzyskać dostęp do aplikacji funkcji z komputera poza siecią wirtualną, otrzymasz odpowiedź HTTP 403 informującą o zatrzymaniu aplikacji.

## <a name="invoke-the-function-from-the-virtual-network"></a>Wywołaj funkcję z sieci wirtualnej

Uzyskiwanie dostępu do funkcji za pośrednictwem przeglądarki sieci Web (przy użyciu usługi Azure bastionu) na skonfigurowanej maszynie wirtualnej w tej sieci spowoduje sukces!

>[!div class="mx-imgBorder"]
>![Dostęp do funkcji platformy Azure za pośrednictwem usługi Azure bastionu](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki


> [!div class="nextstepaction"]
> [Dowiedz się więcej o opcjach sieci w funkcjach](./functions-networking-options.md)
