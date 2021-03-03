---
title: Konteneryzowanie ASP.NET aplikacje i Przeprowadź migrację do usługi Azure Kubernetes Service
description: Dowiedz się, jak konteneryzowanie aplikacje ASP.NET i migrować je do usługi Azure Kubernetes.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 4d61af8a95844884cdb0152f01c32f9c658e3ee4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748127"
---
# <a name="containerize-aspnet-applications-and-migrate-to-azure-kubernetes-service"></a>Konteneryzowanie ASP.NET aplikacje i Przeprowadź migrację do usługi Azure Kubernetes Service

W tym artykule dowiesz się, jak konteneryzowanie aplikacje ASP.NET i migrować je do [usługi Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) za pomocą narzędzia Azure Migrate: App kontenerach. Proces kontenerach nie wymaga dostępu do bazy kodu i zapewnia łatwy sposób konteneryzowanie istniejących aplikacji. Narzędzie działa przy użyciu stanu uruchomienia aplikacji na serwerze w celu określenia składników aplikacji i ułatwia ich spakowanie w obrazie kontenera. Aplikację kontenerową można następnie wdrożyć w usłudze Azure Kubernetes Service (AKS). 

Narzędzie Azure Migrate: App kontenerach obecnie obsługuje — 

- Konteneryzowania ASP.NET aplikacje i wdrażaj je w kontenerach systemu Windows na AKS.
- Konteneryzowania Web Apps Java na platformie Apache Tomcat (na serwerach z systemem Linux) i wdrażaj je w kontenerach systemu Linux na platformie AKS. [Dowiedz się więcej](./tutorial-containerize-java-kubernetes.md) 


Azure Migrate: App kontenerach Tool ułatwia 

- **Odnajdywanie aplikacji**: Narzędzie zdalnie nawiązuje połączenie z serwerami aplikacji z uruchomioną aplikacją ASP.NET i odnajduje składniki aplikacji. Narzędzie tworzy pliku dockerfile, którego można użyć do utworzenia obrazu kontenera dla aplikacji.
- **Tworzenie obrazu kontenera**: możesz sprawdzać i dostosowywać pliku dockerfile zgodnie z wymaganiami aplikacji i używać go do kompilowania obrazu kontenera aplikacji. Obraz kontenera aplikacji jest wypychany do określonego Azure Container Registry.
- **Wdróż w usłudze Azure Kubernetes Service**: narzędzie generuje następnie pliki definicji zasobów Kubernetes YAML, które są konieczne do wdrożenia aplikacji kontenera w klastrze usługi Azure Kubernetes. Można dostosować pliki YAML i użyć ich do wdrożenia aplikacji na AKS. 

> [!NOTE]
> Narzędzie Azure Migrate: App kontenerach ułatwia odnajdywanie określonych typów aplikacji (aplikacji sieci Web ASP.NET i Java na platformie Apache Tomcat) oraz ich składników na serwerze aplikacji. Aby odnajdywać serwery i spis aplikacji, ról i funkcji uruchomionych na maszynach lokalnych, użyj Azure Migrate: funkcji odnajdywania i oceny. [Dowiedz się więcej](./tutorial-discover-vmware.md) 

Mimo że wszystkie aplikacje nie będą korzystać z prostych przesunięć do kontenerów bez znacznego przetworzenia architektury, niektóre korzyści wynikające z przeniesienia istniejących aplikacji do kontenerów bez ponownego zapisywania obejmują:

- **Ulepszone wykorzystanie infrastruktury:** W przypadku kontenerów wiele aplikacji może współużytkować zasoby i być hostowane w tej samej infrastrukturze. Może to ułatwić skonsolidowanie infrastruktury i zwiększenie wykorzystania. 
- **Uproszczone zarządzanie:** Hosting aplikacji na nowoczesnej zarządzanej platformie infrastruktury, takiej jak AKS, pozwala uprościć swoje praktyki zarządzania przy zachowaniu kontroli nad infrastrukturą. Można to osiągnąć przez wycofanie lub zmniejszenie procesów konserwacji i zarządzania infrastrukturą, które są tradycyjnie wykonywane z posiadaną infrastrukturą.
- **Przenośność aplikacji:** Dzięki zwiększonemu wdrażaniu i standaryzacji formatów specyfikacji kontenera oraz platformom aranżacji, przenośność aplikacji nie jest już problemem. 
- **Wdrażaj nowoczesne zarządzanie za pomocą DevOps:** Pomaga w wdrażaniu i standaryzacji nowoczesnych praktyk związanych z zarządzaniem i zabezpieczeniami przy użyciu infrastruktury jako kodu i przejścia do DevOps.


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure.
> * Zainstaluj narzędzie Azure Migrate: App kontenerach.
> * Odkryj aplikację ASP.NET.
> * Utwórz obraz kontenera.
> * Wdróż aplikację kontenerową w systemie AKS.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

