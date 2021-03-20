---
title: Użyj Azure DevTest Labs do szkolenia | Microsoft Docs
description: Ten artykuł zawiera szczegółowe czynności, które można wykonać, aby skonfigurować laboratorium do szkolenia w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2de9550c62f04286a4f9ad42238bfefb9846477
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294853"
---
# <a name="use-azure-devtest-labs-for-training"></a>Użyj Azure DevTest Labs do szkolenia
Azure DevTest Labs może służyć do implementowania wielu kluczowych scenariuszy Oprócz tworzenia i testowania. Jednym z tych scenariuszy jest skonfigurowanie laboratorium do szkoleń. Azure DevTest Labs umożliwia utworzenie laboratorium, w którym można udostępnić szablony niestandardowe, które mogą być używane przez każdego z nich do tworzenia takich samych i izolowanych środowisk na potrzeby szkoleń. Można stosować zasady, aby zapewnić, że środowiska szkoleniowe są dostępne dla każdego z nich tylko wtedy, gdy ich potrzebują, i zawierają wystarczające zasoby, takie jak maszyny wirtualne — wymagane do szkolenia. Na koniec możesz łatwo udostępnić laboratorium z osobami, które mogą uzyskać do nich dostęp w jednym kliknięciem.

![Korzystanie z DevTest Labs na potrzeby szkoleń](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs spełnia następujące wymagania, które są wymagane do przeprowadzenia szkolenia w środowisku wirtualnym: 

* Program szkolony nie widzi maszyn wirtualnych utworzonych przez innego program szkolony
* Każda maszyna szkoleniowa powinna być taka sama
* Osoba szkolony może szybko zainicjować swoje środowiska szkoleniowe
* Kontroluj koszty, upewniając się, że osoba odpowiedzialna nie może uzyskać więcej maszyn wirtualnych niż potrzeba do szkolenia, a także zamknąć maszyny wirtualne, gdy ich nie używają
* Łatwo udostępniaj laboratorium szkoleniowe każdemu z nich
* Ponownie Użyj laboratorium szkoleniowego i ponownie

W tym artykule omówiono różne funkcje Azure DevTest Labs, których można użyć w celu spełnienia opisanych wcześniej wymagań szkoleniowych i szczegółowych czynności, które można wykonać, aby skonfigurować laboratorium do szkolenia.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementowanie szkolenia przy użyciu Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria są punktem początkowym w Azure DevTest Labs. Po utworzeniu laboratorium można wykonać zadania, takie jak dodawanie użytkowników (stażyści) do laboratorium, Ustawianie zasad kontroli kosztów, Definiowanie obrazów maszyn wirtualnych, które mogą szybko tworzyć i nie tylko.   
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak utworzyć laboratorium w Azure DevTest Labs w Azure Portal. |
2. **Twórz szkoleniowe maszyny wirtualne w kilka minut przy użyciu gotowych obrazów z portalu Marketplace i obrazów niestandardowych** 
   
    Możesz wybrać gotowe obrazy z wielu obrazów w portalu Azure Marketplace i udostępnić je dla osoby szkolonych w laboratorium. Jeśli gotowe obrazy nie spełniają Twoich wymagań, możesz utworzyć niestandardowy obraz, tworząc maszynę wirtualną laboratorium przy użyciu gotowego obrazu z witryny Azure Marketplace, instalując wszystkie oprogramowanie potrzebne do szkolenia i zapisując maszynę wirtualną jako obraz niestandardowy w laboratorium. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie obrazów z witryny Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak można zezwolić na obrazy w portalu Azure Marketplace; Udostępnianie do wybrania tylko dla obrazów, które mają być przeznaczone do szkoleń. |
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Tworzenie obrazu niestandardowego przez wstępne zainstalowanie oprogramowania potrzebnego do szkolenia, dzięki któremu program szkolony może szybko utworzyć maszynę wirtualną przy użyciu obrazu niestandardowego. |
3. **Tworzenie szablonów do wielokrotnego użytku dla maszyn szkoleniowych** 
   
    Formuła w Azure DevTest Labs jest listą domyślnych wartości właściwości używanych do tworzenia maszyny wirtualnej. Można utworzyć formułę w laboratorium przez wybranie obrazu, rozmiaru maszyny wirtualnej (kombinacji procesora i pamięci RAM) oraz sieci wirtualnej. Każdy praktykant może zobaczyć formułę w laboratorium i użyć jej do utworzenia maszyny wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami DevTest Labs w celu tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formułę, wybierając obraz, rozmiar maszyny wirtualnej (kombinację procesora CPU i pamięci RAM) oraz sieci wirtualnej. |
4. **Kontrolowanie kosztów**
   
    Azure DevTest Labs pozwala ustawić zasady w laboratorium, aby określić maksymalną liczbę maszyn wirtualnych, które mogą zostać utworzone przez praktykanta w laboratorium. 
   
    Jeśli przeprowadzasz szkolenie wielodniowe i chcesz zatrzymać wszystkie maszyny wirtualne w określonym czasie dnia, a następnie automatycznie uruchomić je ponownie, możesz to zrobić, ustawiając zasady automatycznego zamykania i automatycznego uruchamiania w laboratorium. 
   
    Na koniec po zakończeniu szkolenia możesz usunąć wszystkie maszyny wirtualne jednocześnie, uruchamiając jeden skrypt programu PowerShell. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontroluj koszty, ustawiając zasady w laboratorium. |
   | [Usuń wszystkie maszyny wirtualne laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Usuń wszystkie laboratoria w ramach jednej operacji, gdy szkolenie zostało zakończone. |
5. **Podziel się laboratorium z każdą praktykantem**
   
    Do laboratoriów można uzyskiwać dostęp bezpośrednio przy użyciu linku współużytkowanego przez Ciebie. Twoje osoby szkolony nie muszą nawet mieć konta platformy Azure, o ile mają [konto Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Program szkolony nie widzi maszyn wirtualnych utworzonych przez innych osób szkolonych.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodaj praktykanta do laboratorium w Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj Azure Portal, aby dodać szkolenie do laboratorium szkoleniowego. |
   | [Dodawanie funkcji stażyści do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Za pomocą programu PowerShell automatyzując Dodawanie nowych osób do laboratorium szkoleniowego. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, w jaki sposób można uzyskać bezpośredni dostęp do laboratorium za pośrednictwem hiperlinku. |
6. **Ponowne użycie laboratorium ponownie** 
   
    Możesz zautomatyzować tworzenie laboratorium, w tym ustawienia niestandardowe, tworząc szablon Menedżer zasobów i używając go do ponownego tworzenia identycznych laboratoriów. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu Menedżer zasobów](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Twórz laboratoria w Azure DevTest Labs przy użyciu szablonów Menedżer zasobów. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
