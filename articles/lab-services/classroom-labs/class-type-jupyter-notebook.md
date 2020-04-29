---
title: Skonfiguruj laboratorium, aby nauczyć się analizy danych za pomocą notesów języka Python i Jupyter | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko laboratoryjne do uczenia danych przy użyciu notesów Python i Jupyter.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 222f5647248d27c3bdfabd0feaeb66dd9f543652
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257729"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Konfigurowanie laboratorium do uczenia się danych za pomocą notesów Python i Jupyter

W tym artykule opisano sposób konfigurowania maszyny szablonu w usłudze laboratoryjnej z narzędziami wymaganymi do nauki uczniów, jak korzystać z [notesów Jupyter](http://jupyter-notebook.readthedocs.io).  Notesy Jupyter to projekt typu "open source", który umożliwia łatwe łączenie tekstu sformatowanego i wykonywalnego kodu źródłowego języka [Python](https://www.python.org/) na jednej kanwie o nazwie Notes.  Uruchamianie notesu skutkuje liniowym rekordem danych wejściowych i wyjściowych.  Mogą one obejmować tekst, tabele informacji, wykresy punktowe i wiele innych.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć pracę z tym laboratorium, musisz zacząć korzystać z subskrypcji platformy Azure i konta laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) . Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów z witryny Marketplace, zobacz [Określanie obrazów z portalu Marketplace dostępnych dla twórców laboratorium](specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
| Obraz witryny Marketplace | Włącz obraz [Data Science Virtual Machine-systemu Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) do użycia w ramach konta laboratorium. |

>[!TIP]
>Ten artykuł koncentruje się na konfigurowaniu komputera z szablonem, który korzysta z systemu operacyjnego Windows Server.  Istnieje również możliwość skonfigurowania klasy analizy danych z notesami Python i Jupyter przy użyciu obrazów [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) z portalu Azure Marketplace.

### <a name="lab-settings"></a>Ustawienia laboratorium

W poniższej tabeli przedstawiono ustawienia, które należy wykonać podczas konfigurowania laboratorium zajęć.  Aby uzyskać więcej informacji na temat tworzenia laboratorium klasy, zobacz [Samouczek dotyczący konfigurowania środowiska laboratoryjnego](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcje |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| Mały procesor GPU (COMPUTE). Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z obliczeń i sieci, takich jak sztuczna inteligencja i uczenie głębokie. |
|Obraz maszyny wirtualnej| Data Science Virtual Machine — Windows 2016|

## <a name="template-machine"></a>Komputer szablonu

Obraz [Data Science Virtual Machine-Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) zapewnia niezbędne platformy uczenia głębokiego i narzędzia wymagane dla tego typu klasy.  Obraz zawiera notesy Jupyter i Visual Studio Code.  [Notesy Jupyter](http://jupyter-notebook.readthedocs.io) to aplikacja sieci Web, która umożliwia analitykom danych wykonywanie nieprzetworzonych danych, uruchamianie obliczeń i wyświetlanie wyników wszystkich w tym samym środowisku.  Dla naszego komputera szablonu aplikacja sieci Web będzie uruchomiona lokalnie.  [Visual Studio Code](https://code.visualstudio.com/) to środowisko IDE, które zapewnia bogate interaktywne środowisko podczas pisania i testowania notesu.  Aby uzyskać więcej informacji, zobacz [Praca z notesami Jupyter w Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

Pozostałe zadanie konfigurowania klasy polega na udostępnianiu lokalnych notesów.  Instrukcje dotyczące korzystania z przykładów Azure Machine Learning można znaleźć w temacie [How to Configure a Environment with Jupyter Notess](../../machine-learning/how-to-configure-environment.md#jupyter).  Możesz również udostępniać własne Notesy na komputerze szablonu.  Notesy zostaną skopiowane do wszystkich komputerów uczniów po opublikowaniu szablonu.

## <a name="cost-estimate"></a>Oszacowanie kosztów

Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy.  Będziemy używać klasy 25 studentów.  Zaplanowana godzina klasy wynosi 20 godzin.  Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy.  Wybrany rozmiar maszyny wirtualnej to mały procesor GPU (COMPUTE), czyli 139 jednostek laboratorium.

Oto przykład możliwego oszacowania kosztów dla tej klasy:

25 studentów \* (20 zaplanowanych godzin + 10 godzin \* przydziału) 139 \* jednostek laboratoryjnych 0,01 USD za godzinę = 1042,5 USD

Więcej szczegółowych informacji na temat cen można znaleźć w temacie [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie

W tym artykule omówiono procedurę tworzenia laboratorium dla klasy notesów Jupyter. Możesz użyć podobnej konfiguracji dla innych klas uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)