**Wymaganie** | **Szczegóły**
--- | ---
**Zidentyfikuj komputer, aby zainstalować narzędzie** | Maszyna z systemem Windows w celu zainstalowania i uruchomienia narzędzia Azure Migrate: App kontenerach. Komputer z systemem Windows może być serwerem (Windows Server 2016 lub nowszym) lub systemem operacyjnym klienta (Windows 10), co oznacza, że narzędzie można również uruchomić na pulpicie. <br/><br/> Maszyna z systemem Windows, na której działa narzędzie, powinna mieć łączność sieciową z serwerami i maszynami wirtualnymi hostującym aplikacje ASP.NET.<br/><br/> Upewnij się, że dostępne jest 6 GB miejsca na maszynie z systemem Windows, na którym uruchomiono narzędzie Azure Migrate: App kontenerach do przechowywania artefaktów aplikacji. <br/><br/> Komputer z systemem Windows powinien mieć dostęp do Internetu, bezpośrednio lub za pośrednictwem serwera proxy. <br/> <br/>Zainstaluj narzędzie Web Deploy firmy Microsoft na komputerze, na którym działa narzędzie pomocnika kontenerach aplikacji i serwer aplikacji, jeśli nie zostały jeszcze zainstalowane. Możesz pobrać narzędzie z tego [miejsca](https://aka.ms/webdeploy3.6) 
**Serwery aplikacji** | Włącz komunikację zdalną programu PowerShell na serwerach aplikacji: Zaloguj się do serwera aplikacji i postępuj zgodnie z [tymi](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting) instrukcjami, aby włączyć funkcję komunikacji zdalnej programu PowerShell. <br/><br/> Jeśli na serwerze aplikacji jest uruchomiony program Windows Server 2008 R2, upewnij się, że na serwerze aplikacji jest zainstalowany program PowerShell 5,1. Postępuj zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.microsoft.com/powershell/scripting/windows-powershell/wmf/setup/install-configure) , aby pobrać i zainstalować program PowerShell 5,1 na serwerze aplikacji. <br/><br/> Zainstaluj narzędzie Web Deploy firmy Microsoft na komputerze, na którym działa narzędzie pomocnika kontenerach aplikacji i serwer aplikacji, jeśli nie zostały jeszcze zainstalowane. Możesz pobrać narzędzie z tego [miejsca](https://aka.ms/webdeploy3.6) 
**Aplikacja ASP.NET** | Narzędzie obsługuje obecnie <br/><br/> -ASP.NET aplikacje korzystające z Microsoft .NET Framework 3,5 lub nowszego.<br/> -Serwery aplikacji z systemem Windows Server 2008 R2 lub nowszym (serwery aplikacji powinny mieć uruchomiony program PowerShell w wersji 5,1). <br/> -Aplikacje działające w Internet Information Services (IIS) 7,5 lub nowszym. <br/><br/> Narzędzie nie obsługuje obecnie <br/><br/> -Aplikacje wymagające uwierzytelniania systemu Windows (AKS nie obsługuje obecnie gMSA). <br/> -Aplikacje, które są zależne od innych usług systemu Windows hostowanych poza usługami IIS. 


## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

Po skonfigurowaniu subskrypcji będziesz potrzebować konta użytkownika platformy Azure z:
- Uprawnienia właściciela do subskrypcji platformy Azure
- Uprawnienia do rejestrowania aplikacji Azure Active Directory

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi** wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure.](./media/tutorial-discover-vmware/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp** Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli** kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę.](./media/tutorial-discover-vmware/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli** wybierz rolę właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta.](./media/tutorial-discover-vmware/assign-role.png)

