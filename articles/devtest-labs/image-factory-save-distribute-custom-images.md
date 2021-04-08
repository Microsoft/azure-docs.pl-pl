---
title: Zapisuj i Dystrybuuj obrazy w Azure DevTest Labs | Microsoft Docs
description: Ten artykuł zawiera instrukcje dotyczące zapisywania obrazów niestandardowych z już utworzonych maszyn wirtualnych w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476244"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Zapisywanie obrazów niestandardowych i ich dystrybucja do wielu laboratoriów
Ten artykuł zawiera instrukcje dotyczące zapisywania obrazów niestandardowych z już utworzonych maszyn wirtualnych. Omówiono w nim również sposób dystrybuowania tych obrazów niestandardowych do innych laboratoriów DevTest w organizacji.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące elementy powinny być już na miejscu:

- Laboratorium dla fabryki obrazów w Azure DevTest Labs.
- Projekt usługi Azure DevOps używany do automatyzowania fabryki obrazu.
- Lokalizacja kodu źródłowego zawierająca skrypty i konfigurację (w naszym przykładzie w tym samym projekcie DevOps wymienionym w poprzednim kroku).
- Definicja kompilacji służąca do organizowania zadań programu Azure PowerShell.

W razie potrzeby wykonaj kroki opisane w sekcji [Uruchamianie fabryki obrazu z usługi Azure DevOps](image-factory-set-up-devops-lab.md) , aby utworzyć lub skonfigurować te elementy. 

## <a name="save-vms-as-generalized-vhds"></a>Zapisuj maszyny wirtualne jako uogólnione dyski VHD
Zapisz istniejące maszyny wirtualne jako uogólnione wirtualne dyski twarde.  Istnieje przykładowy skrypt programu PowerShell umożliwiający zapisanie istniejących maszyn wirtualnych jako uogólnionych dysków VHD. Aby go użyć, należy najpierw dodać kolejne zadanie **programu Azure PowerShell** do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Dodaj krok Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Gdy masz nowe zadanie na liście, wybierz element, aby można było wypełnić wszystkie szczegóły, jak pokazano na poniższej ilustracji: 

![Ustawienia programu PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Uogólnione i wyspecjalizowane obrazy niestandardowe
W [Azure Portal](https://portal.azure.com)podczas tworzenia obrazu niestandardowego z poziomu maszyny wirtualnej można wybrać opcję utworzenia uogólnionego lub wyspecjalizowanego obrazu niestandardowego.

- **Wyspecjalizowany obraz niestandardowy:** Na maszynie nie uruchomiono narzędzia Sysprep/deaprowizacji. Oznacza to, że obraz jest dokładną kopią dysku systemu operacyjnego na istniejącej maszynie wirtualnej (migawkę).  Te same pliki, aplikacje, konta użytkowników, nazwy komputerów i tak dalej, są obecne podczas tworzenia nowego komputera na podstawie tego obrazu niestandardowego.
- **Uogólniony obraz niestandardowy:** Na komputerze uruchomiono polecenie Sysprep/cofanie aprowizacji.  Po uruchomieniu tego procesu program usuwa konta użytkowników, usuwa nazwę komputera, przyłączy gałęzie rejestru użytkownika itp., z celem uogólniania obrazu, aby można je było dostosować podczas tworzenia kolejnej maszyny wirtualnej.  Podczas uogólniania maszyny wirtualnej (przez uruchomienie programu Sysprep) proces niszczy bieżącą maszynę wirtualną — nie będzie już działać.

Skrypt służący do przyciągania obrazów niestandardowych w fabryce obrazu będzie zapisywać wirtualne dyski twarde dla wszystkich maszyn wirtualnych utworzonych w poprzednim kroku (identyfikowanych na podstawie tagu w zasobie na platformie Azure).

## <a name="update-configuration-for-distributing-images"></a>Aktualizacja konfiguracji do dystrybucji obrazów
Następnym krokiem w procesie jest wypychanie obrazów niestandardowych z laboratorium fabryki obrazu do innych laboratoriów, które ich potrzebują. Podstawowym elementem tego procesu jest **labs.jsw** pliku konfiguracji. Ten plik można znaleźć w folderze **konfiguracji** zawartym w fabryce obrazu.

W labs.jsw pliku konfiguracyjnym znajdują się dwa kluczowe elementy:

- Unikatowa identyfikacja określonego laboratorium docelowego przy użyciu identyfikatora subskrypcji i nazwy laboratorium.
- Określony zestaw obrazów, który powinien zostać wypychany do laboratorium jako ścieżki względne do katalogu głównego konfiguracji. Możesz również określić cały folder (Aby uzyskać wszystkie obrazy znajdujące się w tym folderze).

Oto przykład labs.jsw pliku z dwiema wymienionymi laboratoriami. W takim przypadku obrazy są dystrybuowane do dwóch różnych laboratoriów.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Tworzenie zadania kompilacji
Korzystając z tych samych kroków opisanych wcześniej w tym artykule, Dodaj dodatkowe zadanie kompilacji **programu Azure PowerShell** do definicji kompilacji. Wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Kompiluj zadanie w celu dystrybucji obrazów](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametry są następujące: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

To zadanie pobiera wszystkie obrazy niestandardowe obecne w fabryce obrazu i wypycha je do wszystkich laboratoriów zdefiniowanych w Labs.jsna pliku.

## <a name="queue-the-build"></a>Kolejkowanie kompilacji
Po zakończeniu zadania kompilacji dystrybucji Utwórz kolejkę nowej kompilacji, aby upewnić się, że wszystko działa. Po pomyślnym zakończeniu kompilacji nowe obrazy niestandardowe będą widoczne w laboratorium docelowym, które zostały wprowadzone do Labs.jsw pliku konfiguracyjnym.

## <a name="next-steps"></a>Następne kroki
W następnym artykule z serii należy zaktualizować fabrykę obrazu przy użyciu zasad przechowywania i kroków czyszczenia: [Ustaw zasady przechowywania i Uruchom skrypty oczyszczania](image-factory-set-retention-policy-cleanup.md).
