---
title: Skonfiguruj laboratorium, aby nauczyć się analizy danych za pomocą notesów języka Python i Jupyter | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko laboratoryjne do uczenia danych przy użyciu notesów Python i Jupyter.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: 4bbf4c9d4bc83b48b8ecc62946fa9bffa8af50bc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533524"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Konfigurowanie laboratorium do uczenia się danych za pomocą notesów Python i Jupyter
W tym artykule opisano, jak skonfigurować maszynę wirtualną szablonu (VM) w usłudze laboratoryjnej z narzędziami, które są konieczne do nauki uczniów, jak korzystać z [notesów Jupyter](http://jupyter-notebook.readthedocs.io/)oraz jak uczniowie mogą łączyć się z notesami na ich maszynach wirtualnych.

Notesy Jupyter to projekt typu "open source", który umożliwia łatwe łączenie tekstu sformatowanego i wykonywalnego kodu źródłowego języka Python na jednej kanwie o nazwie Notes. Uruchamianie notesu skutkuje liniowym rekordem danych wejściowych i wyjściowych. Mogą one obejmować tekst, tabele informacji, wykresy punktowe i wiele innych.

## <a name="set-up-the-lab"></a>Konfigurowanie laboratorium

### <a name="lab-configuration"></a>Konfiguracja laboratorium
Aby skonfigurować to laboratorium, musisz mieć dostęp do subskrypcji platformy Azure i konta laboratorium. Zapoznaj się z administratorem organizacji, aby sprawdzić, czy możesz uzyskać dostęp do istniejącej subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

Gdy masz subskrypcję platformy Azure, Utwórz nowe konto laboratorium w Azure Lab Services, postępując zgodnie z instrukcjami podanymi w samouczku: [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md). Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium
Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów z witryny Marketplace, zobacz [Określanie obrazów z portalu Marketplace dostępnych dla twórców laboratorium](specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
| Obraz witryny Marketplace | W ramach konta laboratorium należy włączyć jeden z obrazów portalu Azure Marketplace oparty na potrzebach systemu operacyjnego: <br/><ul><li>Data Science Virtual Machine — Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18,04</li></ul> |

> [!NOTE]
> W tym artykule są wykorzystywane obrazy maszyn wirtualnych do analizy danych dostępne w portalu Azure Marketplace, ponieważ są one wstępnie skonfigurowane przy użyciu Jupyter Notebook. Obrazy te zawierają jednak również wiele innych narzędzi programistycznych i modelowania do nauki o danych. Jeśli nie chcesz, aby te dodatkowe narzędzia i chcą mieć uproszczoną konfigurację z tylko notesami Jupyter, Utwórz niestandardowy obraz maszyny wirtualnej. Przykład [instalacji JupyperHub na platformie Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Po utworzeniu obrazu niestandardowego można przekazać go do galerii obrazów udostępnionych, aby użyć obrazu w Azure Lab Services. Dowiedz się więcej o [korzystaniu z galerii obrazów udostępnionych w Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Ustawienia laboratorium
Skonfiguruj ustawienia **rozmiaru maszyny wirtualnej** i **obrazu maszyny wirtualnej** , jak pokazano w poniższej tabeli podczas konfigurowania laboratorium zajęć. Aby uzyskać instrukcje dotyczące tworzenia laboratorium klasy, zobacz [Konfigurowanie laboratorium zajęć](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcje |
| ------------ | ------------------ | 
| Rozmiar maszyny wirtualnej | <p>Wybrany tutaj rozmiar zależy od obciążenia, które chcesz uruchomić:</p><ul><li>Małe lub średnie — dobre dla podstawowej konfiguracji umożliwiającej dostęp do notesów Jupyter</li><li>Mały procesor GPU (COMPUTE) — najlepiej dopasowany do intensywnie korzystających z obliczeń aplikacji i intensywnie korzystających z sieci, takich jak sztuczna inteligencja</li></ul> | 
| Obraz maszyny wirtualnej | <p>Wybierz jeden z następujących obrazów w zależności od potrzeb systemu operacyjnego:</p><ul><li>[Data Science Virtual Machine — Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Szablonowa maszyna wirtualna
Po utworzeniu laboratorium zostanie utworzona maszyna wirtualna z szablonem na podstawie wybranego rozmiaru maszyny wirtualnej. Można skonfigurować maszynę wirtualną szablonu przy użyciu wszystkich elementów, które mają zostać udostępnione uczniom dla tej klasy. Aby dowiedzieć się więcej, zobacz [jak zarządzać maszyną wirtualną szablonu](how-to-create-manage-template.md). 

Domyślnie obrazy Data Science VM są dostarczane z wieloma platformami i narzędziami do nauki o danych, które są wymagane dla tego typu klasy. Na przykład obrazy obejmują:

- [Notesy Jupyter](http://jupyter-notebook.readthedocs.io/): aplikacja sieci Web, która umożliwia analitykom danych wykonywanie nieprzetworzonych danych, uruchamianie obliczeń i wyświetlanie wyników wszystkich w tym samym środowisku. Zostanie on uruchomiony lokalnie na maszynie wirtualnej szablonu.  
- [Visual Studio Code](https://code.visualstudio.com/): zintegrowane środowisko programistyczne (IDE), które zapewnia bogate interaktywne środowisko podczas pisania i testowania notesu. Aby uzyskać więcej informacji, zobacz [Praca z notesami Jupyter w Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Podaj notesy dla klasy
Następnym zadaniem jest udostępnienie uczniom notesów, które mają być używane. Aby zapewnić własne notesy, możesz zapisywać notesy lokalnie na maszynie wirtualnej szablonu. 

Jeśli chcesz używać przykładowych notesów z poziomu Azure Machine Learning, zobacz [jak skonfigurować środowisko przy użyciu notesów Jupyter](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Opcjonalne: Włącz pulpit graficzny dla systemu Linux 
Obraz **Data Science Virtual Machine – Ubuntu** został już zainicjowany z serwerem X2GO i jest gotowy do akceptowania połączeń klienckich. Podczas konfigurowania maszyny wirtualnej szablonu nie są wymagane żadne dalsze kroki. 

### <a name="publish-the-template-machine"></a>Publikowanie maszyny szablonu
Po opublikowaniu szablonu każdy student zarejestrowany w laboratorium otrzyma kopię szablonu maszyny wirtualnej ze wszystkimi lokalnymi narzędziami i notesami, które zostały przez Ciebie skonfigurowane.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Jak uczniowie nawiązują połączenie z notesami Jupyter?
Po opublikowaniu szablonu każdy student będzie miał dostęp do maszyny wirtualnej, która zawiera wszystkie wstępnie skonfigurowane elementy klasy, w tym notesy Jupyter. W poniższych sekcjach przedstawiono różne sposoby łączenia się z notesami Jupyter. 

### <a name="for-windows-vms"></a>Maszyny wirtualne z systemem Windows
Jeśli masz uczniów z maszynami wirtualnymi z systemem Windows, muszą oni łączyć się z maszynami wirtualnymi i korzystać z notesów Jupyter, które są na nich dostępne lokalnie. 

Aby nawiązać połączenie z maszyną wirtualną z systemem Windows, student może korzystać z połączenia pulpitu zdalnego (RDP). Aby uzyskać szczegółowe instrukcje, zobacz [jak uzyskać dostęp do laboratorium zajęć](how-to-use-classroom-lab.md). 

Student korzystający z komputerów Mac lub Chromebook może wykonać instrukcje podane w poniższych artykułach, aby nawiązać połączenie z maszyną wirtualną z systemem Windows z analizą danych. 

- [Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Maszyny wirtualne z systemem Linux
Jeśli masz uczniów z maszynami wirtualnymi z systemem Linux, możesz skorzystać z kilku uczniów, aby nawiązać połączenie z notesami Jupyter na maszynach wirtualnych:

- Dostęp do notesów Jupyter lokalnie po nawiązaniu połączenia z maszyną wirtualną
    - SSH z maszyną wirtualną dla sesji terminalu
     - X2Go połączenie z maszyną wirtualną dla sesji graficznych
- Użyj tunelowania SSH, aby nawiązać połączenie z komputera lokalnego ucznia bezpośrednio z serwerem Jupyter na maszynie wirtualnej. 

W poniższych sekcjach znajdują się szczegółowe informacje o tych sposobach łączenia się z notesami Jupyter. 

#### <a name="ssh-to-virtual-machine"></a>SSH z maszyną wirtualną
Studenci mogą łączyć się za pośrednictwem protokołu SSH z maszynami wirtualnymi z systemem Linux z sesji terminalu. Aby uzyskać szczegółowe instrukcje, zobacz [jak uzyskać dostęp do laboratorium zajęć](how-to-use-classroom-lab.md). Jeśli korzystają z komputera klienckiego z systemem Windows, należy włączyć klienta SSH, [pobierając pobieranie lub](https://www.putty.org/) włączając [OpenSSH w systemie Windows](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse) do protokołu SSH z wiersza polecenia. 

1.  Uruchom maszynę wirtualną.
2.  Po uruchomieniu maszyny wirtualnej kliknij pozycję **Połącz**, co spowoduje wyświetlenie okna dialogowego zawierającego ciąg polecenia SSH, który będzie wyglądać podobnie do poniższego przykładu:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Przejdź do wiersza polecenia lub terminalu i wklej to polecenie, a następnie naciśnij klawisz **Enter**.
4.  Wprowadź hasło, aby zalogować się do maszyny wirtualnej. 

Gdy uczniowie są połączeni z maszynami wirtualnymi, mogą uzyskiwać dostęp do notesów Jupyter lokalnie i uruchamiać je.

#### <a name="x2go-to-virtual-machine"></a>X2Go do maszyny wirtualnej
Obraz **Data Science Virtual Machine – Ubuntu** został już zainicjowany z serwerem X2GO i jest gotowy do akceptowania połączeń klienckich. Aby nawiązać połączenie z pulpitem graficznym maszyny z systemem Linux, uczniowie muszą wykonać te jednorazowe kroki, aby skonfigurować X2Go na swoich komputerach klienckich:

1.  Pobierz i zainstaluj [klienta programu x2go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) dla platformy klienckiej.
2.  W [portalu Azure Lab Services](https://labs.azure.com)upewnij się, że maszyna wirtualna z systemem Linux, z którą chcesz nawiązać połączenie, została uruchomiona.
3.  Po uruchomieniu maszyny wirtualnej kliknij pozycję **Połącz**, co spowoduje wyświetlenie okna dialogowego zawierającego ciąg polecenia SSH, który będzie wyglądać podobnie do poniższego przykładu:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Po uzyskaniu tych informacji Otwórz aplikację kliencką X2Go i Utwórz nową sesję. 
5.  Wypełnij następujące wartości w okienku **Preferencje sesji** :
    - **Nazwa sesji**: może to być dowolne, ale zalecamy użycie nazwy maszyny wirtualnej laboratorium.
     - **Host**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Logowanie**: Student
     - **Port SSH**: 12345
     - **Typ sesji**: pulpit Xfce
6. Wybierz przycisk **OK**. 

    > [!NOTE]
     > Podczas tworzenia nowej sesji X2Go upewnij się, że używasz portu SSH, a **nie** portu RDP.

Teraz, aby nawiązać połączenie z maszyną wirtualną, wykonaj następujące kroki:    

1.  W kliencie X2Go kliknij dwukrotnie maszynę wirtualną, z którą chcesz nawiązać połączenie. 

    ![Klient X2Go](./media/class-type-jupyter-notebook/x2go-client.png)
2. Wprowadź hasło, aby nawiązać połączenie z maszyną wirtualną. (Może być konieczne nadanie X2Go uprawnień do obejścia zapory, aby zakończyć łączenie się).
3.  Powinien być teraz widoczny interfejs graficzny Ubuntu Data Science VM.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>Tunel SSH do Jupyter serwera na maszynie wirtualnej
Niektórzy uczniowie mogą chcieć połączyć się bezpośrednio z komputera lokalnego bezpośrednio z serwerem Jupyter w swoich maszynach wirtualnych. Protokół SSH umożliwia przekazywanie portów między komputerem lokalnym a serwerem zdalnym (w naszym przypadku, na maszynie wirtualnej laboratorium ucznia), dzięki czemu aplikacja uruchomiona na określonym porcie na serwerze jest **tunelowana** na port mapowania na komputerze lokalnym. Studenci powinni postępować zgodnie z tymi krokami, aby tunel SSH do serwera Jupyter na swoich maszynach wirtualnych laboratorium:

1.  W [portalu Azure Lab Services](https://labs.azure.com)upewnij się, że maszyna wirtualna z systemem Linux, którą chcesz połączyć, została uruchomiona.
2.  Po uruchomieniu maszyny wirtualnej kliknij pozycję **Połącz**, co spowoduje wyświetlenie okna dialogowego zawierającego ciąg polecenia SSH, który będzie wyglądać następująco:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. Na komputerze lokalnym uruchom terminal lub wiersz polecenia i skopiuj do niego parametry połączenia SSH. Następnie Dodaj `-L 8888:localhost:8888` do ciągu polecenia, który tworzy **tunel** między portami. Końcowy ciąg powinien wyglądać następująco:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Naciśnij klawisz **Enter** , aby uruchomić polecenie. 
5.  Po wyświetleniu monitu podaj hasło, aby połączyć się z maszyną wirtualną laboratorium. 
6.  Po nawiązaniu połączenia z maszyną wirtualną Uruchom serwer Jupyter za pomocą tego polecenia: 

    ```bash
     jupyter notebook
     ```
7. Uruchomienie polecenia spowoduje udostępnienie adresu URL w terminalu lub w wierszu polecenia. Adres URL powinien wyglądać następująco:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Wklej ten adres URL do przeglądarki na komputerze lokalnym, aby nawiązać połączenie i korzystać z Jupyter Notebook. 

    > [!NOTE]
    > Visual Studio Code również zapewnia zaawansowane [środowisko edytowania Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support). Można postępować zgodnie z instrukcjami dotyczącymi [sposobu nawiązywania połączenia ze zdalnym serwerem Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) i używania tego samego adresu URL z poprzedniego kroku, aby nawiązać połączenie z vs Code zamiast z przeglądarki. 


## <a name="cost-estimate"></a>Oszacowanie kosztów
Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy. Będziemy używać klasy 25 studentów. Zaplanowana godzina klasy wynosi 20 godzin. Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy. Wybrany rozmiar maszyny wirtualnej to mały procesor GPU (COMPUTE), czyli 139 jednostek laboratorium. Jeśli chcesz użyć małych (20 jednostek Lab) lub średniego rozmiaru (42 jednostek laboratoryjnych), możesz zamienić część jednostki laboratoryjnej na poniższe równanie, podając poprawną liczbę.  

Oto przykład możliwego oszacowania kosztów dla tej klasy: 25 studentów * (20 zaplanowanych godzin + 10 godzin przydziału) * 139 jednostek laboratoryjnych * 0,01 USD za godzinę = 1042,5 USD

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