7. Twoje konto platformy Azure wymaga również **uprawnień do rejestrowania aplikacji Azure Active Directory.**
8. W Azure Portal przejdź do **Azure Active Directory**  >  **użytkowników**  >  **Ustawienia użytkownika**.
9. W obszarze **Ustawienia użytkownika** Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

      ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje.](./media/tutorial-discover-vmware/register-apps.png)

10. Jeśli ustawienia "Rejestracje aplikacji" są ustawione na wartość "nie", zażądaj dzierżawy/administratora globalnego, aby przypisał wymagane uprawnienie. Alternatywnie, dzierżawa/Administrator globalny może przypisać rolę **dewelopera aplikacji** do konta, aby umożliwić rejestrację Azure Active Directory aplikacji. [Dowiedz się więcej](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Pobieranie i Instalowanie Azure Migrate: App kontenerach Tool

1. [Pobierz](https://go.microsoft.com/fwlink/?linkid=2134571) Azure Migrate: App kontenerach Installer na komputerze z systemem Windows.
2. Uruchom program PowerShell w trybie administratora i zmień katalog programu PowerShell w folder zawierający Instalatora. 
3. Uruchom skrypt instalacyjny przy użyciu polecenia

   ```powershell
   .\App ContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Uruchamianie narzędzia kontenerach aplikacji 

1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z maszyną z systemem Windows, na którym działa narzędzie kontenerach aplikacji, a następnie otwórz adres URL narzędzia: **https://*Nazwa komputera lub adres IP*: 44368**.

   Możesz też otworzyć aplikację z poziomu pulpitu, wybierając skrót do aplikacji.

2. Jeśli zostanie wyświetlone ostrzeżenie informujące o tym, że połączenie nie jest prywatne, kliknij pozycję Zaawansowane i wybierz opcję przejścia do witryny sieci Web. To ostrzeżenie jest wyświetlane, gdy interfejs sieci Web używa certyfikatu TLS/SSL z podpisem własnym.
3. Na ekranie logowania Użyj konta administratora lokalnego na komputerze, aby się zalogować.
4. W polu Określ typ aplikacji wybierz pozycję **ASP.NET Web Apps** jako typ aplikacji, którą chcesz konteneryzowanie. 

    ![Domyślne ładowanie dla narzędzia App kontenerach.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Ukończ wymagania wstępne narzędzia
1. Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
6. W narzędziu Web App > **Skonfiguruj wymagania wstępne**, wykonaj następujące czynności:
   - **Łączność**: Narzędzie sprawdza, czy komputer z systemem Windows ma dostęp do Internetu. Jeśli maszyna używa serwera proxy:
     - Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy (w postaci adres IP lub nazwa FQDN) i port nasłuchujący.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Obsługiwane są tylko serwery proxy HTTP.
     - Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
   - **Zainstaluj aktualizacje**: Narzędzie automatycznie sprawdzi dostępność najnowszych aktualizacji i zainstaluje je. Możesz również ręcznie zainstalować najnowszą wersję narzędzia z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2134571).
   - **Zainstaluj narzędzie microsoft Web Deploy**: Narzędzie sprawdzi, czy narzędzie Microsoft Web Deploy jest zainstalowane na komputerze z systemem Windows, na którym działa narzędzie Azure Migrate: App kontenerach.
   - **Włącz komunikację zdalną programu PowerShell**: Narzędzie wyświetli informację o tym, że funkcja komunikacji zdalnej programu PowerShell jest włączona na serwerach aplikacji, na których działają aplikacje ASP.NET. 
   

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Kliknij pozycję **Zaloguj** , aby zalogować się do konta platformy Azure. 

1. Do uwierzytelnienia w systemie Azure potrzebny jest kod urządzenia. Kliknięcie przycisku Zaloguj spowoduje otwarcie modalnego kodu urządzenia. 
2. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.

    ![Modalne wyświetlanie kodu urządzenia.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Na nowej karcie Wklej kod urządzenia i Zakończ logowanie się przy użyciu poświadczeń konta platformy Azure. Możesz zamknąć kartę przeglądarki po zakończeniu logowania i wrócić do interfejsu internetowego narzędzia kontenerach aplikacji.
4. Wybierz **dzierżawę platformy Azure** , której chcesz użyć.
5. Określ **subskrypcję platformy Azure** , której chcesz użyć. 

## <a name="discover-aspnet-applications"></a>Odkryj aplikacje ASP.NET

Narzędzie pomocnika kontenerach aplikacji łączy się zdalnie z serwerami aplikacji przy użyciu podanych poświadczeń i próbuje wykryć aplikacje ASP.NET hostowane na serwerach aplikacji.

1. Określ **adres IP/nazwę FQDN oraz poświadczenia** serwera z uruchomioną aplikacją ASP.NET, które powinny być używane do zdalnego łączenia się z serwerem na potrzeby odnajdywania aplikacji. 
    - Podane poświadczenia muszą mieć uprawnienia administratora lokalnego (Windows) na serwerze aplikacji. 
    - W przypadku kont domeny (użytkownik musi być administratorem na serwerze aplikacji), poprzedź nazwę użytkownika nazwą domeny w formacie *<domena \ nazwa_użytkownika>*. 
    - Odnajdywanie aplikacji można uruchomić maksymalnie pięć serwerów jednocześnie. 

2. Kliknij przycisk **Sprawdź poprawność** , aby sprawdzić, czy serwer aplikacji jest dostępny z komputera z uruchomionym narzędziem i czy poświadczenia są prawidłowe. Po pomyślnej weryfikacji w kolumnie Stan zostanie wyświetlony stan **zamapowane**.  

    ![Zrzut ekranu przedstawiający adres IP serwera i poświadczenia.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Kliknij przycisk **Kontynuuj** , aby uruchomić odnajdywanie aplikacji na wybranych serwerach aplikacji.   

4. Po pomyślnym zakończeniu odnajdywania aplikacji możesz wybrać listę aplikacji do konteneryzowanie.

    ![Zrzut ekranu przedstawiający wykrytą aplikację ASP.NET.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Użyj pola wyboru, aby wybrać aplikacje do konteneryzowanie.
5. **Określ nazwę kontenera**: Określ nazwę kontenera docelowego dla każdej wybranej aplikacji. Nazwa kontenera powinna być określona jako nazwa <*: tag*>, gdzie tag jest używany dla obrazu kontenera. Na przykład, można określić nazwę kontenera docelowego jako *nazwa_aplikacji: V1*.   

### <a name="parameterize-application-configurations"></a>Konfiguracje aplikacji Sparametryzuj 
Parametryzacja konfiguracja powoduje, że jest ona dostępna jako parametr czasu wdrożenia. Pozwala to na skonfigurowanie tego ustawienia podczas wdrażania aplikacji, w przeciwieństwie do tego, że jest on zakodowany na określoną wartość w obrazie kontenera. Na przykład ta opcja jest przydatna w przypadku parametrów połączenia bazy danych.
1. Kliknij pozycję **konfiguracje aplikacji** , aby przejrzeć wykryte konfiguracje. 
2. Zaznacz pole wyboru, aby Sparametryzuj wykryte konfiguracje aplikacji. 
3. Po wybraniu konfiguracji do Sparametryzuj kliknij pozycję **Zastosuj** .

   ![Zrzut ekranu przedstawiający aplikację parametryzacja ASP.NET App Configuration.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externalize zależności systemu plików

 Możesz dodać inne foldery używane przez aplikację. Określ, czy powinny być częścią obrazu kontenera, czy mają być zewnętrzne za pomocą woluminów trwałych w udziale plików platformy Azure. Używanie woluminów trwałych działa doskonale w przypadku aplikacji stanowych, które przechowują stan poza kontenerem lub zawierają inną zawartość statyczną przechowywaną w systemie plików. [Dowiedz się więcej](https://docs.microsoft.com/azure/aks/concepts-storage)
 
1. Kliknij przycisk **Edytuj** w obszarze foldery aplikacji, aby przejrzeć wykryte foldery aplikacji. Wykryte foldery aplikacji zostały zidentyfikowane jako obowiązkowe artefakty wymagane przez aplikację i zostaną skopiowane do obrazu kontenera. 
    
2. Kliknij pozycję **Dodaj foldery** i określ ścieżki folderów do dodania. 
3. Aby dodać wiele folderów do tego samego woluminu, podaj `,` wartości rozdzielone przecinkami (). 
4. Wybierz pozycję **trwały wolumin** jako opcję magazyn, jeśli chcesz, aby foldery były przechowywane poza kontenerem na woluminie trwałym. 
5. Po przejrzeniu folderów aplikacji kliknij przycisk **Zapisz** . 
   ![Zrzut ekranu przedstawiający wybór magazynu dla woluminów aplikacji.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Kliknij przycisk **Kontynuuj** , aby przejść do fazy kompilacji obrazu kontenera.

## <a name="build-container-image"></a>Tworzenie obrazu kontenera


1. **Wybierz Azure Container Registry**: Użyj listy rozwijanej, aby wybrać [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) , która będzie używana do kompilowania i przechowywania obrazów kontenerów aplikacji. Możesz użyć istniejącego Azure Container Registry lub utworzyć nowy, korzystając z opcji Utwórz nowy rejestr.

    ![Zrzut ekranu przedstawiający wybór ACR aplikacji.](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Przejrzyj pliku dockerfile**: pliku dockerfile, który jest wymagany do skompilowania obrazów kontenerów dla każdej wybranej aplikacji, jest generowany na początku kroku kompilacji. Kliknij przycisk **Przeglądaj** , aby przejrzeć pliku dockerfile. Możesz również dodać wszelkie niezbędne dostosowania do pliku dockerfile w kroku przegląd i zapisać zmiany przed rozpoczęciem procesu kompilacji.

3. **Wyzwalanie procesu kompilacji**: Wybierz aplikacje do skompilowania obrazów, a następnie kliknij przycisk **Kompiluj**. Kliknięcie przycisku Kompiluj spowoduje rozpoczęcie kompilowania obrazu kontenera dla każdej aplikacji. Narzędzie ciągle monitoruje stan kompilacji i umożliwi przechodzenie do następnego kroku po pomyślnym zakończeniu kompilacji.

4. **Stan kompilacji śledzenia**: można także monitorować postęp kroku kompilacji, klikając link **Kompiluj w toku** w kolumnie Stan. Po wyzwoleniu procesu kompilacji łącze potrwa kilka minut.  

5. Po zakończeniu kompilacji kliknij pozycję **Kontynuuj** , aby określić ustawienia wdrożenia. 

    ![Zrzut ekranu przedstawiający ukończenie kompilacji obrazu kontenera aplikacji.](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Wdróż aplikację z kontenerem w witrynie AKS

Po skompilowaniu obrazu kontenera następnym krokiem jest wdrożenie aplikacji jako kontenera w [usłudze Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/). 

1. **Wybierz klaster usługi Azure Kubernetes**: Określ klaster AKS, na którym ma zostać wdrożona aplikacja. 

     - Wybrany klaster AKS musi mieć pulę węzłów systemu Windows. 
     - Klaster musi być skonfigurowany tak, aby zezwalał na ściąganie obrazów z Azure Container Registry, które zostały wybrane do przechowywania obrazów. 
         - Uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure, aby dołączyć klaster AKS do ACR.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Jeśli nie masz klastra AKS lub chcesz utworzyć nowy klaster AKS do wdrażania aplikacji w programie, możesz wybrać opcję Utwórz za pomocą narzędzia, klikając pozycję **Utwórz nowy klaster AKS**.      
          - Klaster AKS utworzony za pomocą narzędzia zostanie utworzony za pomocą puli węzłów systemu Windows. Klaster zostanie skonfigurowany tak, aby zezwalał na ściąganie obrazów z Azure Container Registry, które zostały utworzone wcześniej (Jeśli wybrano opcję Utwórz nowy rejestr).
     - Po wybraniu klastra AKS kliknij pozycję **Kontynuuj** .

2. **Określ udział plików platformy Azure**: Jeśli dodano więcej folderów i wybrano opcję trwały wolumin, określ udział plików platformy Azure, który ma być używany przez Azure Migrate: App kontenerach Tool w procesie wdrażania. Narzędzie utworzy nowe katalogi w tym udziale plików platformy Azure w celu skopiowania folderów aplikacji skonfigurowanych dla trwałego magazynu woluminów. Po zakończeniu wdrażania aplikacji Narzędzie czyści udział plików platformy Azure, usuwając katalogi, które zostały utworzone. 

     - Jeśli nie masz udziału plików platformy Azure lub chcesz utworzyć nowy udział plików platformy Azure, możesz wybrać opcję Utwórz za pomocą narzędzia, klikając pozycję **Utwórz nowe konto magazynu i udział plików**.  

3. **Konfiguracja wdrażania aplikacji**: po wykonaniu powyższych czynności należy określić konfigurację wdrożenia dla aplikacji. Kliknij pozycję **Konfiguruj** , aby dostosować wdrożenie aplikacji. W kroku Konfigurowanie można wprowadzić następujące dostosowania:
     - **Ciąg prefiksu**: Określ ciąg prefiksu do użycia w nazwie dla wszystkich zasobów, które są tworzone dla aplikacji kontenera w klastrze AKS.
     - **Certyfikat SSL**: Jeśli aplikacja wymaga powiązania witryny https, określ plik PFX zawierający certyfikat, który ma być używany dla powiązania. Plik PFX nie powinien być chroniony hasłem, a oryginalna witryna nie powinna mieć wielu powiązań.
     - **Zestawy replik**: Określ liczbę wystąpień aplikacji (na podst.), które powinny być uruchamiane wewnątrz kontenerów.
     - **Typ modułu równoważenia obciążenia**: wybierz opcję *zewnętrzny* , jeśli aplikacja kontenera powinna być dostępna z sieci publicznych. 
     - **Konfiguracja aplikacji**: dla wszystkich konfiguracji aplikacji, które zostały sparametryzowane, podaj wartości, które mają być używane dla bieżącego wdrożenia.
     - **Magazyn**: w przypadku wszystkich folderów aplikacji, które zostały skonfigurowane dla trwałego magazynu woluminów, określ, czy wolumin powinien być współużytkowany między wystąpieniami aplikacji, czy powinien być inicjowany indywidualnie z każdym wystąpieniem w kontenerze. Domyślnie wszystkie foldery aplikacji na woluminach trwałych są konfigurowane jako współużytkowane.  
     - Kliknij przycisk **Zastosuj** , aby zapisać konfigurację wdrożenia.
     - Kliknij przycisk **Kontynuuj** , aby wdrożyć aplikację.

    ![Zrzut ekranu przedstawiający konfigurację aplikacji wdrożenia.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

4. **Wdróż aplikację**: po zapisaniu konfiguracji wdrażania dla aplikacji narzędzie wygeneruje KUBERNETES wdrożenia YAML dla aplikacji. 
     - Kliknij przycisk **Edytuj** , aby przejrzeć i dostosować KUBERNETES wdrożenia YAML dla aplikacji. 
     - Wybierz aplikację do wdrożenia.
     - Kliknij przycisk **Wdróż** , aby rozpocząć wdrożenia dla wybranych aplikacji

         ![Zrzut ekranu przedstawiający konfigurację wdrażania aplikacji.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy.png)

     - Po wdrożeniu aplikacji można kliknąć kolumnę *stan wdrożenia* , aby śledzić zasoby, które zostały wdrożone dla aplikacji. 

## <a name="download-generated-artifacts"></a>Pobierz wygenerowane artefakty

Wszystkie artefakty, które są używane do kompilowania i wdrażania aplikacji w AKS, w tym pliki specyfikacji pliku dockerfile i Kubernetes YAML, są przechowywane na komputerze, na którym działa narzędzie. Artefakty znajdują się w witrynie *C:\ProgramData\Microsoft Azure Migrate App kontenerach*.

Pojedynczy folder jest tworzony dla każdego serwera aplikacji. Można wyświetlić i pobrać wszystkie pośrednie artefakty używane w procesie kontenerach, przechodząc do tego folderu. Folder odpowiadający serwerowi aplikacji zostanie oczyszczony na początku każdego uruchomienia narzędzia dla określonego serwera.

## <a name="troubleshoot-issues"></a>Rozwiązywanie problemów

Aby rozwiązać wszelkie problemy z narzędziem, można sprawdzić pliki dziennika na komputerze z systemem Windows, na którym jest uruchomione narzędzie App kontenerach. Pliki dziennika narzędzi znajdują się w folderze *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs* . 

## <a name="next-steps"></a>Następne kroki

- Konteneryzowania Web Apps Java na platformie Apache Tomcat (na serwerach z systemem Linux) i wdrażaj je w kontenerach systemu Linux na platformie AKS. [Dowiedz się więcej](./tutorial-containerize-java-kubernetes.md) 