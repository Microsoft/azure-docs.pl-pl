---
title: Pule udostępnione
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak utworzyć & wdrożyć udostępnioną pulę usługi Data Science Virtual Machines (DSVMs) jako zasób udostępniony dla zespołu.
keywords: głębokie uczenie się, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519715"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Tworzenie udostępnionej puli Virtual Machines nauki o danych

W tym artykule dowiesz się, jak utworzyć udostępnioną pulę Virtual Machines analizy danych (DSVMs) dla zespołu. Zalety korzystania z puli udostępnionej obejmują lepsze wykorzystanie zasobów, łatwiejsze udostępnianie i współpracę oraz wydajniejsze zarządzanie zasobami DSVM.

Za pomocą wielu metod i technologii można utworzyć pulę DSVMs. Ten artykuł koncentruje się na pulach dla interaktywnych maszyn wirtualnych. Alternatywna zarządzana infrastruktura obliczeniowa to Azure Machine Learning COMPUTE. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra obliczeniowego](../how-to-create-attach-compute-cluster.md).

## <a name="interactive-vm-pool"></a>Interaktywna Pula maszyn wirtualnych

Pula interaktywnych maszyn wirtualnych, które są współużytkowane przez cały zespół ds. analizy danych, umożliwia użytkownikom logowanie się do dostępnego wystąpienia DSVM zamiast tworzenia dedykowanego wystąpienia dla każdego zestawu użytkowników. Ta konfiguracja zapewnia lepszą dostępność i bardziej efektywne wykorzystanie zasobów.

Za pomocą technologii [Azure Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) można utworzyć interaktywną pulę maszyn wirtualnych. Za pomocą zestawów skalowania można tworzyć i zarządzać grupami maszyn wirtualnych o identycznej obciążeniu i skalowaniu.

Użytkownik loguje się do adresu IP lub DNS puli głównej. Zestaw skalowania automatycznie kieruje sesję do dostępnego DSVM w zestawie skalowania. Ponieważ użytkownicy chcą spójne i znane środowisko niezależnie od maszyny wirtualnej, do której się logują, wszystkie wystąpienia maszyny wirtualnej w zestawie skalowania instalują udostępniony dysk sieciowy, taki jak udział Azure Files lub udział systemu plików NFS. Udostępniony obszar roboczy użytkownika jest zwykle przechowywany w udostępnionym magazynie plików, który jest zainstalowany na każdym z wystąpień.

Możesz znaleźć przykładowy szablon Azure Resource Manager, który tworzy zestaw skalowania z wystąpieniami Ubuntu DSVM w witrynie [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Przykład [pliku parametrów](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) dla szablonu Azure Resource Manager można znaleźć w tej samej lokalizacji.

Zestaw skalowania można utworzyć na podstawie szablonu Azure Resource Manager, określając wartości dla pliku parametru w interfejsie wiersza polecenia platformy Azure:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

W poprzednich poleceniach przyjęto założenie, że:

* Kopia pliku parametrów z wartościami określonymi dla Twojego wystąpienia zestawu skalowania.
* Liczba wystąpień maszyn wirtualnych.
* Wskaźniki do udziału Azure Filesowego.
* Poświadczenia konta magazynu, które zostaną zainstalowane na każdej maszynie wirtualnej.

Plik parametru jest przywoływany lokalnie w poleceniach. Możesz również przekazać parametry wewnętrznie lub Monituj dla nich w skrypcie.  

Poprzedni szablon umożliwia port SSH i JupyterHub z zestawu skalowania frontonu do puli zaplecza Ubuntu DSVMs. Użytkownik loguje się do maszyny wirtualnej na Secure Shell (SSH) lub na JupyterHub w normalny sposób. Ponieważ wystąpienia maszyn wirtualnych można skalować w górę lub w dół, każdy stan musi być zapisany w zainstalowanym Azure Files udziale. Możesz użyć tego samego podejścia do tworzenia puli systemu Windows DSVMs.

[Skrypt instalujący udział Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) jest również dostępny w repozytorium Azure DataScienceVM w witrynie GitHub. Skrypt instaluje udział Azure Files w określonym punkcie instalacji w pliku parametrów. Skrypt tworzy również linki nietrwałe do zainstalowanego dysku w katalogu macierzystym początkowego użytkownika. Katalog notesu specyficzny dla użytkownika w udziale Azure Files jest niezależny do `$HOME/notebooks/remote` katalogu, dzięki czemu użytkownicy mogą uzyskiwać dostęp do swoich notesów Jupyter, uruchamiać je i zapisywać. Tej samej Konwencji można użyć podczas tworzenia dodatkowych użytkowników na maszynie wirtualnej, aby wskazywały obszar roboczy Jupyter każdego użytkownika na udział Azure Files.

Zestawy skalowania maszyn wirtualnych obsługują Skalowanie automatyczne. Możesz ustawić reguły, kiedy należy utworzyć dodatkowe wystąpienia i kiedy skalować wystąpienia w dół. Na przykład można skalować w dół do zero wystąpień, aby zaoszczędzić na kosztach użycia sprzętu w chmurze, gdy maszyny wirtualne nie są używane. Strony dokumentacji zestawów skalowania maszyn wirtualnych zawierają szczegółowe kroki [skalowania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)automatycznego.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj wspólną tożsamość](dsvm-common-identity.md)
* [Bezpieczne przechowywanie poświadczeń w celu uzyskania dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)