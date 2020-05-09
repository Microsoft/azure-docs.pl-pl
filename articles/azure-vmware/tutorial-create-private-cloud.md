---
title: Samouczek — wdrażanie klastra vSphere na platformie Azure
description: Dowiedz się, jak wdrożyć klaster vSphere na platformie Azure przy użyciu rozwiązania Azure VMWare (Automatyczna synchronizacja)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 6a192454367f2e6ca071e9cfe0a9f1a94868cbdb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977707"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Samouczek: Wdrażanie chmury prywatnej automatycznej synchronizacji na platformie Azure

Rozwiązanie Azure VMware (Automatyczna synchronizacja) umożliwia wdrażanie klastra vSphere na platformie Azure. Minimalne początkowe wdrożenie to trzy hosty. Dodatkowe hosty mogą być dodawane pojedynczo do maksymalnie 16 hostów na klaster. 

Ponieważ funkcja automatycznej synchronizacji nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego programu vCenter podczas uruchamiania, należy przeprowadzić dodatkową konfigurację i połączenie z lokalnym wystąpieniem programu vCenter, siecią wirtualną i wieloma innymi. Te procedury i powiązane z nim wymagania wstępne zostaną omówione w tej serii samouczków.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie chmury prywatnej AVS
> * Weryfikowanie wdrożenia chmury prywatnej

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Odpowiednie prawa administracyjne i uprawnienia do tworzenia chmury prywatnej.
- Upewnij się, że skonfigurowano odpowiednią sieć, zgodnie z opisem w [samouczku: Lista kontrolna sieci](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Aby można było korzystać z rozwiązania VMWare na platformie Azure, należy najpierw zarejestrować dostawcę zasobów. Poniższy przykład rejestruje dostawcę zasobów w ramach subskrypcji.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Chmurę prywatną można utworzyć za pomocą [Azure Portal](#azure-portal) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](#azure-cli).

### <a name="azure-portal"></a>Azure Portal

W Azure Portal wybierz pozycję **+ Utwórz nowy zasób**. W polu tekstowym **Wyszukaj w witrynie Marketplace** `Azure VMware Solution`, a następnie wybierz pozycję **Azure VMware Solution** z listy. W oknie **rozwiązanie platformy Azure VMware** wybierz pozycję **Utwórz** .

Na karcie **podstawowe** wpisz wartości pól. W poniższej tabeli przedstawiono szczegółową listę właściwości.

| Pole   | Wartość  |
| ---| --- |
| **Subskrypcja** | Subskrypcja, której planujesz użyć do wdrożenia.|
| **Grupa zasobów** | Grupa zasobów dla zasobów w chmurze prywatnej. |
| **Lokalizacja** | Wybierz lokalizację, na przykład **Wschodnie stany USA**.|
| **Nazwa zasobu** | Nazwa chmury prywatnej automatycznej synchronizacji. |
| **Magazyn** | Wybierz wartość SKU wymienionych: AV36 |
| **Pracując** | Jest to liczba hostów do dodania do klastra chmury prywatnej. Wartość domyślna to 3. Ta wartość może zostać podniesiona lub obniżona po wdrożeniu.  |
| **hasło administratora vCenter** | Wprowadź hasło administratora chmury. |
| **Hasło Menedżera NSX-T** | Wprowadź hasło administratora NSX-T. |
| **Blok adresów** | Wprowadź blok adresów IP dla sieci CIDR dla chmury prywatnej. Przykładem jest 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Tworzenie chmury prywatnej" border="true":::

Po zakończeniu wybierz pozycję **Przegląd + Utwórz**. Na następnym ekranie Sprawdź poprawność wprowadzonych informacji. Jeśli wszystkie informacje są poprawne, wybierz pozycję **Utwórz**.

> [!NOTE]
> Ten krok zajmuje mniej niż dwie godziny. 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Alternatywnie możesz użyć interfejsu wiersza polecenia platformy Azure, aby utworzyć chmurę prywatną na platformie Azure. W tym celu można użyć Azure Cloud Shell, wykonując następujące kroki.

#### <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony https://shell.azure.com/bash. Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz **Enter** , aby go uruchomić.

#### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Tworzenie chmury prywatnej

Aby utworzyć chmurę prywatną do automatycznej synchronizacji, należy podać nazwę grupy zasobów, nazwę chmury prywatnej, lokalizację, rozmiar klastra


|Właściwość  |Opis  |
|---------|---------|
|Nazwa grupy zasobów     | Nazwa grupy zasobów, do której należy wdrożyć chmurę prywatną.        |
|Nazwa chmury prywatnej     | Nazwa chmury prywatnej.        |
|Lokalizacja     | Lokalizacja używana dla chmury prywatnej         |
|Rozmiar klastra     | Rozmiar klastra. Wartość minimalna to 3.         |
|Blok sieciowy     | Zakres CIDR używany przez chmurę prywatną. Zalecane jest, aby była unikatowa w środowisku lokalnym, a także w środowisku platformy Azure.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Sprawdzenie, czy wdrożenie zakończyło się pomyślnie

Przejdź do utworzonej grupy zasobów i wybierz chmurę prywatną, po zakończeniu wdrażania zobaczysz następujący ekran i zobaczysz stan **powodzenie**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Weryfikowanie wdrożenia chmury prywatnej" border="true":::

## <a name="delete-a-private-cloud"></a>Usuwanie chmury prywatnej

Jeśli masz niezbędną chmurę prywatną do automatycznej synchronizacji, możesz ją usunąć. Po usunięciu chmury prywatnej wszystkie klastry wraz ze wszystkimi składnikami zostaną usunięte.

Aby to zrobić, przejdź do chmury prywatnej w Azure Portal i wybierz pozycję **Usuń**. Na stronie Potwierdzenie Potwierdź nazwę chmury prywatnej, a następnie wybierz pozycję **tak**.

> [!CAUTION]
> Usuwanie chmury prywatnej jest operacją nieodwracalną. Po usunięciu chmury prywatnej dane nie mogą zostać odzyskane, ponieważ kończy wszystkie uruchomione obciążenia, składniki i niszczy wszystkie dane chmury prywatnej oraz ustawienia konfiguracji, w tym publiczne adresy IP. 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie chmury prywatnej AVS
> * Zweryfikowano wdrożenie chmury prywatnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć sieć wirtualną do użycia z chmurą prywatną w ramach konfigurowania lokalnego zarządzania klastrami w chmurze prywatnej.

> [!div class="nextstepaction"]
> [Tworzenie Virtual Network](tutorial-configure-networking.md)
