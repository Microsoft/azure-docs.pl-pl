---
title: Skonfiguruj laboratorium do uczenia się analizy danych Big Data za pomocą Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium do uczenia się analizy danych Big Data przy użyciu wdrożenia platformy Docker Hortonworks Data Platform (HDP).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659900"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Konfigurowanie laboratorium na potrzeby analizy danych Big Data przy użyciu platformy Docker HortonWorks Data Platform

W tym artykule opisano sposób konfigurowania laboratorium do uczenia klasy analizy danych Big Data.  Z tego typu klasy studenci mogą dowiedzieć się, jak obsługiwać duże ilości danych i stosować algorytmy uczenia maszynowego i statystycznego w celu uzyskania szczegółowych informacji.  Najważniejszym celem dla studentów jest Nauka korzystania z narzędzi analitycznych danych, takich jak [pakiet oprogramowania typu open source Apache Hadoop](https://hadoop.apache.org/) , który oferuje narzędzia do przechowywania i przetwarzania danych Big Data oraz zarządzania nimi.

W tym laboratorium studenci będą używać popularnej komercyjnej wersji usługi Hadoop dostarczonej przez [Cloudera](https://www.cloudera.com/)o nazwie [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html).  W każdym przypadku studenci będą korzystać z [HDP piaskownicy 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) , która jest uproszczoną, łatwą w użyciu wersją platformy, która jest bezpłatna i przeznaczona do uczenia się i eksperymentowania.  Chociaż ta klasa może korzystać z maszyn wirtualnych z systemem Windows lub Linux i wdrożonych w nim piaskownicy HDP, w tym artykule przedstawiono sposób korzystania z systemu Windows.

Innym interesującym aspektem tego laboratorium jest wdrożenie piaskownicy HDP na maszynach wirtualnych laboratorium przy użyciu kontenerów [platformy Docker](https://www.docker.com/) .  Każdy kontener platformy Docker zapewnia własne środowisko izolowane dla aplikacji oprogramowania do uruchomienia.  W przypadku kontenerów platformy Docker, takich jak zagnieżdżone maszyny wirtualne, można łatwo wdrażać i uruchamiać różne aplikacje na podstawie obrazów kontenerów dostępnych w usłudze [Docker Hub](https://www.docker.com/products/docker-hub).  Skrypt wdrażania usługi Cloudera dla piaskownicy HDP automatycznie ściąga [obraz platformy Docker 3.0.1 HDP](https://hub.docker.com/r/hortonworks/sandbox-hdp) z usługi Docker Hub i uruchamia dwa kontenery Docker:
  - Piaskownica — HDP
  - Piaskownica — serwer proxy

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć pracę z tym laboratorium, musisz zacząć korzystać z subskrypcji platformy Azure i konta laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów z witryny Marketplace, zobacz [Określanie obrazów z portalu Marketplace dostępnych dla twórców laboratorium](./specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
|Obraz z witryny Marketplace| Włącz obraz systemu Windows 10 Pro do użycia w ramach konta laboratorium.|

### <a name="lab-settings"></a>Ustawienia laboratorium

W poniższej tabeli przedstawiono ustawienia, które należy wykonać podczas konfigurowania laboratorium zajęć.  Aby uzyskać więcej informacji na temat tworzenia laboratorium klasy, zobacz [Samouczek dotyczący konfigurowania środowiska laboratoryjnego](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcje |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| Średni (Wirtualizacja zagnieżdżona). Ten rozmiar maszyny wirtualnej jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy.  Ten rozmiar obsługuje również wirtualizację zagnieżdżoną.|  
|Obraz maszyny wirtualnej| Windows 10 Pro|

> [!NOTE] 
> Musimy używać średnika (Wirtualizacja zagnieżdżona), ponieważ wdrażanie piaskownicy HDP przy użyciu platformy Docker wymaga:
>   - Windows Hyper-V z wirtualizacją zagnieżdżoną
>   - Co najmniej 10 GB pamięci RAM

## <a name="template-machine-configuration"></a>Konfiguracja komputera szablonu

Aby skonfigurować maszynę szablonu, będziemy:
- Zainstaluj platformę Docker
- Wdróż piaskownicę HDP
- Automatyczne uruchamianie kontenerów platformy Docker za pomocą programu PowerShell i systemu Windows Harmonogram zadań

### <a name="install-docker"></a>Zainstaluj platformę Docker

Kroki opisane w tej sekcji bazują na [Cloudera instrukcji dotyczących wdrażania przy użyciu kontenerów platformy Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Aby można było używać kontenerów platformy Docker, należy najpierw zainstalować program Docker Desktop na maszynie wirtualnej szablonu:

1. Postępuj zgodnie z instrukcjami w [sekcji wymagania wstępne](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) , aby zainstalować [Docker for Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Upewnij się, że opcja **Użyj kontenerów systemu Windows zamiast opcji konfiguracja kontenerów z systemem Linux** nie jest zaznaczona.

1. Upewnij się, że są włączone **kontenery systemu Windows i funkcje funkcji Hyper-V** .
   ![Włącz lub wyłącz funkcje systemu Windows](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Postępuj zgodnie z instrukcjami w sekcji [pamięć dla systemu Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) , aby skonfigurować konfigurację pamięci platformy Docker.

    > [!WARNING]
    > Jeśli przypadkowo zaznaczono opcję **Użyj kontenerów systemu Windows zamiast kontenerów z systemem Linux** podczas instalowania platformy Docker, ustawienia konfiguracji pamięci nie będą widoczne.  Aby rozwiązać ten problem, możesz przełączyć się do korzystania z kontenerów systemu Linux, [klikając ikonę platformy Docker na pasku zadań systemu Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog). Po otwarciu menu platformy Docker wybierz pozycję **Przełącz do kontenerów systemu Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Wdróż piaskownicę HDP

W tej sekcji zostanie wdrożona piaskownica HDP, a następnie dostęp do piaskownicy HDP przy użyciu przeglądarki.

1. Upewnij się, że zainstalowano usługę [git bash](https://gitforwindows.org/) zgodnie z opisem w [sekcji wymagania wstępne](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) przewodnika, ponieważ jest to zalecane w przypadku wykonywania następnych kroków.

1. Korzystając z [przewodnika wdrażania i instalacji programu Cloudera dla platformy Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html), wykonaj kroki opisane w następujących sekcjach:
   
   -    Wdróż piaskownicę HDP
   -    Weryfikuj piaskownicę HDP

    > [!WARNING] 
    > Podczas pobierania najnowszego pliku ZIP dla HDP upewnij się, że *nie* Zapisano pliku. zip w ścieżce katalogu, która zawiera odstępy.

    > [!NOTE] 
    > Jeśli wystąpi wyjątek podczas wdrażania **nie udostępniono dysku**, należy udostępnić dysk C za pomocą platformy Docker, aby kontenery systemu Linux HDP mogły uzyskiwać dostęp do plików lokalnych w systemie Windows.  Aby rozwiązać ten problem, [kliknij ikonę platformy Docker na pasku zadań systemu Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) , aby otworzyć menu platformy Docker i wybrać pozycję **Ustawienia**.  Gdy zostanie otwarte okno dialogowe **Ustawienia platformy Docker** , wybierz pozycję **zasoby > udostępnianie plików** i sprawdź dysk **C** .  Następnie można powtórzyć kroki w celu wdrożenia piaskownicy HDP.

1. Po wdrożeniu i uruchomieniu kontenerów platformy Docker dla piaskownicy HDP możesz uzyskać dostęp do środowiska, uruchamiając przeglądarkę i postępując zgodnie z instrukcjami Cloudera, aby otworzyć [stronę powitalną piaskownicy](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) i uruchomić Pulpit nawigacyjny HDP.

    > [!NOTE] 
    > W tych instrukcjach przyjęto założenie, że najpierw zmapowano lokalny adres IP środowiska piaskownicy do sandbox-hdp.hortonworks.com w pliku hosta na maszynie wirtualnej szablonu.  Jeśli to mapowanie **nie** zostanie przedzielone, możesz uzyskać dostęp do strony powitalnej piaskownicy, przechodząc do obszaru `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Automatycznie uruchamiaj kontenery platformy Docker podczas logowania uczniów

Aby ułatwić korzystanie z funkcji dla studentów, użyjemy automatycznie skryptu programu PowerShell:
  - Uruchamia kontenery Docker piaskownicy HDP, gdy student rozpocznie pracę i nawiązuje połączenie z maszyną wirtualną laboratorium.
  - Uruchamia przeglądarkę i przechodzi do strony powitalnej piaskownicy.
Użyjemy również Harmonogram zadań systemu Windows, aby automatycznie uruchomić ten skrypt, gdy student zaloguje się do swojej maszyny wirtualnej.
Aby je skonfigurować, wykonaj następujące kroki: [skrypty analizy danych Big Data](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Oszacowanie kosztów

Jeśli chcesz oszacować koszt tego laboratorium, możesz użyć poniższego przykładu.

W przypadku klasy 25 studentów z upływem 20 godzin planowanego czasu zajęć i 10 godzin przydziału dla prac domowych lub przydziałów, Cena za laboratorium to:
  - 25 studentów * (20 + 10) godz. * 55 jednostek laboratoryjnych * 0,01 USD za godzinę = 412,50 USD

Więcej szczegółowych informacji na temat cen można znaleźć w temacie [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie

W tym artykule opisano kroki niezbędne do utworzenia laboratorium dla klasy analizy danych Big Data, która korzysta z platformy danych Hortonworks wdrożonej z platformą Docker.  Konfiguracja tego typu klasy może być używana dla podobnych klas analizy danych.  Ta konfiguracja może być również stosowana do innych typów klas, które używają platformy Docker do wdrożenia.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)