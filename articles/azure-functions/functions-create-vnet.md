---
title: Integrowanie Azure Functions z siecią wirtualną platformy Azure
description: Samouczek krok po kroku pokazujący, jak połączyć funkcję z siecią wirtualną platformy Azure
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: efc936111d162d73b1cc5465ae6b677c9006ab32
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937034"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Samouczek: integrowanie usługi Functions z siecią wirtualną platformy Azure

W tym samouczku pokazano, jak używać Azure Functions do nawiązywania połączeń z zasobami w sieci wirtualnej platformy Azure. utworzysz funkcję, która ma dostęp zarówno do Internetu, jak i na MASZYNę wirtualną z uruchomioną usługą WordPress w sieci wirtualnej.

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji w planie Premium
> * Wdrażanie witryny WordPress na maszynie wirtualnej w sieci wirtualnej
> * Połącz aplikację funkcji z siecią wirtualną
> * Tworzenie serwera proxy funkcji w celu uzyskania dostępu do zasobów WordPress
> * Zażądaj pliku WordPress z wewnątrz sieci wirtualnej

## <a name="topology"></a>Topologia

Na poniższym diagramie przedstawiono architekturę tworzonego rozwiązania:

 ![Interfejs użytkownika integracji sieci wirtualnej](./media/functions-create-vnet/topology.png)

