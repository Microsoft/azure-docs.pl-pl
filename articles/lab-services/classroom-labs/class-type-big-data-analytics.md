---
title: Skonfiguruj laboratorium, aby uczyć analizy dużych zbiorów danych za pomocą usług Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium do nauczania analizy dużych zbiorów danych przy użyciu wdrożenia platformy Docker w platformie danych Hortonworks (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538780"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Konfigurowanie laboratorium do analizy dużych zbiorów danych przy użyciu wdrożenia platformy Docker w platformie danych HortonWorks

W tym artykule pokazano, jak skonfigurować laboratorium, aby uczyć klasy analizy dużych zbiorów danych.  W przypadku tego typu zajęć uczniowie uczą się obsługi dużych ilości danych i stosują algorytmy uczenia maszynowego i statystycznego w celu uzyskania szczegółowych informacji o danych.  Kluczowym celem dla studentów jest nauczenie się korzystania z narzędzi do analizy danych, takich jak [pakiet oprogramowania open source Apache Hadoop,](https://hadoop.apache.org/) który zapewnia narzędzia do przechowywania, zarządzania i przetwarzania dużych zbiorów danych.

W tym laboratorium studenci będą korzystać z popularnej komercyjnej wersji Hadoop dostarczonej przez [Cloudera](https://www.cloudera.com/), zwanej [Hortonworks Data Platform (HDP).](https://www.cloudera.com/products/hdp.html)  W szczególności uczniowie będą korzystać z [HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) która jest uproszczoną, łatwą w użyciu wersją platformy, która jest bezpłatna i przeznaczona do nauki i eksperymentowania.  Chociaż ta klasa może używać maszyn wirtualnych systemu Windows lub Linux (VM) z wdrożoną piaskownicą HDP, w tym artykule pokazano, jak używać systemu Windows.

Innym interesującym aspektem tego laboratorium jest wdrożenie piaskownicy HDP na maszynach wirtualnych laboratorium przy użyciu kontenerów [platformy Docker.](https://www.docker.com/)  Każdy kontener platformy Docker udostępnia własne środowisko izolowane dla aplikacji do uruchamiania wewnątrz.  Koncepcyjnie kontenery platformy Docker są jak zagnieżdżone maszyny wirtualne i mogą być używane do łatwego wdrażania i uruchamiania szerokiej gamy aplikacji opartych na obrazach kontenerów dostarczanych w [centrum platformy Docker Hub.](https://www.docker.com/products/docker-hub)  Skrypt wdrażania cloudera dla HDP Sandbox automatycznie pobiera obraz platformy [dokowej HDP Sandbox 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) z usługi Docker Hub i uruchamia dwa kontenery platformy Docker:
  - piaskownicy hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure i konta laboratorium, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Można również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów portalu Marketplace, zobacz [Określanie obrazów portalu Marketplace dostępnych dla twórców laboratoriów.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
|Obraz portalu Marketplace| Włącz obraz systemu Windows 10 Pro do użycia na koncie laboratoryjnym.|

### <a name="lab-settings"></a>Ustawienia laboratorium

Podczas konfigurowania laboratorium w klasie należy używać ustawień w poniższej tabeli.  Aby uzyskać więcej informacji na temat tworzenia laboratorium w klasie, zobacz [konfigurowanie samouczka laboratorium w klasie](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcja |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| Medium (wirtualizacja zagnieżdżona). Ten rozmiar maszyny Wirtualnej najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy.  Ten rozmiar obsługuje również zagnieżdżoną wirtualizację.|  
|Obraz maszyny wirtualnej| Windows 10 Pro|

> [!NOTE] 
> Musimy używać medium (wirtualizacji zagnieżdżonej), ponieważ wdrożenie piaskownicy HDP przy użyciu platformy Docker wymaga:
>   - Funkcja Hyper-V systemu Windows z zagnieżdżoną wirtualizacją
>   - Co najmniej 10 GB pamięci RAM

## <a name="template-machine-configuration"></a>Konfiguracja maszyny szablonu

Aby skonfigurować maszynę szablonową,:
- Zainstaluj platformę Docker
- Wdrażanie piaskownicy HDP
- Automatyczne uruchamianie kontenerów platformy Docker za pomocą programu PowerShell i Harmonogramu zadań systemu Windows

### <a name="install-docker"></a>Zainstaluj platformę Docker

Kroki opisane w tej sekcji są oparte na [instrukcjach cloudera dotyczących wdrażania za pomocą kontenerów platformy Docker.](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html) 

Aby korzystać z kontenerów platformy Docker, należy najpierw zainstalować pulpit platformy Docker na szablonie maszyny Wirtualnej:

1. Postępuj zgodnie z [instrukcjami w sekcji Wymagania wstępne,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) aby zainstalować [platformę Docker dla systemu Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Upewnij się, że **opcja konfiguracji Użyj kontenerów systemu Windows zamiast kontenerów systemu Linux** pozostanie niezaznaczona.

1. Upewnij się, że **kontenery systemu Windows i funkcje funkcji funkcji Hyper-V** są włączone.
   ![Włącz lub wyłącz funkcje systemu Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Wykonaj kroki opisane w sekcji [Pamięć dla systemu Windows,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) aby skonfigurować konfigurację pamięci platformy Docker.

    > [!WARNING]
    > Jeśli przypadkowo zaznaczysz opcję **Użyj kontenerów systemu Windows zamiast kontenerów systemu Linux** podczas instalowania platformy Docker, nie zostaną wyświetlone ustawienia konfiguracji pamięci.  Aby rozwiązać ten problem, możesz przełączyć się na korzystanie z kontenerów Linuksa, [klikając ikonę platformy Docker w zasobniku systemowym Windows;](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) po otwarciu menu Pulpit platformy Docker wybierz pozycję **Przełącz na kontenery systemu Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Wdrażanie piaskownicy HDP

W tej sekcji wdrożysz piaskownicę HDP, a następnie uzyskisz dostęp do piaskownicy HDP za pomocą przeglądarki.

1. Upewnij się, że zainstalowano [Git Bash,](https://gitforwindows.org/) jak wymieniono w [sekcji Wymagania wstępne](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) przewodnika, ponieważ jest to zalecane do wykonania kolejnych kroków.

1. Korzystając z [przewodnika wdrażania i instalacji cloudera dla platformy Docker,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)wykonaj kroki w następujących sekcjach:
   
   -    Wdrażanie piaskownicy HDP
   -    Weryfikowanie piaskownicy HDP

    > [!WARNING] 
    > Po pobraniu najnowszego pliku zip dla hdp upewnij się, że plik .zip *nie* jest zapisywany w ścieżce katalogu zawierającej odstępy.

    > [!NOTE] 
    > Jeśli podczas wdrażania zostanie wyświetlony wyjątek z informacją, że **dysk nie został udostępniony,** musisz udostępnić dysk C do platformy Docker, aby kontenery systemu Linux firmy HDP mogły uzyskiwać dostęp do lokalnych plików systemu Windows.  Aby rozwiązać ten problem, [kliknij ikonę Platformy Docker w zasobniku systemowym systemu Windows,](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) aby otworzyć menu Pulpit platformy Docker i wybrać pozycję **Ustawienia**.  Po otwarciu okna dialogowego **Ustawienia platformy Docker** wybierz pozycję Zasoby > udostępnianie **plików** i sprawdź dysk **C.**  Następnie można powtórzyć kroki, aby wdrożyć piaskownicę HDP.

1. Po wdrożeniu i uruchomieniu kontenerów platformy Docker dla piaskownicy HDP można uzyskać dostęp do środowiska, uruchamiając przeglądarkę i postępując zgodnie z instrukcjami Cloudera dotyczącymi otwierania [strony powitalnej piaskownicy](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) i uruchamiania pulpitu nawigacyjnego HDP.

    > [!NOTE] 
    > W tych instrukcjach przyjęto założenie, że najpierw zamapowany został lokalny adres IP środowiska piaskownicy na sandbox-hdp.hortonworks.com w pliku hosta na szablonie maszyny Wirtualnej.  Jeśli **nie** wykonasz tego mapowania, możesz uzyskać dostęp do [http://localhost:8080](http://localhost:8080)strony powitalnej piaskownicy, przechodząc do .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Automatyczne uruchamianie kontenerów platformy Docker po zalogowaniu się uczniów

Aby zapewnić uczniom łatwe w użyciu środowisko, użyjemy skryptu programu PowerShell, który automatycznie:
  - Uruchamia kontenery platformy dokowania piaskownicy HDP, gdy uczeń uruchamia i łączy się z maszyną wirtualną w laboratorium.
  - Uruchamia przeglądarkę i przechodzi do strony powitalnej piaskownicy.
Użyjemy również Harmonogram zadań systemu Windows, aby automatycznie uruchomić ten skrypt, gdy uczeń loguje się do swojej maszyny Wirtualnej.
Aby to skonfigurować, wykonaj następujące kroki: [Skrypty analizy dużych zbiorów danych](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Szacowanie kosztów

Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć poniższego przykładu.

Dla klasy 25 uczniów z 20 godzin zaplanowanego czasu zajęć i 10 godzin przydziału na pracę domową lub zadania, cena za laboratorium będzie:
  - 25 studentów * (20 + 10) godzin * 55 jednostek laboratoryjnych * 0.01 USD za godzinę = 412.50 USD

Więcej informacji na temat cen można znaleźć w [cenniku usług Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie

W tym artykule opracowywano kroki niezbędne do utworzenia laboratorium dla klasy analizy dużych zbiorów danych, która korzysta z platformy danych Hortonworks wdrożonej za pomocą platformy Docker.  Konfiguracja dla tego typu klasy może służyć do podobnych klas analizy danych.  Ta konfiguracja może mieć również zastosowanie do innych typów klas, które używają platformy Docker do wdrażania.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne dla konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji wiadomości e-mail do studentów](how-to-configure-student-usage.md#send-invitations-to-users)
