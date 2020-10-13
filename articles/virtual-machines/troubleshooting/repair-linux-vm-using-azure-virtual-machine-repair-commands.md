---
title: Naprawianie maszyny wirtualnej z systemem Linux przy użyciu poleceń naprawy maszyny wirtualnej platformy Azure | Microsoft Docs
description: W tym artykule szczegółowo opisano sposób używania poleceń naprawy maszyny wirtualnej platformy Azure do łączenia dysku z inną maszyną wirtualną z systemem Linux w celu usunięcia błędów, a następnie odbudowania oryginalnej maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: bfd3b2351a280f423ba0ef0b15318449554b5e3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91595944"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Naprawianie maszyny wirtualnej z systemem Linux za pomocą poleceń naprawiania maszyny wirtualnej platformy Azure

Jeśli maszyna wirtualna z systemem Linux na platformie Azure napotyka błąd rozruchu lub dysku, może być konieczne wykonanie środków zaradczych na dysku. Typowym przykładem może być niepowodzenie aktualizacji aplikacji, która uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano sposób używania poleceń naprawy maszyny wirtualnej platformy Azure do łączenia dysku z inną maszyną wirtualną z systemem Linux w celu usunięcia błędów, a następnie odbudowania oryginalnej maszyny wirtualnej.

> [!IMPORTANT]
> * Skrypty w tym artykule mają zastosowanie tylko do maszyn wirtualnych, które używają [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
> * Łączność wychodząca z maszyny wirtualnej (port 443) jest wymagana do uruchomienia skryptu.
> * Jednocześnie może działać tylko jeden skrypt.
> * Nie można anulować uruchomionego skryptu.
> * Maksymalny czas uruchomienia skryptu wynosi 90 minut, po upływie którego limit czasu zostanie przekroczony.
> * W przypadku maszyn wirtualnych używających Azure Disk Encryption obsługiwane są tylko dyski zarządzane szyfrowane z pojedynczym przekazaniem (z lub bez KEK).

## <a name="repair-process-overview"></a>Przegląd procesu naprawy

Teraz możesz użyć poleceń naprawy maszyny wirtualnej platformy Azure, aby zmienić dysk systemu operacyjnego dla maszyny wirtualnej, i nie musisz już usuwać i tworzyć maszyn wirtualnych.

Wykonaj następujące kroki, aby rozwiązać problem z maszyną wirtualną:

1. Uruchamianie usługi Azure Cloud Shell
2. Uruchom AZ Extension Add/Update
3. Uruchom AZ VM Repair Create
4. Uruchom polecenie AZ VM Repair Run lub wykonaj kroki zaradcze.
5. Uruchom AZ VM Repair Restore

Aby uzyskać dodatkową dokumentację i instrukcje, zobacz [AZ VM Repair](/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Przykład procesu naprawy

1. Uruchamianie usługi Azure Cloud Shell

   Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Obejmuje to popularne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem.

   Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com](https://shell.azure.com) .

   Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, a następnie wklej kod do Cloud Shell a następnie wybierz **klawisz ENTER** , aby go uruchomić.

   Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie ``az --version``, aby dowiedzieć się, jaka wersja jest używana. Jeśli musisz zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
   
   Jeśli musisz zalogować się do Cloud Shell przy użyciu innego konta niż aktualnie zalogowano się w witrynie Azure Portal, możesz użyć ``az login`` [AZ login Reference](/cli/azure/reference-index?view=azure-cli-latest#az-login&preserve-view=true).  Aby przełączać się między subskrypcjami skojarzonymi z Twoim kontem, możesz użyć ``az account set --subscription`` [AZ Account Set Reference](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true).

2. Jeśli używasz poleceń po raz pierwszy `az vm repair` , Dodaj rozszerzenie interfejsu wiersza polecenia maszyny wirtualnej do naprawy.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Jeśli wcześniej użyto `az vm repair` poleceń, Zastosuj wszystkie aktualizacje rozszerzenia maszyny wirtualnej.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Uruchom polecenie `az vm repair create`. To polecenie spowoduje utworzenie kopii dysku systemu operacyjnego dla niefunkcjonalnej maszyny wirtualnej, utworzenie naprawy maszyny wirtualnej w nowej grupie zasobów i dołączenie kopii dysku systemu operacyjnego.  Maszyna wirtualna naprawy będzie mieć ten sam rozmiar i region, co określona maszyna wirtualna nie funkcjonalna. Grupa zasobów i nazwa maszyny wirtualnej używane we wszystkich krokach będą przeznaczone dla niefunkcjonalnej maszyny wirtualnej. Jeśli maszyna wirtualna używa Azure Disk Encryption polecenie spróbuje odblokować zaszyfrowany dysk, aby był dostępny po dołączeniu do maszyny wirtualnej naprawy.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Uruchom polecenie `az vm repair run`. To polecenie spowoduje uruchomienie określonego skryptu naprawy na dołączonym dysku za pośrednictwem maszyny wirtualnej naprawy. Jeśli używasz przewodnika rozwiązywania problemów z określonym identyfikatorem uruchomienia, użyj go tutaj. w przeciwnym razie możesz użyć polecenia AZ VM Repair list-scripts, aby wyświetlić dostępne skrypty naprawcze. Użyta w tym miejscu Grupa zasobów i nazwa maszyny wirtualnej są przeznaczone dla niefunkcjonalnej maszyny wirtualnej używanej w kroku 3. Dodatkowe informacje o skryptach naprawczych można znaleźć w [bibliotece skryptów naprawy](https://github.com/Azure/repair-script-library).

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id lin-hello-world --verbose
   ```

   Opcjonalnie można wykonać wszystkie wymagane kroki zaradcze ręcznie przy użyciu maszyny wirtualnej naprawy, a następnie przejść do kroku 5.

5. Uruchom polecenie `az vm repair restore`. To polecenie spowoduje zamianę naprawionego dysku systemu operacyjnego na oryginalny dysk systemu operacyjnego maszyny wirtualnej. Użyta w tym miejscu Grupa zasobów i nazwa maszyny wirtualnej są przeznaczone dla niefunkcjonalnej maszyny wirtualnej używanej w kroku 3.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Weryfikowanie i włączanie diagnostyki rozruchu

Poniższy przykład włącza rozszerzenie diagnostyki na maszynie wirtualnej o nazwie ``myVMDeployed`` w grupie zasobów o nazwie ``myResourceGroup`` :

Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Następne kroki

* Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami RDP z maszyną wirtualną platformy Azure](./troubleshoot-rdp-connection.md).
* Problemy dotyczące uzyskiwania dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć w temacie [Rozwiązywanie problemów z łącznością aplikacji na maszynach wirtualnych na platformie Azure](./troubleshoot-app-connection.md).
* Aby uzyskać więcej informacji o korzystaniu z Menedżer zasobów, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/management/overview.md).