Funkcje działające w planie Premium mają takie same możliwości hostingu jak aplikacje sieci Web w Azure App Service, które obejmują funkcję integracji z siecią wirtualną. Aby dowiedzieć się więcej o integracji sieci wirtualnej, w tym o rozwiązywaniu problemów i konfiguracji zaawansowanej, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku ważne jest zrozumienie adresów IP i podsieci. Możesz rozpocząć od [tego artykułu, który obejmuje podstawowe informacje dotyczące adresowania i podsieci](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Wiele dodatkowych artykułów i filmów wideo jest dostępnych w trybie online.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-a-function-app-in-a-premium-plan"></a>Tworzenie aplikacji funkcji w planie Premium

Najpierw Utwórz aplikację funkcji w [planie Premium]. Ten plan zapewnia skalę bezserwerową podczas obsługi integracji sieci wirtualnej.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Możesz przypiąć aplikację funkcji do pulpitu nawigacyjnego, wybierając ikonę pinezki w prawym górnym rogu. Przypinanie ułatwia powrót do tej aplikacji funkcji po utworzeniu maszyny wirtualnej.

## <a name="create-a-vm-inside-a-virtual-network"></a>Tworzenie maszyny wirtualnej w sieci wirtualnej

Następnie Utwórz wstępnie skonfigurowaną maszynę wirtualną z systemem WordPress w sieci wirtualnej ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Maszyna wirtualna WordPress jest używana ze względu na jej niski koszt i wygodę. Ten sam scenariusz działa z dowolnym zasobem w sieci wirtualnej, takim jak interfejsy API REST, środowiska App Service i inne usługi platformy Azure. 

1. W portalu wybierz pozycję **+ Utwórz zasób** w lewym okienku nawigacji, w polu wyszukiwania wpisz `WordPress LEMP7 Max Performance` i naciśnij klawisz ENTER.

1. W wynikach wyszukiwania wybierz pozycję **WordPress Lemp Max Performance** . Wybierz plan oprogramowania **WordPress Lemp Max Performance for CentOS** jako **Plan oprogramowania** i wybierz pozycję **Utwórz**.

1. Na karcie **podstawy** Użyj ustawień maszyny wirtualnej określonych w tabeli poniżej obrazu:

    ![Karta podstawowe informacje na temat tworzenia maszyny wirtualnej](./media/functions-create-vnet/create-vm-1.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której są tworzone zasoby. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Wybierz `myResourceGroup` lub grupę zasobów utworzoną za pomocą aplikacji funkcji. Korzystanie z tej samej grupy zasobów dla aplikacji funkcji, maszyny wirtualnej WordPress i planu hostingu ułatwia czyszczenie zasobów po zakończeniu pracy z tym samouczkiem. |
    | **Nazwa maszyny wirtualnej** | VNET-Wordpress | Nazwa maszyny wirtualnej musi być unikatowa w grupie zasobów |
    | **[Region (Region)](https://azure.microsoft.com/regions/)** | Terenie Europa Zachodnia | Wybierz region znajdujący się w sąsiedztwie lub w sąsiedztwie funkcji, które uzyskują dostęp do maszyny wirtualnej. |
    | **Rozmiar** | B1s | Wybierz **Zmień rozmiar** , a następnie wybierz obraz B1s Standard, który ma 1 vCPU i 1 GB pamięci. |
    | **Authentication type** (Typ uwierzytelniania) | Hasło | Aby używać uwierzytelniania hasła, należy również określić **nazwę użytkownika**, bezpieczne **hasło**, a następnie **potwierdzić hasło**. W tym samouczku nie będzie konieczne zalogowanie się do maszyny wirtualnej, chyba że trzeba rozwiązać problemy. |

1. Wybierz kartę **Sieć** , a następnie w obszarze Konfiguruj sieci wirtualne wybierz pozycję **Utwórz nowy**.

1. W obszarze **Utwórz sieć wirtualną** Użyj ustawień w tabeli poniżej obrazu:

    ![Karta Sieć w obszarze Utwórz maszynę wirtualną](./media/functions-create-vnet/create-vm-2.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Nazwa** | Moja resourceName — Sieć wirtualna | Możesz użyć domyślnej nazwy wygenerowanej dla sieci wirtualnej. |
    | **Zakres adresów** | 10.10.0.0/16 | Użyj jednego zakresu adresów dla sieci wirtualnej. |
    | **Nazwa podsieci** | Tutorial-Net | Nazwa podsieci. |
    | **Zakres adresów** (podsieci) | 10.10.1.0/24   | Rozmiar podsieci definiuje liczbę interfejsów, które można dodać do podsieci. Ta podsieć jest używana przez lokację WordPress.  `/24`Podsieć zawiera adresy hostów 254. |

1. Wybierz **przycisk OK** , aby utworzyć sieć wirtualną.

1. Na karcie **Sieć** wybierz opcję **Brak** dla **publicznego adresu IP**.

1. Wybierz kartę **Zarządzanie** , a następnie w obszarze **konto magazynu diagnostyki** wybierz konto magazynu utworzone za pomocą aplikacji funkcji.

1. Wybierz pozycję **Przejrzyj i utwórz**. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Proces tworzenia maszyny wirtualnej trwa kilka minut. Utworzona maszyna wirtualna może uzyskać dostęp tylko do sieci wirtualnej.

1. Po utworzeniu maszyny wirtualnej wybierz pozycję **Przejdź do zasobu** , aby wyświetlić stronę nowej maszyny wirtualnej, a następnie wybierz pozycję **Sieć** w obszarze **Ustawienia**.

1. Sprawdź, czy nie ma **publicznego adresu IP**. Zanotuj **prywatny adres IP**, za pomocą którego chcesz nawiązać połączenie z maszyną wirtualną z poziomu aplikacji funkcji.

    ![Ustawienia sieci na maszynie wirtualnej](./media/functions-create-vnet/vm-networking.png)

Masz już całkowicie wdrożoną witrynę WordPress w sieci wirtualnej. Ta witryna nie jest dostępna z publicznego Internetu.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Łączenie aplikacji funkcji z siecią wirtualną

W przypadku witryny WordPress działającej na maszynie wirtualnej w sieci wirtualnej możesz teraz połączyć swoją aplikację funkcji z tą siecią wirtualną.

1. W nowej aplikacji funkcji wybierz pozycję **Sieć** w menu po lewej stronie.

1. W obszarze integracja z siecią **wirtualną** wybierz **pozycję kliknij tutaj, aby ją skonfigurować**.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Wybieranie sieci w aplikacji funkcji":::

1. Na stronie **integracja z siecią wirtualną** wybierz pozycję **Dodaj sieć wirtualną**.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="Dodawanie wersji zapoznawczej integracji sieci wirtualnej":::

1. W obszarze **stan funkcji sieci** Użyj ustawień w tabeli poniżej obrazu:

    ![Zdefiniuj sieć wirtualną aplikacji funkcji](./media/functions-create-vnet/networking-3.png)

    | Ustawienie      | Sugerowana wartość  | Opis      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | Moja resourceName — Sieć wirtualna | Ta sieć wirtualna jest utworzoną wcześniej. |
    | **Podsieć** | Utwórz nową podsieć | Utwórz podsieć w sieci wirtualnej, która ma być używana przez aplikację funkcji. Integracja sieci wirtualnej musi być skonfigurowana do używania pustej podsieci. Nie ma znaczenia, że funkcje korzystają z innej podsieci niż maszyna wirtualna. Sieć wirtualna automatycznie kieruje ruchem między dwiema podsieciami. |
    | **Nazwa podsieci** | Function-Net | Nazwa nowej podsieci. |
    | **Blok adresów sieci wirtualnej** | 10.10.0.0/16 | Wybierz ten sam blok adresów, który jest używany przez witrynę WordPress. Powinien być zdefiniowany tylko jeden blok adresu. |
    | **Zakres adresów** | 10.10.2.0/24   | Rozmiar podsieci ogranicza łączną liczbę wystąpień, do których można skalować aplikację funkcji planu Premium. W tym przykładzie używa się `/24` podsieci z 254 dostępnych adresów hosta. Ta podsieć jest nadmiernie obsługiwana, ale prosta do obliczenia. |

1. Wybierz **przycisk OK** , aby dodać podsieć. Zamknij strony **Integracja sieci wirtualnej** i **stan funkcji sieciowych** , aby powrócić do strony aplikacji funkcji.

Aplikacja funkcji może teraz uzyskiwać dostęp do sieci wirtualnej, w której jest uruchomiona witryna WordPress. Następnie użyj [serwery proxy usługi Azure Functions](functions-proxies.md) , aby zwrócić plik z witryny WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Tworzenie serwera proxy w celu uzyskania dostępu do zasobów maszyny wirtualnej

Przy włączonej integracji sieci wirtualnej można utworzyć w aplikacji funkcji serwer proxy do przesyłania dalej żądań do maszyny wirtualnej działającej w sieci wirtualnej.

1. W aplikacji funkcji wybierz pozycję  **serwery proxy** z menu po lewej stronie, a następnie wybierz pozycję **Dodaj**. Użyj ustawień serwera proxy w tabeli poniżej obrazu:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Definiowanie ustawień serwera proxy":::

    | Ustawienie  | Sugerowana wartość  | Opis      |
    | -------- | ---------------- | ---------------- |
    | **Nazwa** | Zakład | Nazwa może być dowolną wartością. Służy do identyfikowania serwera proxy. |
    | **Szablon trasy** | /plant | Trasy mapowane do zasobu maszyny wirtualnej. |
    | **Adres URL zaplecza** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Zamień na `<YOUR_VM_IP>` adres IP utworzonej wcześniej maszyny wirtualnej WordPress. To mapowanie zwraca pojedynczy plik z lokacji. |

1. Wybierz pozycję **Utwórz** , aby dodać serwer proxy do aplikacji funkcji.

## <a name="try-it-out"></a>Czas to wypróbować

1. W przeglądarce spróbuj uzyskać dostęp do adresu URL, który był używany jako **adres URL zaplecza**. Zgodnie z oczekiwaniami upłynął limit czasu żądania. Limit czasu występuje, ponieważ witryna WordPress jest połączona tylko z siecią wirtualną, a nie z Internetem.

1. Skopiuj wartość **adres URL serwera proxy** z nowego serwera proxy i wklej ją na pasku adresu przeglądarki. Zwrócony obraz pochodzi z witryny WordPress działającej w sieci wirtualnej.

    ![Plik obrazu zakładu zwrócony z witryny WordPress](./media/functions-create-vnet/plant.png)

Aplikacja funkcji jest połączona zarówno z Internetem, jak i z siecią wirtualną. Serwer proxy otrzymuje żądanie za pośrednictwem publicznej sieci Internet, a następnie działa jako prosty serwer proxy HTTP do przesyłania dalej tego żądania do połączonej sieci wirtualnej. Serwer proxy przekazuje następnie odpowiedź z powrotem do użytkownika publicznie przez Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku witryna WordPress służy jako interfejs API, który jest wywoływany przy użyciu serwera proxy w aplikacji funkcji. W tym scenariuszu jest dostępny dobry samouczek, ponieważ można go łatwo skonfigurować i wizualizować. Można użyć dowolnego innego interfejsu API wdrożonego w ramach sieci wirtualnej. Można również utworzyć funkcję z kodem, który wywołuje interfejsy API wdrożone w ramach sieci wirtualnej. Bardziej realistyczny scenariusz to funkcja, która używa interfejsów API klienta danych do wywoływania SQL Server wystąpienia wdrożonego w sieci wirtualnej.

Funkcje działające w planie Premium współdzielą tę samą podstawową infrastrukturę App Serviceową co aplikacje sieci Web w planach PremiumV2. Cała dokumentacja usługi [Web Apps w Azure App Service](../app-service/overview.md) ma zastosowanie do funkcji planu Premium.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o opcjach sieci w funkcjach](./functions-networking-options.md)

[Plan Premium]: functions-premium-plan.md
