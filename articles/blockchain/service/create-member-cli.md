---
title: Tworzenie członka Azure Blockchain Service — interfejs wiersza polecenia platformy Azure
description: Utwórz członka Azure Blockchain Service dla konsorcjum łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 2d94640e1c6a037893fb1ad9198b82195c46dd24
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768129"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Szybki start: tworzenie nowego Azure Blockchain Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start wdrożysz nowego członka łańcucha bloków i konsorcjum w Azure Blockchain Service interfejsie wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli musisz zainstalować lub uaktualnić usługę , zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Zaloguj się.

    Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu polecenia [az login](/cli/azure/reference-index#az_login).

    ```azurecli
    az login
    ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    Aby pracować z odwołaniami do rozszerzenia dla interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować rozszerzenie.  Rozszerzenia interfejsu wiersza polecenia platformy Azure dają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze dostarczone jako część podstawowego interfejsu wiersza polecenia.  Aby dowiedzieć się więcej na temat rozszerzeń, w tym aktualizacji i odinstalowywania, zobacz [Używanie rozszerzeń z interfejsem wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

    Zainstaluj [rozszerzenie dla Azure Blockchain Service,](/cli/azure/ext/blockchain/blockchain) uruchamiając następujące polecenie:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Utwórz grupę zasobów.

    Azure Blockchain Service, podobnie jak wszystkie zasoby platformy Azure, należy wdrożyć w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

    W tym przewodniku Szybki start utwórz grupę zasobów o nazwie _myResourceGroup_ w lokalizacji _eastus_ za pomocą następującego [polecenia az group create:](/cli/azure/group#az_group_create)

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Tworzenie członka łańcucha bloków

Członek Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnego konsorcjum. Podczas aprowizowania członka możesz utworzyć sieć konsorcjum lub dołączyć do niej. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba członków łańcucha bloków wymaganych przez uczestników zależy od scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą udostępniać członków innym uczestnikom. Aby uzyskać więcej informacji na temat konsorcjum, zobacz [Azure Blockchain Service consortium](consortium.md).

Istnieje kilka parametrów i właściwości, które należy przekazać. Zastąp przykładowe parametry swoimi wartościami.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasoby są tworzone. Użyj grupy zasobów utworzonej w poprzedniej sekcji.
| **Nazwa** | Unikatowa nazwa identyfikująca twój Azure Blockchain Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **Lokalizacji** | Region świadczenia usługi Azure, w którym jest tworzony członek łańcucha bloków. Na przykład `westus2`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. Funkcje mogą nie być dostępne w niektórych regionach. Azure Blockchain Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
| **hasło** | Hasło domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym publicznym punktem końcowym węzła transakcji członka łańcucha bloków.
| **Protokół** | Protokół łańcucha bloków. Obecnie obsługiwany *jest* protokół kworum.
| **Konsorcjum** | Nazwa konsorcjum, które ma dołączyć lub utworzyć. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).
| **consortium-management-account-password** | Hasło do konta konsorcjum jest również nazywane hasłem konta członkowskiego. Hasło konta członkowskiego jest używane do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla członka. Do zarządzania konsorcjum należy użyć konta członkowskiego i hasła do konta członka.
| **Numer jednostki magazynowej** | Typ warstwy. *Standardowa* lub *Podstawowa.* Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji koncepcji. Użyj warstwy *Standardowa* dla wdrożeń klasy produkcyjnej. Użyj również warstwy *Standardowa,* jeśli używasz Blockchain Data Manager lub wysyłasz dużą ilość transakcji prywatnych. Zmiana warstwy cenowej między podstawową i standardową po utworzeniu członków nie jest obsługiwana.

Utworzenie członka łańcucha bloków i zasobów obsługi trwa około 10 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć członka łańcucha bloków utworzonego w następnym przewodniku Szybki start lub samouczku. Gdy zasoby nie będą już potrzebne, możesz je usunąć, usuwając `myResourceGroup` grupę zasobów utworzoną na potrzeby przewodnika Szybki start.

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono Azure Blockchain Service i nowe konsorcjum. Wypróbuj następny przewodnik Szybki start, aby użyć Azure Blockchain Development Kit for Ethereum w celu dołączenia do Azure Blockchain Service członkowskiego.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia Visual Studio Code za pomocą Azure Blockchain Service](connect-vscode.md)
