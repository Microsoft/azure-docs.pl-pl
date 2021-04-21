---
title: Przenoszenie plików do i z maszyny wirtualnej za pomocą połączenia SCP
description: Bezpieczne przenoszenie plików do i z maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu połączenia SCP i pary kluczy SSH.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.subservice: ''
ms.openlocfilehash: edfc44f79cff25486fde6326ac954fe5b575d846
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816444"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>Przenoszenie plików do i z maszyny wirtualnej z systemem Linux za pomocą połączenia SCP 

W tym artykule przedstawiono sposób przenoszenia plików ze stacji roboczej do maszyny wirtualnej platformy Azure z systemem Linux lub z maszyny wirtualnej platformy Azure z systemem Linux do stacji roboczej przy użyciu bezpiecznego kopiowania (SCP). Szybkie i bezpieczne przenoszenie plików między stacją roboczą a maszyną wirtualną z systemem Linux ma kluczowe znaczenie dla zarządzania infrastrukturą platformy Azure. 

Na potrzeby tego artykułu musisz wdrożyć maszynę wirtualną z systemem Linux na platformie Azure przy użyciu plików kluczy publicznych i [prywatnych SSH.](mac-create-ssh-keys.md) Potrzebny jest również klient SCP dla komputera lokalnego. Jest ona zbudowana na podstawie SSH i jest zawarta w domyślnej powłoce Bash większości komputerów z systemami Linux i Mac oraz w programie PowerShell.


## <a name="quick-commands"></a>Szybkie polecenia

Kopiowanie pliku do maszyny wirtualnej z systemem Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiowanie pliku z maszyny wirtualnej z systemem Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Jako przykład przenosimy plik konfiguracji platformy Azure do maszyny wirtualnej z systemem Linux i ściągamy katalog pliku dziennika przy użyciu kluczy SCP i SSH.   

## <a name="ssh-key-pair-authentication"></a>Uwierzytelnianie pary kluczy SSH

SCP używa protokołu SSH dla warstwy transportu. SSH obsługuje uwierzytelnianie na hoście docelowym i przenosi plik w szyfrowanym tunelu dostarczonym domyślnie za pomocą protokołu SSH. W przypadku uwierzytelniania SSH można używać nazw użytkowników i haseł. Jednak najlepszym rozwiązaniem w zakresie zabezpieczeń jest uwierzytelnianie za pomocą klucza publicznego i prywatnego SSH. Po uwierzytelnieniu połączenia przez połączenie SSH program SCP rozpoczyna kopiowanie pliku. Połączenie SCP można nawiązane przy użyciu prawidłowo skonfigurowanego klucza publicznego i prywatnego SSH przy użyciu nazwy `~/.ssh/config` serwera (lub adresu IP). Jeśli masz tylko jeden klucz SSH, program SCP szuka go w katalogu i domyślnie używa go do logowania `~/.ssh/` się do maszyny wirtualnej.

Aby uzyskać więcej informacji na temat konfigurowania publicznych i prywatnych kluczy `~/.ssh/config` SSH, zobacz [Create SSH keys (Tworzenie kluczy SSH).](mac-create-ssh-keys.md)

## <a name="scp-a-file-to-a-linux-vm"></a>SCP plik do maszyny wirtualnej z systemem Linux

W pierwszym przykładzie skopiujemy plik konfiguracji platformy Azure do maszyny wirtualnej z systemem Linux, która jest używana do wdrażania automatyzacji. Ponieważ ten plik zawiera poświadczenia interfejsu API platformy Azure, które zawierają wpisy tajne, zabezpieczenia są ważne. Zaszyfrowany tunel dostarczany przez połączenie SSH chroni zawartość pliku.

Następujące polecenie kopiuje lokalny plik *.azure/config* na maszynę wirtualną platformy Azure z myserver.eastus.cloudapp.azure.com *.* Jeśli nie masz ustawionej [sieci FQDN,](../create-fqdn.md)możesz również użyć adresu IP maszyny wirtualnej. Nazwa użytkownika administratora na maszynie wirtualnej platformy Azure to *azureuser.* Plik jest ukierunkowany na *katalog /home/azureuser/.* W tym poleceniu zastąp własne wartości.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP katalog z maszyny wirtualnej z systemem Linux

W tym przykładzie skopiujemy katalog plików dziennika z maszyny wirtualnej z systemem Linux do stacji roboczej. Plik dziennika może lub nie może zawierać poufnych lub tajnych danych. Jednak użycie narzędzia SCP gwarantuje, że zawartość plików dziennika jest szyfrowana. Transfer plików przy użyciu programu SCP jest najprostszym sposobem na przeniesienie katalogu dziennika i plików na stację roboczą przy jednoczesnym zachowaniu bezpieczeństwa.

Następujące polecenie kopiuje pliki z *katalogu /home/azureuser/logs/* na maszynie wirtualnej platformy Azure do lokalnego katalogu /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Flaga `-r` nakazuje punktowi SCP rekursywne kopiowanie plików i katalogów z punktu katalogu wymienionego w poleceniu .  Zwróć również uwagę, że składnia wiersza polecenia jest podobna do `cp` polecenia kopiowania.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie użytkownikami i SSH oraz sprawdzanie lub naprawianie dysków na maszynach wirtualnych z systemem Linux na platformie Azure przy użyciu rozszerzenia VMAccess](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
