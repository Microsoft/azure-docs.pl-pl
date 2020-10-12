---
title: Profil użytkownika i identyfikator do użycia z podglądem Azure Notebooks
description: Tworzenie profilu użytkownika i identyfikatora użytkownika oraz zarządzanie nim przy użyciu Azure Notebooks, który jest częścią adresu URL udostępnionych notesów.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 9a1ff7f92faec21f537f068f0a33473700ddfed8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831356"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Twój profil i identyfikator użytkownika dla Azure Notebooks wersja zapoznawcza

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

W ramach zaawansowanego obszaru współpracy Azure Notebooks Twój profil użytkownika przedstawia publiczny obraz innym użytkownikom:

[![Strona profilu Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Identyfikator użytkownika jest częścią adresów URL używanych do udostępniania projektów i notesów. Na poniższej liście opisano różne wzorce adresów URL:

- `https://notebooks.azure.com/<user_id>`: Twoja strona Twojego profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Twoje projekty. Zobaczysz wszystkie projekty; inni użytkownicy widzą tylko Twoje projekty publiczne.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Pliki projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klonowanie określonych projektów.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Podgląd HTML określonego notesu lub pliku.

## <a name="your-user-id"></a>Twój identyfikator użytkownika

Po pierwszym zalogowaniu się do Azure Notebooks Twoje konto zostanie automatycznie przypisane do tymczasowego identyfikatora użytkownika, takiego jak "Anon-idr3ca". O ile użytkownik ma identyfikator użytkownika zaczynający się od ciągu "Anon-", Azure Notebooks prosi o zmianę przy każdym zalogowaniu:

![Monituj o utworzenie identyfikatora użytkownika podczas logowania do Azure Notebooks](media/accounts/create-user-id.png)

W polu Nazwa użytkownika tymczasowego pojawia się również polecenie **Konfiguruj identyfikator użytkownika** :

![Skonfiguruj polecenie identyfikatora użytkownika, które pojawia się, gdy używasz identyfikatora tymczasowego](media/accounts/configure-user-id-command.png)

Identyfikator użytkownika można również zmienić w dowolnym momencie na stronie Twojego profilu.

Identyfikator użytkownika musi składać się z czterech i szesnastu liter, cyfr i łączników. Nie są dozwolone żadne inne znaki, a identyfikator użytkownika nie może rozpoczynać się ani kończyć łącznikiem ani używać wielu łączników w wierszu. Ponieważ identyfikatory użytkowników są unikatowe dla wszystkich kont Azure Notebooks, może zostać wyświetlony komunikat "identyfikator użytkownika jest już w użyciu". (Komunikat jest również wyświetlany, jeśli próbujesz użyć znaku towarowego firmy Microsoft jako identyfikatora użytkownika). W takich przypadkach wybierz inny identyfikator użytkownika.

> [!Important]
> Zmiana identyfikatora unieważnia wszystkie adresy URL, które mogły zostać udostępnione przy użyciu poprzedniego identyfikatora. Identyfikator można zmienić na poprzedni identyfikator, aby ponownie sprawdzić poprawność linków. Możliwe jest jednak, aby inny użytkownik zatwierdził nieużywany identyfikator w międzyczasie.

## <a name="your-profile"></a>Twój profil

Twój profil składa się z publicznie dodanych informacji przy użyciu adresu URL `https://notebooks.azure.com/<user_id>` . Na stronie profil znajdują się także ostatnio używane projekty i wszystkie projekty oznaczona gwiazdkami.

Aby edytować profil, użyj polecenia **Edytuj informacje o profilu** na stronie Twojego profilu. Poniżej przedstawiono sekcje profilu:

| Sekcja | Zawartość |
| --- | --- |
| Zdjęcie profilu | Obraz wyświetlany na stronie Twojego profilu. |
| Informacje o koncie | Nazwa wyświetlana, identyfikator użytkownika i publiczne konto e-mail. Konto e-mail w tym miejscu daje innym użytkownikom średnią skontaktowanie się z Tobą i może się różnić od [konta](azure-notebooks-user-account.md) , którego używasz do logowania się do Azure Notebooks samego siebie. |
| Informacje o profilu | Twoje miejsce, firma, stanowisko, witryna sieci Web i Krótki opis siebie. |
| Profile społecznościowe | Identyfikatory usługi GItHub, Twitter i Facebook, jeśli chcesz je udostępnić. |
| Ustawienia prywatności | Program udostępnia dwa polecenia:<ul><li>**Eksportuj mój profil**: tworzy i pobiera plik *. zip* zawierający wszystkie informacje, które Azure Notebooks zapisywane w Twoim profilu, w tym zdjęcie, informacje o profilu i dzienniki zabezpieczeń.</li><li>**Usuń moje konto**: trwale usuwa wszystkie dane osobowe przechowywane w Azure Notebooks.</li></ul> |
| Włącz funkcje witryny | Pozwala kontrolować aspekty zachowania Azure Notebooks:<ul><li>**Ujednolicone frontony dla notesów**: umożliwiają szybsze uruchamianie notesu i lepszą trwałość.</li><li>**Domyślnie Uruchom program w JupyterLab**: domyślnie Azure Notebooks udostępnia prosty interfejs użytkownika, który jest odpowiedni dla większości użytkowników. JupyterLab zapewnia bogatszy, ale bardziej skomplikowany interfejs dla doświadczonych użytkowników.</li><li>**Witryna sieci Web vNext**: umożliwia korzystanie z nowoczesnego układu internetowego pokazanego w tej dokumentacji.</li></ul> |

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i uruchamianie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
