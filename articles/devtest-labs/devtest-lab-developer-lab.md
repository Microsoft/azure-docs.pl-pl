---
title: Użyj Azure DevTest Labs dla deweloperów | Microsoft Docs
description: Dowiedz się więcej o funkcjach Azure DevTest Labs, których można użyć do spełnienia wymagań deweloperskich i szczegółowych czynności, które można wykonać, aby skonfigurować laboratorium.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4f195f843e30ad1a56bad7d5b9ca9e73888783b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89296247"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Użyj Azure DevTest Labs dla deweloperów
Usługi Azure DevTest Labs można używać do wdrażania wielu kluczowych scenariuszy, ale jeden z podstawowych scenariuszy obejmuje używanie usługi DevTest Labs do hostowania maszyn deweloperskich dla deweloperów. W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Deweloperzy mogą szybko inicjować obsługę swoich maszyn programistycznych na żądanie.
- Deweloperzy mogą łatwo dostosowywać swoje maszyny deweloperskie w miarę potrzeb.
- Administratorzy mogą kontrolować koszty, upewniając się, że:
  - Deweloperzy nie mogą uzyskać więcej maszyn wirtualnych niż potrzeba do programowania.
  - Maszyny wirtualne są zamykane, gdy nie są używane. 

![Korzystanie z DevTest Labs na potrzeby szkoleń](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

W tym artykule omówiono różne funkcje Azure DevTest Labs, których można użyć do spełnienia wymagań deweloperskich i szczegółowych czynności, które można wykonać w celu skonfigurowania laboratorium.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementowanie środowisk deweloperskich za pomocą Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria są punktem początkowym w Azure DevTest Labs. Po utworzeniu laboratorium można wykonać zadania, takie jak dodawanie użytkowników (deweloperów) do laboratorium, Ustawianie zasad w celu kontrolowania kosztów, definiowania obrazów maszyn wirtualnych, które mogą szybko tworzyć i nie tylko.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium w usłudze Azure DevTest Labs](devtest-lab-create-lab.md) |Dowiedz się, jak utworzyć laboratorium w Azure DevTest Labs w Azure Portal. |
2. **Tworzenie maszyn wirtualnych w kilka minut przy użyciu gotowych obrazów z portalu Marketplace i obrazów niestandardowych** 
   
    Możesz wybrać gotowe obrazy z wielu obrazów w portalu Azure Marketplace i udostępnić je w laboratorium. Jeśli gotowe obrazy nie spełniają Twoich wymagań, możesz utworzyć niestandardowy obraz, tworząc maszynę wirtualną laboratorium przy użyciu gotowego obrazu z witryny Azure Marketplace, instalując wszystkie wymagane oprogramowanie i zapisując maszynę wirtualną jako obraz niestandardowy w laboratorium.

    Jeśli będziesz używać obrazów niestandardowych, rozważ użycie fabryki obrazu do tworzenia i dystrybuowania obrazów. Fabryka obrazów to rozwiązanie typu "Konfiguracja jako kod", które regularnie kompiluje i dystrybuuje skonfigurowane obrazy automatycznie. Pozwala to zaoszczędzić czas wymagany do ręcznego skonfigurowania systemu po utworzeniu maszyny wirtualnej przy użyciu podstawowego systemu operacyjnego.
  
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie obrazów z witryny Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, w jaki sposób można zezwolić na tworzenie obrazów w portalu Azure Marketplace, a dostępne do wyboru obrazy przeznaczone dla deweloperów.|
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Utwórz niestandardowy obraz, instalując wstępnie wymagane oprogramowanie, dzięki czemu deweloperzy mogą szybko utworzyć maszynę wirtualną przy użyciu obrazu niestandardowego.|
   | [Informacje o fabryce obrazu](./devtest-lab-faq.md#blog-post) |Obejrzyj wideo, w którym opisano sposób konfigurowania fabryki obrazów i korzystania z niej.|

3. **Tworzenie szablonów do wielokrotnego użytku dla komputerów deweloperów** 
   
    Formuła w Azure DevTest Labs jest listą domyślnych wartości właściwości używanych do tworzenia maszyny wirtualnej. Można utworzyć formułę w laboratorium przez wybranie obrazu, rozmiaru maszyny wirtualnej (kombinacji procesora i pamięci RAM) oraz sieci wirtualnej. Każdy deweloper może zobaczyć formułę w laboratorium i użyć jej do utworzenia maszyny wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami DevTest Labs w celu tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formułę, wybierając obraz, rozmiar maszyny wirtualnej (kombinację procesora CPU i pamięci RAM) oraz sieci wirtualnej.|

4. **Utwórz artefakty umożliwiające elastyczne dostosowywanie maszyny wirtualnej**

   Artefakty służą do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi maszyny wirtualnej. Artefakty mogą być następujące:

   - Narzędzia, które chcesz zainstalować na maszynie wirtualnej, takie jak agenci, programu Fiddler i Visual Studio.
   - Akcje, które mają zostać uruchomione na maszynie wirtualnej, takie jak klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefaktów jest już dostępnych jako gotowe do użycia. Możesz utworzyć własne artefakty niestandardowe, jeśli potrzebujesz większej dostosowania do konkretnych potrzeb.

   Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs](devtest-lab-artifact-author.md) |Utwórz własne artefakty niestandardowe dla maszyn wirtualnych w laboratorium.|
   | [Dodawanie repozytorium git do przechowywania niestandardowych artefaktów i szablonów Azure Resource Manager do użycia w programie Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak przechowywać niestandardowe artefakty w swoim prywatnym repozytorium git.|

5. **Kontrolowanie kosztów**
   
    Azure DevTest Labs pozwala ustawić zasady w laboratorium, aby określić maksymalną liczbę maszyn wirtualnych, które mogą zostać utworzone przez dewelopera w laboratorium. 
   
    Jeśli Twój zespół deweloperów ma ustawiony harmonogram pracy i chcesz zatrzymać wszystkie maszyny wirtualne w określonym czasie dnia, a następnie automatycznie uruchomić je ponownie, można to zrobić, ustawiając zasady automatycznego zamykania i automatycznego uruchamiania w laboratorium. 
   
    Na koniec po zakończeniu opracowywania aplikacji można usunąć wszystkie maszyny wirtualne jednocześnie, uruchamiając jeden skrypt programu PowerShell. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontroluj koszty, ustawiając zasady w laboratorium. |
   | [Usuń wszystkie maszyny wirtualne laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Usuń wszystkie laboratoria w jednej operacji po zakończeniu opracowywania.|

1. **Dodawanie sieci wirtualnej do maszyny wirtualnej** 
   
    Podczas tworzenia laboratorium DevTest Labs tworzy nową sieć wirtualną (VNET). Jeśli skonfigurowano własną sieć wirtualną — na przykład przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja-lokacja — możesz dodać tę sieć wirtualną do ustawień sieci wirtualnej laboratorium, aby była dostępna podczas tworzenia maszyn wirtualnych.

    Ponadto jest dostępny artefakt przyłączania do domeny Azure Active Directory, który zostanie przyłączony do domeny podczas tworzenia maszyny wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w Azure DevTest Labs](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieć wirtualną w Azure DevTest Labs przy użyciu Azure Portal.|

6. **Udostępnij laboratorium dla każdego dewelopera**
   
    Do laboratoriów można uzyskiwać dostęp bezpośrednio przy użyciu linku udostępnianego deweloperom. Nie muszą oni nawet mieć konta platformy Azure, o ile ma [konto Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Deweloperzy nie mogą zobaczyć maszyn wirtualnych utworzonych przez innych deweloperów.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie dewelopera do laboratorium w Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj Azure Portal, aby dodać deweloperów do laboratorium.|
   | [Dodawanie deweloperów do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Za pomocą programu PowerShell Automatyzuj Dodawanie deweloperów do laboratorium. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, w jaki sposób deweloperzy mogą bezpośrednio uzyskiwać dostęp do laboratorium za pośrednictwem hiperlinku.|

7. **Automatyzowanie tworzenia laboratorium dla większej liczby zespołów** 
   
    Możesz zautomatyzować tworzenie laboratorium, w tym ustawienia niestandardowe, tworząc szablon Menedżer zasobów i używając go do ponownego tworzenia identycznych laboratoriów. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu Menedżer zasobów](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Twórz laboratoria w Azure DevTest Labs przy użyciu szablonów Menedżer zasobów. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
