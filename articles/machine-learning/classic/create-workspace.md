---
title: 'ML Studio (klasyczny): Tworzenie obszaru roboczego — Azure'
description: Aby użyć Azure Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 700cc461563f145f58e02f7ed9a09b2899a4eb5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345826"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Tworzenie i udostępnianie obszaru roboczego Machine Learning Studio (klasyczny)

**dotyczy:** ![ tak ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ nie](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

Aby użyć Azure Machine Learning Studio (klasyczny), musisz mieć obszar roboczy Machine Learning Studio (klasyczny). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia i publikowania eksperymentów oraz zarządzania nimi.

## <a name="create-a-studio-classic-workspace"></a>Tworzenie obszaru roboczego programu Studio (klasycznego)

Aby otworzyć obszar roboczy w Machine Learning Studio (klasyczny), użytkownik musi być zalogowany na koncie Microsoft użytym do utworzenia obszaru roboczego lub uzyskać zaproszenia od właściciela, aby dołączyć do obszaru roboczego. Z Azure Portal można zarządzać obszarem roboczym, który obejmuje możliwość konfigurowania dostępu.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Aby zalogować się i utworzyć obszar roboczy Studio (klasyczny), musisz być administratorem subskrypcji platformy Azure. 
    >
    > 

2. Kliknij pozycję **+ Nowy**

3. W polu wyszukiwania wpisz **Machine Learning Studio (klasyczny) obszar roboczy** i wybierz pasujący element. Następnie wybierz pozycję **Utwórz** w dolnej części strony.

4. Wprowadź informacje o obszarze roboczym:

   - *Nazwa obszaru roboczego* może zawierać maksymalnie 260 znaków, a nie kończy się spacją. Nazwa nie może zawierać następujących znaków: `< > * % & : \ ? + /`
   - W przypadku wdrażania usług sieci Web z tego obszaru roboczego jest używana wybrana (lub utworzona) *Usługa sieci Web* oraz skojarzona z nią *warstwa cenowa* .

     ![Tworzenie nowego obszaru roboczego programu Studio (klasycznego)](./media/create-workspace/create-new-workspace.png)

5. Kliknij przycisk **Utwórz**.

   Machine Learning jest obecnie dostępna w ograniczonej liczbie regionów. Jeśli subskrypcja nie obejmuje jednego z tych regionów, może zostać wyświetlony komunikat o błędzie "nie masz żadnych subskrypcji w dozwolonych regionach".  Aby zażądać dodania regionu do subskrypcji, Utwórz nowe żądanie pomocy technicznej firmy Microsoft z Azure Portal, wybierz **rozliczenia** jako typ problemu i postępuj zgodnie z monitami, aby przesłać żądanie.


> [!NOTE]
> Machine Learning Studio (klasyczny) zależy od konta usługi Azure Storage, które podano w celu zapisania danych pośrednich podczas wykonywania przepływu pracy. Po utworzeniu obszaru roboczego, jeśli konto magazynu zostanie usunięte lub klucze dostępu zostaną zmienione, obszar roboczy przestanie działać, a wszystkie eksperymenty w tym obszarze roboczym zakończą się niepowodzeniem.
Jeśli przypadkowo usuniesz konto magazynu, Utwórz ponownie konto magazynu o tej samej nazwie w tym samym regionie co usunięte konto magazynu i ponownie zsynchronizuj klucz dostępu. Jeśli doszło do zmiany kluczy dostępu do konta magazynu, należy ponownie zsynchronizować klucze dostępu w obszarze roboczym, korzystając z witryny Azure Portal.

Po wdrożeniu obszaru roboczego można go otworzyć w Machine Learning Studio (klasyczny).

1. Przejdź do Machine Learning Studio (klasyczny) o [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Wybierz swój obszar roboczy w prawym górnym rogu.

    ![Wybór obszaru roboczego](./media/create-workspace/open-workspace.png)

3. Kliknij pozycję **Moje eksperymenty**.

    ![Otwarte eksperymenty](./media/create-workspace/my-experiments.png)

Aby uzyskać informacje na temat zarządzania obszarem roboczym programu Studio (klasycznego), zobacz [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznego)](manage-workspace.md).
Jeśli wystąpi problem podczas tworzenia obszaru roboczego, zobacz [Przewodnik rozwiązywania problemów: Tworzenie obszaru roboczego Machine Learning Studio (klasycznego) i nawiązywanie z nim połączenia](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Udostępnianie obszaru roboczego Azure Machine Learning Studio (klasyczny)
Po utworzeniu obszaru roboczego Machine Learning Studio (klasycznego) można zaprosić użytkowników do obszaru roboczego, aby udostępnić obszar roboczy i wszystkie jego eksperymenty, zestawy danych itp. Możesz dodać użytkowników w jednej z dwóch ról:

* **Użytkownik — użytkownik** obszaru roboczego może tworzyć, otwierać, modyfikować i usuwać eksperymenty, zestawy danych itp. w obszarze roboczym.
* **Właściciel** — właściciel może zapraszać i usuwać użytkowników w obszarze roboczym, a także do tego, co użytkownik może zrobić.

> [!NOTE]
> Konto administratora, które tworzy obszar roboczy, jest automatycznie dodawane do obszaru roboczego jako właściciel obszaru roboczego. Jednak inni administratorzy lub Użytkownicy w tej subskrypcji nie otrzymują automatycznie dostępu do obszaru roboczego — należy zaprosić je jawnie.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Aby udostępnić obszar roboczy Studio (klasyczny)

1. Zaloguj się do Machine Learning Studio (klasyczny) o [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. W lewym panelu kliknij pozycję **Ustawienia** .

3. Kliknij kartę **Użytkownicy** .

4. Kliknij pozycję **Zaproś więcej użytkowników** u dołu strony

    ![Ustawienia programu Studio](./media/create-workspace/settings.png)

5. Wprowadź co najmniej jeden adres e-mail. Użytkownicy muszą mieć prawidłowy konto Microsoft lub konto organizacyjne (z Azure Active Directory).

6. Wybierz, czy chcesz dodać użytkowników jako właściciela, czy użytkownika.

7. Kliknij przycisk znacznik wyboru **OK** .

Każdy dodawany użytkownik otrzyma wiadomość e-mail z instrukcjami dotyczącymi sposobu logowania się do udostępnionego obszaru roboczego.

> [!NOTE]
> Aby użytkownicy mogli wdrażać usługi sieci Web i zarządzać nimi w tym obszarze roboczym, muszą być współautorem lub administratorem w ramach subskrypcji platformy Azure. 

## <a name="troubleshoot-storage-accounts"></a>Rozwiązywanie problemów z kontami magazynu


Usługa Machine Learning wymaga konta magazynu do przechowywania danych. Możesz użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu podczas tworzenia nowego obszaru roboczego Machine Learning Studio (klasycznego) (Jeśli masz przydział do utworzenia nowego konta magazynu).

Po utworzeniu nowego obszaru roboczego Machine Learning Studio (klasyczny) możesz zalogować się do Machine Learning Studio (klasyczny) przy użyciu konto Microsoft użytego do utworzenia obszaru roboczego. Jeśli zostanie wyświetlony komunikat o błędzie "nie znaleziono obszaru roboczego" (podobnego do poniższego zrzutu ekranu), wykonaj poniższe kroki, aby usunąć pliki cookie przeglądarki.

![Nie znaleziono obszaru roboczego](media/troubleshooting-creating-ml-workspace/screen3.png)

**Aby usunąć pliki cookie przeglądarki**

1. Jeśli używasz programu Internet Explorer, kliknij przycisk **Narzędzia** w prawym górnym rogu i wybierz pozycję **Opcje internetowe**.  

   ![Opcje internetowe](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na karcie **Ogólne** kliknij przycisk **Usuń.**

   ![Karta Ogólne](media/troubleshooting-creating-ml-workspace/screen5.png)

3. W oknie dialogowym **usuwanie historii przeglądania** upewnij się, że **pliki cookie i dane witryny sieci Web** są zaznaczone, a następnie kliknij pozycję **Usuń**.

   ![Usuwanie plików cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po usunięciu plików cookie ponownie uruchom przeglądarkę, a następnie przejdź do strony [Microsoft Azure Machine Learning Studio (klasyczne)](https://studio.azureml.net) . Gdy zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź tę samą konto Microsoft, która została użyta do utworzenia obszaru roboczego.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania obszarem roboczym, zobacz [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznego)](manage-workspace.md).
