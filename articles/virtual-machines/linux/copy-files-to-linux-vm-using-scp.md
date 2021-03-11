---
title: Przenoszenie plików do i z maszyn wirtualnych z systemem Linux platformy Azure przy użyciu usługi SCP
description: Bezpieczne przenoszenie plików do i z maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu punktu połączenia usługi i pary kluczy SSH.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 83b57055ee7a3fedab014abeab96520c3877b843
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558444"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Przenoszenie plików do i z maszyny wirtualnej z systemem Linux przy użyciu usługi SCP

W tym artykule przedstawiono sposób przenoszenia plików z stacji roboczej do maszyny wirtualnej platformy Azure z systemem Linux lub z maszyny wirtualnej platformy Azure z systemem Linux do stacji roboczej przy użyciu funkcji bezpiecznego kopiowania (SCP). Szybkie i bezpieczne przeniesienie plików między stacją roboczą a maszyną wirtualną z systemem Linux ma kluczowe znaczenie dla zarządzania infrastrukturą platformy Azure. 

W tym artykule potrzebna jest maszyna wirtualna z systemem Linux wdrożona na platformie Azure przy użyciu [plików publicznego i prywatnego klucza SSH](mac-create-ssh-keys.md). Wymagany jest również klient punktu SCP dla komputera lokalnego. Jest on oparty na usłudze SSH i uwzględniony w domyślnej powłoce bash większości komputerów z systemem Linux i Mac oraz niektórych powłok systemu Windows.

## <a name="quick-commands"></a>Szybkie polecenia

Kopiowanie pliku do maszyny wirtualnej z systemem Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiuj plik w dół z maszyny wirtualnej z systemem Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Przykładowo przenosimy plik konfiguracji platformy Azure do maszyny wirtualnej z systemem Linux i pobieramy katalog pliku dziennika, używając protokołu SCP i kluczy SSH.   

## <a name="ssh-key-pair-authentication"></a>Uwierzytelnianie pary kluczy SSH

Punkt połączenia usługi używa protokołu SSH dla warstwy transportowej. Protokół SSH obsługuje uwierzytelnianie na hoście docelowym i przenosi plik w szyfrowanym tunelu udostępnianym domyślnie przy użyciu protokołu SSH. W przypadku uwierzytelniania SSH można używać nazw użytkowników i haseł. Jednak najlepszym rozwiązaniem w zakresie zabezpieczeń jest uwierzytelnianie klucza publicznego i prywatnego protokołu SSH. Po uwierzytelnieniu połączenia przez protokół SSH usługa SCP rozpocznie kopiowanie pliku. Za pomocą prawidłowo skonfigurowanych `~/.ssh/config` i prywatnych kluczy SSH, połączenie punktu połączenia usługi może być nawiązywane przy użyciu nazwy serwera (lub adresu IP). Jeśli masz tylko jeden klucz SSH, punkt połączenia usługi szuka go w `~/.ssh/` katalogu i domyślnie użyje go w celu zalogowania się do maszyny wirtualnej.

Aby uzyskać więcej informacji na temat konfigurowania `~/.ssh/config` kluczy publicznych i prywatnych SSH, zobacz [Tworzenie kluczy SSH](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP pliku do maszyny wirtualnej z systemem Linux

W pierwszym przykładzie plik konfiguracji platformy Azure jest kopiowany do maszyny wirtualnej z systemem Linux, która jest używana do wdrażania automatyzacji. Ponieważ ten plik zawiera poświadczenia interfejsu API platformy Azure, w tym wpisy tajne, należy pamiętać o zabezpieczeniach. Szyfrowany tunel zapewniany przez protokół SSH chroni zawartość pliku.

Następujące polecenie kopiuje plik Local *. Azure/config* na maszynę wirtualną platformy Azure z nazwą FQDN *MyServer.eastus.cloudapp.Azure.com*. Nazwa użytkownika administratora na maszynie wirtualnej platformy Azure to *azureuser*. Plik jest przeznaczony dla katalogu */Home/azureuser/* . Zastąp własne wartości w tym poleceniu.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Punkt połączenia usługi z maszyną wirtualną z systemem Linux

W tym przykładzie skopiujemy katalog plików dziennika z maszyny wirtualnej z systemem Linux do stacji roboczej. Plik dziennika może lub nie może zawierać danych poufnych lub tajnych. Jednak użycie punktu połączenia usługi gwarantuje, że zawartość plików dziennika jest zaszyfrowana. Transfer plików przy użyciu punktu połączenia usługi jest najprostszym sposobem na uzyskanie katalogu dziennika i plików na stację roboczą, a także Zabezpieczanie.

Następujące polecenie kopiuje pliki z katalogu */Home/azureuser/Logs/* na maszynie wirtualnej platformy Azure do lokalnego katalogu/tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r`Flaga nakazuje usłudze SCP cykliczne kopiowanie plików i katalogów z punktu katalogu wymienionego w poleceniu.  Zauważ również, że składnia wiersza polecenia jest podobna do `cp` polecenia copy.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami, SSH i sprawdzaniem lub naprawianie dysków na maszynach wirtualnych z systemem Linux platformy Azure przy użyciu rozszerzenia VMAccess](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
