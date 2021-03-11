---
title: Konfigurowanie zasad przechowywania w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skonfigurować zasady przechowywania, wyczyścić fabrykę i wycofać stare obrazy z DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85384e88f8d456c7bf67302a57618d7a9703a5ee
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550029"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Konfigurowanie zasad przechowywania w Azure DevTest Labs
W tym artykule opisano konfigurowanie zasad przechowywania, czyszczenie fabryki oraz wycofywanie starych obrazów ze wszystkich innych laboratoriów DevTest w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że zostały wykonane następujące artykuły:

- [Tworzenie fabryki obrazów](image-factory-create.md)
- [Uruchamianie fabryki obrazów z usługi Azure DevOps](image-factory-set-up-devops-lab.md)
- [Zapisywanie obrazów niestandardowych i ich dystrybucja do wielu laboratoriów](image-factory-save-distribute-custom-images.md)

Następujące elementy powinny być już na miejscu:

- Laboratorium dla fabryki obrazów w Azure DevTest Labs
- Co najmniej jeden Azure DevTest Labs docelowy, w którym fabryka będzie dystrybuować złote obrazy
- Projekt usługi Azure DevOps używany do automatyzowania fabryki obrazu.
- Lokalizacja kodu źródłowego zawierającego skrypty i konfigurację (w naszym przykładzie w tym samym projekcie DevOps użytym powyżej)
- Definicja kompilacji służąca do organizowania zadań programu Azure PowerShell
 
## <a name="setting-the-retention-policy"></a>Ustawianie zasad przechowywania
Przed skonfigurowaniem kroków oczyszczania Zdefiniuj liczbę obrazów historycznych, które chcesz zachować w laboratorium DevTest Labs. Po wykonaniu artykułu [Uruchom fabrykę obrazu z usługi Azure DevOps](image-factory-set-up-devops-lab.md) , skonfigurowano różne zmienne kompilacji. Jedna z nich została **ImageRetention**. Ta zmienna jest ustawiana na `1` , co oznacza, że laboratorium DevTest Labs nie będzie zachować historii obrazów niestandardowych. Dostępne są tylko najnowsze obrazy rozproszone. Jeśli zmienisz tę zmienną na `2` , zostanie zachowany najnowszy rozproszony obraz oraz poprzednie. Możesz ustawić tę wartość, aby zdefiniować liczbę obrazów historycznych, które mają być utrzymywane w DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Czyszczenie fabryki
Pierwszym krokiem w czyszczeniu fabryki jest usunięcie złota maszyn wirtualnych obrazów z fabryki obrazu. Istnieje skrypt służący do wykonywania tego zadania podobnie jak w przypadku poprzednich skryptów. Pierwszym krokiem jest dodanie kolejnego zadania **programu Azure PowerShell** do definicji kompilacji, jak pokazano na poniższej ilustracji:

![Krok programu PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Po utworzeniu nowego zadania na liście Wybierz element i Wypełnij wszystkie szczegóły, jak pokazano na poniższej ilustracji:

![Zadanie programu PowerShell oczyszczania starych obrazów](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skryptu są następujące: `-DevTestLabName $(devTestLabName)` .

## <a name="retire-old-images"></a>Wycofywanie starych obrazów 
To zadanie usuwa wszystkie stare obrazy, zachowując tylko historię pasującą do zmiennej kompilacji **ImageRetention** . Dodaj do naszej definicji kompilacji dodatkowe zadanie kompilacji **programu Azure PowerShell** . Po dodaniu wybierz zadanie i wypełnij szczegóły, jak pokazano na poniższej ilustracji: 

![Wycofywanie starych obrazów — zadanie programu PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skryptu są następujące: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Kolejkowanie kompilacji
Po zakończeniu definiowania kompilacji Utwórz kolejkę nowej kompilacji, aby upewnić się, że wszystko działa. Po pomyślnym zakończeniu kompilacji nowe obrazy niestandardowe są wyświetlane w laboratorium docelowym, a w przypadku zaznaczenia programu Image Factory Lab nie są wyświetlane maszyny wirtualne z obsługą administracyjną. Ponadto w przypadku tworzenia kolejek dalszych kompilacji są wyświetlane zadania oczyszczania, które wycofywanie starych obrazów niestandardowych z laboratoriów DevTest zgodnie z wartością przechowywania ustawioną w zmiennych kompilacji.

> [!NOTE]
> Jeśli potok kompilacji został wykonany na końcu ostatniego artykułu w serii, ręcznie usuń maszyny wirtualne, które zostały utworzone w laboratorium fabryki obrazu przed przystąpieniem do kolejkowania nowej kompilacji.  Krok ręcznego czyszczenia jest wymagany tylko wtedy, gdy ustawimy wszystko i sprawdzimy, czy działa.



## <a name="summary"></a>Podsumowanie
Teraz masz działającą fabrykę obrazu, która umożliwia generowanie i dystrybucję obrazów niestandardowych do laboratoriów na żądanie. W tym momencie jest to bardzo ważne, aby zapewnić prawidłowe skonfigurowanie obrazów i zidentyfikowanie laboratoriów docelowych. Jak wspomniano w poprzednim artykule, **Labs.js** pliku znajdującego się w folderze **konfiguracyjnym** określa, które obrazy należy udostępnić w każdej z laboratoriów docelowych. Po dodaniu innych laboratoriów DevTest do organizacji wystarczy dodać wpis w Labs.jsna potrzeby nowego laboratorium.

Dodawanie nowego obrazu do fabryki jest również proste. Jeśli chcesz dołączyć nowy obraz do fabryki, Otwórz [Azure Portal](https://portal.azure.com), przejdź do portalu fabryki DevTest Labs, wybierz przycisk, aby dodać maszynę wirtualną, a następnie wybierz żądany obraz i artefakty witryny Marketplace. Zamiast wybierać przycisk **Utwórz** , aby utworzyć nową maszynę wirtualną, wybierz pozycję **Wyświetl Azure Resource Manager szablon** i Zapisz szablon jako plik JSON w miejscu w folderze **GoldenImages** w repozytorium. Przy następnym uruchomieniu fabryki obrazu zostanie utworzony niestandardowy obraz.


## <a name="next-steps"></a>Następne kroki
1. [Zaplanuj kompilację/wydanie](/azure/devops/pipelines/build/triggers?tabs=designer) , aby okresowo uruchamiać fabrykę obrazu. W regularnych odstępach czasu odświeżane są obrazy generowane przez fabrykę.
2. Utwórz więcej złota obrazów dla fabryki. Możesz również rozważyć [utworzenie artefaktów](devtest-lab-artifact-author.md) , aby dodać do skryptu dodatkowe fragmenty zadań konfiguracyjnych maszyny wirtualnej i uwzględnić artefakty w obrazach fabryki.
4. Utwórz [oddzielną kompilację/wydanie](/azure/devops/pipelines/overview?view=azure-devops-2019) , aby osobno uruchomić skrypt **DistributeImages** . Ten skrypt można uruchomić po wprowadzeniu zmian do Labs.jsi pobrania obrazów skopiowanych do laboratoriów docelowych bez konieczności ponownego tworzenia wszystkich obrazów.

