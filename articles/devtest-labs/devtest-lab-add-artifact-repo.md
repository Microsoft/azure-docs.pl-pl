---
title: Dodawanie repozytorium git do laboratorium w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak dodać repozytorium usługi GitHub lub Azure DevOps Services dla niestandardowego źródła artefaktów w programie Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483146"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Dodawanie repozytorium git do przechowywania niestandardowych artefaktów i szablonów Menedżer zasobów

Możesz [tworzyć niestandardowe artefakty](devtest-lab-artifact-author.md) dla maszyn wirtualnych w laboratorium lub [używać szablonów Azure Resource Manager do tworzenia niestandardowego środowiska testowego](devtest-lab-create-environment-from-arm.md). Należy dodać prywatne repozytorium git dla artefaktów lub Menedżer zasobów szablonów tworzonych przez zespół. Repozytorium może być hostowane w usłudze [GitHub](https://github.com) lub na [Azure DevOps Services](https://visualstudio.com).

Oferujemy [repozytorium GitHub artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , które można wdrożyć jako-is lub można je dostosować do swoich laboratoriów. W przypadku dostosowywania lub tworzenia artefaktu nie można przechowywać artefaktu w repozytorium publicznym. Należy utworzyć własne repozytorium prywatne dla artefaktów niestandardowych i dla utworzonych artefaktów. 

Podczas tworzenia maszyny wirtualnej można zapisać szablon Menedżer zasobów, dostosować go w razie potrzeby, a następnie użyć go później w celu utworzenia większej liczby maszyn wirtualnych. Należy utworzyć własne prywatne repozytorium do przechowywania niestandardowych szablonów Menedżer zasobów.  

* Aby dowiedzieć się, jak utworzyć repozytorium GitHub, zobacz artykuł [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Aby dowiedzieć się, jak utworzyć projekt Azure DevOps Services, który ma repozytorium git, zobacz [Connect to Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Poniższy rysunek przedstawia przykład sposobu, w jaki repozytorium, które ma artefakty, może wyglądać w serwisie GitHub:  

![Przykładowe repozytorium artefaktów GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Pobierz informacje o repozytorium i poświadczenia
Aby dodać repozytorium do laboratorium, najpierw Pobierz informacje o kluczu z repozytorium. W poniższych sekcjach opisano, jak uzyskać wymagane informacje dotyczące repozytoriów hostowanych w serwisie GitHub lub Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Pobieranie adresu URL klonowania repozytorium GitHub i osobistego tokenu dostępu

1. Przejdź do strony głównej repozytorium GitHub, która zawiera definicje artefaktów lub Menedżer zasobów szablonów.
2. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
3. Aby skopiować adres URL do schowka, wybierz przycisk **adres URL klonowania https** . Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu usługi GitHub wybierz obraz profilu, a następnie wybierz pozycję **Ustawienia**.
5. W menu **Ustawienia osobiste** po lewej stronie wybierz pozycję **osobiste tokeny dostępu**.
6. Wybierz pozycję **Generuj nowy token**.
7. Na stronie **nowy osobisty token dostępu** w obszarze **Opis tokenu** wprowadź opis. Zaakceptuj domyślne elementy w obszarze **Wybierz zakresy**, a następnie wybierz pozycję **Generuj token**.
8. Zapisz wygenerowany token. Token jest używany później.
9. Zamknij witrynę GitHub.   
10. Przejdź do sekcji [łączenie laboratorium z repozytorium](#connect-your-lab-to-the-repository) .

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania Azure Repos i osobistego tokenu dostępu

1. Przejdź do strony głównej kolekcji zespołu (na przykład `https://contoso-web-team.visualstudio.com` ), a następnie wybierz projekt.
2. Na stronie głównej projektu wybierz pozycję **kod**.
3. Aby wyświetlić adres URL klonowania, na stronie **kod** projektu wybierz pozycję **Klonuj**.
4. Zapisz adres URL. Ten adres URL jest używany później.
5. Aby utworzyć osobisty token dostępu, w menu rozwijanym konto użytkownika wybierz pozycję **mój profil**.
6. Na stronie informacje o profilu wybierz pozycję **zabezpieczenia**.
7. Na karcie **zabezpieczenia** wybierz pozycję **Dodaj**.
8. Na stronie **Tworzenie osobistego tokenu dostępu** :
   1. Wprowadź **Opis** tokenu.
   2. Na liście **wygasanie** wybierz pozycję **180 dni**.
   3. Na liście **konta** wybierz pozycję **wszystkie dostępne konta**.
   4. Wybierz opcję **tylko do odczytu** .
   5. Wybierz pozycję **Utwórz token**.
9. Nowy token zostanie wyświetlony na liście **osobiste tokeny dostępu** . Wybierz pozycję **Kopiuj token**, a następnie Zapisz wartość tokenu do późniejszego użycia.
10. Przejdź do sekcji [łączenie laboratorium z repozytorium](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Łączenie laboratorium z repozytorium
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz pozycję **więcej usług**, a następnie wybierz pozycję **DevTest Labs** z listy usług.
3. Z listy laboratoriów wybierz laboratorium. 
4. Wybierz kolejno pozycje **Konfiguracja i repozytoria i zasady i**  >    >  **Dodaj**.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na drugiej stronie **repozytoria** podaj następujące informacje:
   1. **Nazwa**. Wprowadź nazwę repozytorium.
   2. **Adres Url git clone**. Wprowadź adres URL klonowania HTTPS usługi git, który został skopiowany wcześniej z witryny GitHub lub Azure DevOps Services.
   3. **Gałąź**. Aby uzyskać definicje, wprowadź gałąź.
   4. **Osobisty token dostępu**. Wprowadź osobisty token dostępu uzyskany wcześniej z witryny GitHub lub Azure DevOps Services.
   5. **Ścieżki folderów**. Wprowadź co najmniej jedną ścieżkę folderu względem adresu URL klonowania zawierającego artefakt lub Menedżer zasobów definicje szablonu. Po określeniu podkatalogu upewnij się, że w ścieżce folderu znajduje się ukośnik do przodu.

      ![Obszar repozytoriów](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz pozycję **Zapisz**.

### <a name="related-blog-posts"></a>Powiązane wpisy w blogu
* [Rozwiązywanie problemów z błędami artefaktów w DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Przyłączanie maszyny wirtualnej do istniejącej domeny Active Directory przy użyciu szablonu Menedżer zasobów w DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po utworzeniu prywatnego repozytorium git można wykonać jedną lub obie następujące czynności, w zależności od potrzeb:
* Przechowywanie [niestandardowych artefaktów](devtest-lab-artifact-author.md). Można ich użyć później do utworzenia nowych maszyn wirtualnych.
* [Tworzenie środowisk z obsługą wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów Menedżer zasobów](devtest-lab-create-environment-from-arm.md). Następnie można przechowywać szablony w swoim repozytorium prywatnym.

Podczas tworzenia maszyny wirtualnej można sprawdzić, czy artefakty lub szablony są dodawane do repozytorium git. Są one natychmiast dostępne na liście artefaktów lub szablonów. Nazwa repozytorium prywatnego jest wyświetlana w kolumnie, która określa źródło. 
