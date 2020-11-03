---
title: 'Szybki Start: Tworzenie Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Skonfiguruj i Utwórz Data Science Virtual Machine dla systemu Linux (Ubuntu), aby przeprowadzić analizę i uczenie maszynowe.
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: a14f7ac212d9957e5258f75a386b8730ad5de700
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233846"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Szybki Start: Konfigurowanie Data Science Virtual Machine dla systemu Linux (Ubuntu)

Zacznij korzystać z Ubuntu 18,04 Data Science Virtual Machine.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć Ubuntu 18,04 Data Science Virtual Machine, musisz mieć subskrypcję platformy Azure. [Wypróbuj bezpłatnie platformę Azure](https://azure.com/free).

>[!NOTE]
>Bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych obsługujących procesor GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Tworzenie Data Science Virtual Machine dla systemu Linux

Poniżej przedstawiono procedurę tworzenia wystąpienia Data Science Virtual Machine Ubuntu 18,04:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Jeśli użytkownik nie jest jeszcze zalogowany, może zostać wyświetlony monit o zalogowanie się do konta platformy Azure.
1. Znajdź listę maszyn wirtualnych, wpisując ciąg "maszyna wirtualna do analizy danych" i wybierając pozycję "Data Science Virtual Machine-Ubuntu 18,04"

1. W następnym oknie wybierz pozycję **Utwórz**.

1. Należy przekierować do bloku "Tworzenie maszyny wirtualnej".
   
1. Wprowadź następujące informacje, aby skonfigurować każdy krok kreatora:

    1. **Podstawy** :
    
       * **Subskrypcja** : Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
       * **Grupa zasobów** : Utwórz nową grupę lub Użyj istniejącej.
       * **Nazwa maszyny wirtualnej** : Wprowadź nazwę maszyny wirtualnej. Ta nazwa będzie używana w Azure Portal.
       * **Region** : wybierz najbardziej odpowiednie centrum danych. Aby uzyskać najszybszy dostęp do sieci, znajduje się w centrum danych, które ma najwięcej z nich lub znajduje się najbliżej fizycznej lokalizacji. Dowiedz się więcej o [regionach platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Obraz** : pozostaw wartość domyślną.
       * **Rozmiar** : Ta opcja powinna automatycznie wypełniać rozmiar, który jest odpowiedni dla ogólnych obciążeń. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux na platformie Azure](../../virtual-machines/linux/sizes.md).
       * **Typ uwierzytelniania** : aby szybciej skonfigurować konfigurację, wybierz pozycję "hasło". 
         
         > [!NOTE]
         > Jeśli zamierzasz używać JupyterHub, upewnij się, że wybrano opcję "Password" (hasło), ponieważ JupyterHub *nie* jest skonfigurowana do używania kluczy publicznych SSH.

       * **Nazwa użytkownika** : Wprowadź nazwę użytkownika administratora. Ta nazwa użytkownika będzie używana do logowania się do maszyny wirtualnej. Ta nazwa użytkownika nie może być taka sama jak nazwa użytkownika platformy Azure. *Nie* używaj wielkich liter.
         
         > [!IMPORTANT]
         > Jeśli używasz wielkich liter w nazwie użytkownika, JupyterHub nie będzie działał i wystąpi błąd wewnętrzny serwera 500.

       * **Hasło** : wprowadź hasło, które będzie używane do logowania się do maszyny wirtualnej.    
    
   1. Wybierz pozycję **Przejrzyj i utwórz**.
   1. **Przegląd + tworzenie**
      * Sprawdź, czy wszystkie wprowadzone informacje są poprawne. 
      * Wybierz przycisk **Utwórz**.
    
    Inicjowanie obsługi powinno trwać około 5 minut. Stan jest wyświetlany w Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Jak uzyskać dostęp do Data Science Virtual Machine Ubuntu

Możesz uzyskać dostęp do Ubuntu DSVM na jeden z trzech sposobów:

  * Protokół SSH w sesjach terminalu
  * Program X2Go w sesjach graficznych
  * Serwery JupyterHub i JupyterLab w notesach programu Jupyter

Możesz również dołączyć Data Science Virtual Machine do Azure Notebooks do uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>Protokół SSH

Jeśli maszyna wirtualna została skonfigurowana przy użyciu uwierzytelniania SSH, możesz zalogować się przy użyciu poświadczeń konta utworzonych w sekcji **podstawy** kroku 3 dla interfejsu powłoki tekstu. W systemie Windows można pobrać narzędzie klienta SSH [, takie jak](https://www.putty.org)wyglądająco. Jeśli wolisz korzystać z graficznego pulpitu (X systemu Windows), możesz użyć funkcji przekazywania dalej X11.

> [!NOTE]
> Klient X2Go był lepszy niż X11 do przesyłania dalej w testowaniu. Zalecamy korzystanie z klienta X2Go w interfejsie graficznym.

### <a name="x2go"></a>X2Go

Maszyna wirtualna z systemem Linux została już zainicjowana z serwerem X2Go i gotowa do akceptowania połączeń klienckich. Aby nawiązać połączenie z graficznym pulpitem maszyny wirtualnej z systemem Linux, wykonaj poniższą procedurę w obrębie klienta:

1. Pobierz i zainstaluj klienta X2Go na platformie klienckiej z witryny [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Zanotuj publiczny adres IP maszyny wirtualnej, który można znaleźć w Azure Portal, otwierając utworzoną maszynę wirtualną.

   ![Ubuntu adres IP maszyny](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Uruchom klienta X2Go. Jeśli okno "Nowa sesja" nie zostanie automatycznie wyskakujące, przejdź do sesji > nowej sesji.

1. W oknie konfiguracji wynikowej wprowadź następujące parametry konfiguracji:
   * **Karta Session** (Sesja):
     * **Host** : wprowadź zanotowany wcześniej adres IP maszyny wirtualnej.
     * **Login** (Identyfikator logowania): wprowadź nazwę użytkownika na maszynie wirtualnej z systemem Linux.
     * **SSH Port** (Port SSH): pozostaw wartość domyślną 22.
     * **Session Type** (Typ sesji): zmień wartość na **XFCE**. Obecnie maszyna wirtualna z systemem Linux obsługuje tylko pulpit XFCE.
   * **Karta Media** (Multimedia): możesz wyłączyć obsługę dźwięku i drukowanie przez klienta, jeśli nie potrzebujesz tych funkcji.
   * **Foldery udostępnione** : Użyj tej karty, aby dodać katalog komputera klienckiego, który ma zostać zainstalowany na maszynie wirtualnej. 

   ![Konfiguracja rozwiązania X2Go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Wybierz pozycję **OK**.
1. Kliknij pole w prawym okienku okna X2Go, aby wyświetlić ekran logowania dla maszyny wirtualnej.
1. Wprowadź hasło maszyny wirtualnej.
1. Wybierz pozycję **OK**.
1. Aby zakończyć nawiązywanie połączenia, być może trzeba będzie nadać uprawnienia rozwiązania X2Go umożliwiające obchodzenie zapory.
1. Powinien być teraz widoczny interfejs graficzny Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

Ubuntu DSVM uruchamia [JupyterHub](https://github.com/jupyterhub/jupyterhub), wieloużytkownikowego serwera Jupyter. Aby nawiązać połączenie, wykonaj następujące czynności:

   1. Zanotuj publiczny adres IP dla maszyny wirtualnej, wyszukując i wybierając maszynę wirtualną w Azure Portal.
      ![Ubuntu adres IP maszyny](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Na komputerze lokalnym Otwórz przeglądarkę internetową i przejdź do protokołu https: \/ /Your-VM-IP: 8000, zastępując ciąg "The-VM-IP" adresem IP, który został wcześniej zanotowany.
   1. Przeglądarka prawdopodobnie uniemożliwia otworzenie strony bezpośrednio, co oznacza, że wystąpił błąd certyfikatu. DSVM zapewnia bezpieczeństwo za pośrednictwem certyfikatu z podpisem własnym. Większość przeglądarek pozwoli Ci kliknąć po tym ostrzeżeniu. Wiele przeglądarek będzie w dalszym ciągu dostarczać wizualne ostrzeżenie dotyczące certyfikatu w całej sesji sieci Web.
   1. Wprowadź nazwę użytkownika i hasło, które zostały użyte podczas tworzenia maszyny wirtualnej i zaloguj się. 

      ![Wprowadź nazwę logowania Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

>[!NOTE]
> Jeśli na tym etapie wystąpi błąd 500, prawdopodobnie w nazwie użytkownika są używane wielkie litery. Jest to znana interakcja między centrum Jupyter i PAMAuthenticator, którego używa. Jeśli zostanie wyświetlony komunikat o błędzie "nie można nawiązać połączenia z tą stroną", prawdopodobnie trzeba będzie dostosować uprawnienia do sieciowej grupy zabezpieczeń. W Azure Portal Znajdź zasób sieciowej grupy zabezpieczeń w grupie zasobów. Aby uzyskać dostęp do usługi JupyterHub z publicznej sieci Internet, musisz mieć otwarty port 8000. (Obraz pokazuje, że ta maszyna wirtualna jest skonfigurowana dla dostępu just in Time, co jest zdecydowanie zalecane. Zobacz [Zabezpieczanie portów zarządzania przy użyciu dostępu just in Time](../../security-center/security-center-just-in-time.md). ![Konfiguracja sieciowej grupy zabezpieczeń](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Przejrzyj wiele przykładowych notesów, które są dostępne.

Dostępna jest również JupyterLab nowej generacji notesów Jupyter i JupyterHub. Aby uzyskać do niego dostęp, zaloguj się w usłudze JupyterHub, a następnie przejdź do adresu URL https: \/ /Your-VM-IP: 8000/User/username/Lab, zastępując ciąg "Twoja nazwa użytkownika" nazwą użytkownika wybraną podczas konfigurowania maszyny wirtualnej. Ponownie można zablokować dostęp do witryny z powodu błędu certyfikatu.

Możesz ustawić JupyterLab jako domyślny serwer notesu, dodając ten wiersz do `/etc/jupyterhub/jupyterhub_config.py` :

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Następne kroki

Oto jak można kontynuować uczenie i eksplorację:

* Analiza [danych w Data Science Virtual Machine dla systemu Linux](linux-dsvm-walkthrough.md) zawiera informacje na temat wykonywania kilku typowych zadań związanych z nauką o danych przy użyciu DSVM systemu Linux. 
* Zapoznaj się z różnymi narzędziami do nauki o danych w DSVM, korzystając z narzędzi opisanych w tym artykule. Aby uzyskać więcej informacji na temat narzędzi zainstalowanych na maszynie wirtualnej, można również uruchomić `dsvm-more-info` na powłoce w obrębie maszyny wirtualnej.  
* Dowiedz się, jak systematycznie kompilować rozwiązania analityczne przy użyciu [zespołowego procesu nauki o danych](https://aka.ms/tdsp).
* Odwiedź [Azure AI Gallery](https://gallery.azure.ai/) , aby zapoznać się z przykładami dotyczącymi usługi Machine Learning i analizy danych, które korzystają z usług Azure AI.
* Zapoznaj się z odpowiednią [dokumentacją](./reference-ubuntu-vm.md) dla tej maszyny wirtualnej.
