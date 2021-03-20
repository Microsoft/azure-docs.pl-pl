---
title: Używanie Azure DevTest Labs dla środowisk testowych i PaaS Microsoft Docs
description: Dowiedz się, jak używać Azure DevTest Labs na potrzeby scenariuszy środowiska testowego maszyn wirtualnych i PaaS.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 978071aa160098612a5effebfb30d5d58b777df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89299834"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Używanie Azure DevTest Labs dla środowisk testowych i PaaS

Usługi Azure DevTest Labs możesz używać do wdrażania wielu kluczowych scenariuszy, ale podstawowy scenariusz polega na używaniu usługi DevTest Labs do hostowania maszyn dla testerów. 

W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Testerzy mogą przetestować najnowszą wersję swoich aplikacji dzięki możliwości szybkiej aprowizacji środowisk systemów Windows i Linux przy użyciu szablonów wielokrotnego użytku i artefaktów.
- Testerzy mogą skalować w górę swoje testowane obciążenie, aprowizując wielu agentów testowych.
- Administratorzy mogą kontrolować koszty, upewniając się, że:
  - Testerzy nie mogą uzyskać więcej maszyn wirtualnych niż potrzebują.
  - Maszyny wirtualne są zamykane, gdy nie są używane.

![Korzystanie z DevTest Labs na potrzeby szkoleń](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ten artykuł zawiera informacje o różnych funkcjach Azure DevTest Labs używanych do spełnienia wymagań testera i szczegółowych czynności, które należy wykonać w celu skonfigurowania laboratorium.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementowanie środowisk testowych przy użyciu Azure DevTest Labs
1. **Tworzenie laboratorium** 
   
    Laboratoria są punktem początkowym w Azure DevTest Labs. Po utworzeniu laboratorium można wykonać zadania, takie jak dodawanie użytkowników (testerów) do laboratorium, Ustawianie zasad w celu kontrolowania kosztów, definiowania obrazów maszyn wirtualnych, które mogą szybko tworzyć i nie tylko.  
   
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
   | [Konfigurowanie obrazów z witryny Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Dowiedz się, jak można zezwolić na obrazy w portalu Azure Marketplace, udostępniając tylko wybrane obrazy dla testerów.|
   | [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) |Utwórz niestandardowy obraz, instalując wstępnie wymagane oprogramowanie, dzięki czemu testerzy mogą szybko utworzyć maszynę wirtualną przy użyciu obrazu niestandardowego.|
   | [Informacje o fabryce obrazu](./devtest-lab-faq.md#blog-post) |Obejrzyj wideo, w którym opisano sposób konfigurowania fabryki obrazów i korzystania z niej.|

3. **Tworzenie szablonów wielokrotnego użytku dla maszyn testowych** 
   
    Formuła w Azure DevTest Labs jest listą domyślnych wartości właściwości używanych do tworzenia maszyny wirtualnej. Można utworzyć formułę w laboratorium przez wybranie obrazu, rozmiaru maszyny wirtualnej (kombinacji procesora i pamięci RAM) oraz sieci wirtualnej. Każdy tester może zobaczyć formułę w laboratorium i użyć jej do utworzenia maszyny wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Zarządzanie formułami DevTest Labs w celu tworzenia maszyn wirtualnych](devtest-lab-manage-formulas.md) |Dowiedz się, jak utworzyć formułę, wybierając obraz, rozmiar maszyny wirtualnej (kombinację procesora CPU i pamięci RAM) oraz sieci wirtualnej.|

3. **Tworzenie środowisk testowych z obsługą wielu maszyn wirtualnych** 
   
    Za pomocą szablonów Azure Resource Manager można zdefiniować infrastrukturę i konfigurację rozwiązania platformy Azure oraz wielokrotnie wdrożyć wiele testowych maszyn wirtualnych w spójnym stanie.

    Zasoby PaaS platformy Azure mogą być obsługiwane w środowisku z szablonu Menedżer zasobów i pojawiają się w obszarze śledzenia kosztów. Jednak automatyczne zamknięcie maszyny wirtualnej nie dotyczy zasobów PaaS.

    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Dowiedz się, w jaki sposób można wdrożyć wiele maszyn wirtualnych w spójnym stanie dla środowiska testowego.|

4. **Utwórz artefakty umożliwiające elastyczne dostosowywanie maszyny wirtualnej**

   Artefakty służą do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi maszyny wirtualnej. Artefakty mogą być następujące:

   - Narzędzia, które chcesz zainstalować na maszynie wirtualnej, takie jak agenci, programu Fiddler i Visual Studio.
   - Akcje, które mają zostać uruchomione na maszynie wirtualnej, takie jak klonowanie repozytorium.
   - Aplikacje, które chcesz przetestować.

   Wiele artefaktów jest już dostępnych jako gotowe do użycia. Ale jeśli potrzebujesz więcej dostosowań dla konkretnych potrzeb, możesz utworzyć własne artefakty niestandardowe.

   Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs](devtest-lab-artifact-author.md) |Utwórz własne artefakty niestandardowe dla maszyn wirtualnych w laboratorium.|
   | [Dodawanie repozytorium git do przechowywania niestandardowych artefaktów i szablonów Azure Resource Manager do użycia w programie Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Dowiedz się, jak przechowywać niestandardowe artefakty w swoim prywatnym repozytorium git.|

5. **Kontrolowanie kosztów**
   
    Azure DevTest Labs pozwala ustawić zasady w laboratorium, aby określić maksymalną liczbę maszyn wirtualnych, które mogą zostać utworzone przez testera w laboratorium. 
   
    Jeśli zespół testowy ma harmonogram pracy i chcesz zatrzymać wszystkie maszyny wirtualne w określonym czasie dnia, a następnie automatycznie uruchomić je ponownie, można to zrobić, ustawiając zasady automatycznego zamykania i automatycznego uruchamiania w laboratorium. 
   
    Na koniec po zakończeniu opracowywania aplikacji można usunąć wszystkie maszyny wirtualne jednocześnie, uruchamiając jeden skrypt programu PowerShell. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) |Kontroluj koszty, ustawiając zasady w laboratorium. |
   | [Usuń wszystkie maszyny wirtualne laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Usuń wszystkie laboratoria w ramach jednej operacji, gdy testowanie zostało zakończone.|

1. **Dodawanie sieci wirtualnej do laboratorium** 
   
    Podczas tworzenia laboratorium DevTest Labs tworzy nową sieć wirtualną (VNET). Jeśli skonfigurowano własną sieć wirtualną — na przykład przy użyciu usługi ExpressRoute lub sieci VPN typu lokacja-lokacja — możesz dodać tę sieć wirtualną do ustawień sieci wirtualnej laboratorium, aby była dostępna podczas tworzenia maszyn wirtualnych.

    Ponadto istnieje dostępny Azure Active Directory artefaktu przyłączania do domeny, który przyłącza maszynę wirtualną do domeny podczas tworzenia maszyny wirtualnej. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Konfigurowanie sieci wirtualnej w Azure DevTest Labs](devtest-lab-configure-vnet.md) |Dowiedz się, jak skonfigurować sieć wirtualną w Azure DevTest Labs przy użyciu Azure Portal.|

6. **Udostępnij laboratorium każdemu testerowi**
   
    Do laboratoriów można uzyskać dostęp bezpośrednio przy użyciu linku współużytkowanego z testerami. Nie muszą oni nawet mieć konta platformy Azure, o ile ma [konto Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Testerzy nie mogą zobaczyć maszyn wirtualnych utworzonych przez innych testerów.  
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Dodawanie testera do laboratorium w Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Użyj Azure Portal, aby dodać testerów do laboratorium.|
   | [Dodawanie testerów do laboratorium przy użyciu skryptu programu PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Za pomocą programu PowerShell można zautomatyzować Dodawanie testerów do laboratorium. |
   | [Uzyskaj link do laboratorium](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Dowiedz się, w jaki sposób testerzy mogą bezpośrednio uzyskiwać dostęp do laboratorium za pośrednictwem hiperlinku.|

7. **Automatyzowanie tworzenia laboratorium dla większej liczby zespołów** 
   
    Możesz zautomatyzować tworzenie laboratorium, w tym ustawienia niestandardowe, tworząc szablon Menedżer zasobów i używając go do ponownego tworzenia identycznych laboratoriów. 
   
    Dowiedz się więcej, klikając linki w poniższej tabeli:
   
   | Zadanie | Omawiane zagadnienia |
   | --- | --- |
   | [Tworzenie laboratorium przy użyciu szablonu Menedżer zasobów](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Twórz laboratoria w Azure DevTest Labs przy użyciu szablonów Menedżer zasobów. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
