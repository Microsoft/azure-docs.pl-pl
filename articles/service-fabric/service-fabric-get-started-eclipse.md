---
title: Wtyczka Service Fabric platformy Azure dla programu zaćmienie
description: Dowiedz się więcej na temat rozpoczynania pracy z usługą Azure Service Fabric w języku Java przy użyciu funkcji przezaćmienia i Service Fabric dostarczonej wtyczki.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.custom: devx-track-java
ms.openlocfilehash: b85206f9b1e92607bba7b6f141b700922c129b65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656924"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Wtyczka usługi Service Fabric na potrzeby tworzenia aplikacji Java w środowisku Eclipse
Eclipse jest jednym z najczęściej używanych zintegrowanych środowisk projektowych (IDE, integrated development environment) przez programistów języka Java. W tym artykule omówiono konfigurowanie środowiska projektowego Eclipse do pracy z usługą Azure Service Fabric. Dowiedz się, jak zainstalować wtyczkę usługi Service Fabric oraz utworzyć aplikację usługi Service Fabric i wdrożyć ją w lokalnym lub zdalnym klastrze usługi Service Fabric w środowisku Eclipse. 

> [!NOTE]
> Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

> [!IMPORTANT]
> Upewnij się, że JDK 8 jest zainstalowany w systemie i wybrany w obszarze przezaćmienie.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalowanie lub aktualizowanie wtyczki usługi Service Fabric w środowisku Eclipse
W środowisku Eclipse można zainstalować wtyczkę usługi Service Fabric. Wtyczka może pomóc w uproszczeniu procesu kompilowania i wdrażania usług Java.

> [!IMPORTANT]
> Wtyczka usługi Service Fabric wymaga środowiska Eclipse Neon lub nowszej wersji. Aby sprawdzić wersję środowiska Eclipse, zobacz poniższe instrukcje. Jeśli masz zainstalowaną starszą wersję środowiska Eclipse, możesz pobrać nowsze wersje z [witryny środowiska Eclipse](https://www.eclipse.org). Nie zaleca się instalowania nowej wersji na istniejącej instalacji środowiska Eclipse. Można usunąć starszą wersję przed uruchomieniem instalatora lub zainstalować nowszą wersję w innym katalogu. 
> 
> W systemie Ubuntu zaleca się instalowanie bezpośrednio z witryny środowiska Eclipse, a nie za pomocą instalatora pakietu (`apt` lub `apt-get`). Gwarantuje to uzyskanie najnowszej wersji środowiska Eclipse. 

Zainstaluj program Eclipse Neon lub nowszy z [witryny programu Eclipse](https://www.eclipse.org).  Zainstaluj także wersję 2.2.1 lub nowszą wtyczki Buildship (wtyczka usługi Service Fabric nie jest zgodna ze starszymi wersjami wtyczki Buildship):
-   Aby sprawdzić wersje zainstalowanych składników, w programie zaćmienie przejdź do **pomocy**  >  **dotyczącej** przechodzenia do  >  **szczegółów instalacji**.
-   Aby zaktualizować zestaw Buildship, zobacz [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: wtyczki środowiska Eclipse dla narzędzia Gradle).
-   Aby sprawdzić i zainstalować aktualizacje dla programu zaćmienie, przejdź do obszaru **Pomoc**  >  **Sprawdź aktualizacje**.

Zainstaluj wtyczkę Service Fabric, w obszarze zaćmienie przejdź do pozycji **Pomoc**  >  **Instalowanie nowego oprogramowania**.
1. W polu **Pracuj z** wprowadź wartość https: \/ /DL.Microsoft.com/Eclipse.
2. Kliknij pozycję **Dodaj**.

   ![Wtyczka usługi Service Fabric dla środowiska Eclipse][sf-eclipse-plugin-install]
3. Wybierz wtyczkę usługi Service Fabric, a następnie kliknij przycisk **Next** (Dalej).
4. Wykonaj kroki instalacji, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.

Jeśli wtyczka usługi Service Fabric jest już zainstalowana, zainstaluj najnowszą wersję. 
1. Aby sprawdzić dostępność dostępnych aktualizacji, przejdź do **pomocy**  >  **dotyczącej** przechodzenia do  >  **szczegółów instalacji**. 
2. Na liście zainstalowanych wtyczek wybierz usługę Service Fabric, a następnie kliknij pozycję **Update** (Aktualizuj). Dostępne aktualizacje zostaną zainstalowane.
3. Po zaktualizowaniu wtyczki usługi Service Fabric odśwież także projekt narzędzia Gradle.  Kliknij prawym przyciskiem myszy pozycję **build.gradle**, a następnie wybierz polecenie **Odśwież**.

> [!NOTE]
> Jeśli instalowanie lub aktualizowanie wtyczki usługi Service Fabric przebiega wolno, przyczyną może być ustawienie środowiska Eclipse. Środowisko Eclipse zbiera metadane dotyczące wszystkich zmian w witrynach aktualizowania, które są zarejestrowane w wystąpieniu środowiska Eclipse. Aby przyspieszyć proces sprawdzania i instalowania aktualizacji wtyczki usługi Service Fabric, wybierz pozycję **Available Software Sites** (Dostępne witryny z oprogramowaniem). Wyczyść pola wyboru dla wszystkich lokacji z wyjątkiem tej, która wskazuje na Service Fabric lokalizację wtyczki (https: \/ /DL.Microsoft.com/Eclipse/Azure/servicefabric).

> [!NOTE]
>Jeśli środowisko Eclipse nie działa zgodnie z oczekiwaniami na komputerze Mac (lub wymagane są uprawnienia administratora), przejdź do folderu **ECLIPSE_INSTALLATION_PATH**, a następnie do podfolderu **Eclipse.app/Contents/MacOS**. Uruchom środowisko Eclipse, uruchamiając element `./eclipse`.

## <a name="create-a-service-fabric-application-in-eclipse"></a>Tworzenie aplikacji usługi Service Fabric w środowisku Eclipse

1.  W obszarze zaćmienie przejdź do pozycji **plik**  >  **nowe**  >  **inne**. Wybierz pozycję **Service Fabric Project** (Projekt usługi Service Fabric), a następnie kliknij przycisk **Next** (Dalej).

    ![Nowy projekt usługi Service Fabric — strona 1][create-application/p1]

2.  Wprowadź nazwę projektu, a następnie kliknij przycisk **Next** (Dalej).

    ![Nowy projekt usługi Service Fabric — strona 2][create-application/p2]

3.  Na liście szablonów wybierz pozycję **Service Template** (Szablon usługi). Wybierz typ szablonu usługi — Actor (Aktor), Stateless (Bezstanowa), Container (Kontener) lub Guest Binary (Binarna gościa) — a następnie kliknij przycisk **Next** (Dalej).

    ![Nowy projekt usługi Service Fabric — strona 3][create-application/p3]

4.  Wprowadź nazwę usługi i jej szczegóły, a następnie kliknij przycisk **Finish** (Zakończ).

    ![Nowy projekt usługi Service Fabric — strona 4][create-application/p4]

5. Podczas tworzenia pierwszego projektu usługi Service Fabric w oknie dialogowym **Open Associated Perspective** (Otwieranie skojarzonej perspektywy) kliknij przycisk **Yes** (Tak).

    ![Nowy projekt usługi Service Fabric — strona 5][create-application/p5]

6.  Nowy projekt wygląda następująco:

    ![Nowy projekt usługi Service Fabric — strona 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Kompilowanie aplikacji Service Fabric w programie zaćmienie

1.  Kliknij prawym przyciskiem myszy nową aplikację usługi Service Fabric, a następnie wybierz polecenie **Service Fabric**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

2. W menu kontekstowym wybierz jedną z następujących opcji:
    -   Aby skompilować aplikację bez czyszczenia, kliknij opcję **Build Application** (Kompiluj aplikację).
    -   Aby skompilować aplikację z wcześniejszym czyszczeniem, kliknij opcję **Rebuild Application** (Kompiluj ponownie aplikację).
    -   Aby wyczyścić aplikację z artefaktów kompilacji, kliknij opcję **Clean Application** (Wyczyść aplikację).

## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Wdrażanie aplikacji Service Fabric w klastrze lokalnym przy użyciu programu zaćmienie

Po skompilowaniu aplikacji Service Fabric wykonaj następujące kroki, aby wdrożyć ją w klastrze lokalnym.

1. Jeśli klaster lokalny nie został uruchomiony, postępuj zgodnie z instrukcjami w temacie [Konfigurowanie lokalnego klastra](./service-fabric-get-started-linux.md#set-up-a-local-cluster) do uruchamiania lokalnego klastra i upewnij się, że jest on uruchomiony.
2. Kliknij prawym przyciskiem myszy aplikację Service Fabric, a następnie wybierz pozycję **Service Fabric**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

3.  Z menu kontekstowego kliknij polecenie **Wdróż aplikację**.
4.  Postęp operacji wdrażania można wykonać w oknie konsoli.
5.  Aby sprawdzić, czy aplikacja jest uruchomiona, Otwórz Service Fabric Explorer w klastrze lokalnym w oknie przeglądarki `http://localhost:19080/Explorer` . Rozwiń węzeł **aplikacje** i upewnij się, że aplikacja jest uruchomiona. 

Aby dowiedzieć się, jak debugować aplikację w programie zaćmienie przy użyciu klastra lokalnego, zobacz [debugowanie usługi Java w programie zaćmienie](./service-fabric-debugging-your-application-java.md).

Aplikację można również wdrożyć w klastrze lokalnym za pomocą polecenia **Publikuj aplikację** :

1. Kliknij prawym przyciskiem myszy aplikację Service Fabric, a następnie wybierz pozycję **Service Fabric**.
2. Z menu kontekstowego kliknij polecenie **Publikuj aplikację.**...
3. W oknie **publikowanie aplikacji** wybierz pozycję **PublishProfiles/Local.js** jako profil docelowy, a następnie kliknij przycisk **Publikuj**.

    ![Okno dialogowe publikowania — lokalnie](./media/service-fabric-get-started-eclipse/localjson.png)

    Domyślnie Local.jsw profilu publikowania jest skonfigurowany do publikowania w klastrze lokalnym. Więcej informacji o parametrach połączenia i punktu końcowego znajdujących się w profilach publikowania znajduje się w następnej sekcji.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publikowanie aplikacji Service Fabric na platformie Azure przy użyciu przezaćmienia

Aby opublikować aplikację w chmurze, wykonaj następujące kroki:

1. Aby opublikować aplikację w bezpiecznym klastrze w chmurze, wymagany jest certyfikat X. 509 do użycia w celu komunikowania się z klastrem. W środowiskach testowych i programistycznych używany certyfikat jest często certyfikatem klastra. W środowiskach produkcyjnych certyfikat powinien być certyfikatem klienta, który różni się od certyfikatu klastra. Wymagany jest zarówno certyfikat, jak i klucz prywatny. Plik certyfikatu (i klucz) musi być w formacie PEM. Można utworzyć plik PEM zawierający certyfikat i klucz prywatny z pliku PFX przy użyciu następującego polecenia OpenSSL:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Jeśli plik PFX nie jest chroniony hasłem, użyj `--passin pass:` dla ostatniego parametru.

2. Otwórz **Cloud.jsw** pliku w katalogu **PublishProfiles** . Należy skonfigurować odpowiednie dla klastra punkt końcowy i poświadczenia zabezpieczeń.

   - `ConnectionIPOrURL`Pole zawiera adres IP lub adres URL klastra. Należy zauważyć, że wartość nie zawiera schematu adresu URL ( `https://` ).
   - Domyślnie `ConnectionPort` pole powinno być `19080` , chyba że został on jawnie zmieniony dla klastra.
   - `ClientKey`Pole powinno wskazywać na plik PEM lub Key o formacie PEM na komputerze lokalnym, który zawiera klucz prywatny certyfikatu klienta lub klastra.
   - `ClientCert`Pole powinno wskazywać na plik PEM lub CRT o formacie PEM na komputerze lokalnym, który zawiera dane certyfikatu dla klienta lub klastra. certyfikatu. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Kliknij prawym przyciskiem myszy aplikację Service Fabric, a następnie wybierz pozycję **Service Fabric**.
3. Z menu kontekstowego kliknij polecenie **Publikuj aplikację.**...
3. W oknie **publikowanie aplikacji** wybierz pozycję **PublishProfiles/Cloud.js** jako profil docelowy, a następnie kliknij przycisk **Publikuj**.

    ![Okno dialogowe publikowania — chmura](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Postęp operacji publikowania można wykonać w oknie konsoli.
5. Aby sprawdzić, czy aplikacja jest uruchomiona, Otwórz Service Fabric Explorer w klastrze platformy Azure w oknie przeglądarki. Dla powyższego przykładu będzie to: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer` . Rozwiń węzeł **aplikacje** i upewnij się, że aplikacja jest uruchomiona. 

Jeśli aplikacja zawiera Reliable Services usług w bezpiecznych klastrach systemu Linux, należy również skonfigurować certyfikat, którego usługi mogą używać do wywoływania interfejsów API środowiska uruchomieniowego Service Fabric. Aby dowiedzieć się więcej, zobacz [Konfigurowanie aplikacji Reliable Services do uruchamiania w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Aby dowiedzieć się, jak wdrożyć aplikację Service Fabric Reliable Services zapisaną w języku Java do bezpiecznego klastra z systemem Linux, zobacz [Szybki Start: wdrażanie aplikacji Java Reliable Services](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Wdrażanie aplikacji Service Fabric przy użyciu konfiguracji uruchamiania usługi zaćmienie

Alternatywnym sposobem wdrażania aplikacji usługi Service Fabric jest użycie konfiguracji uruchamiania środowiska Eclipse.

1. W obszarze zaćmienie przejdź do pozycji **Uruchom**  >  **konfiguracje** uruchomieniowe.
2. W obszarze **Gradle Project** (Projekt narzędzia Gradle) wybierz konfigurację uruchamiania **ServiceFabricDeployer**.
3. W prawym okienku na karcie **argumenty** upewnij się, że parametry **IP**, **port**, **clientCert** i **clientKey** są odpowiednio ustawione dla danego wdrożenia. Domyślnie parametry są ustawiane jako wdrożone w klastrze lokalnym tak jak na poniższym zrzucie ekranu. Aby opublikować aplikację na platformie Azure, możesz zmodyfikować parametry w taki sposób, aby zawierały szczegóły punktu końcowego i poświadczenia zabezpieczeń dla klastra platformy Azure. Aby uzyskać więcej informacji, zobacz poprzednią sekcję, [Publikuj swoją aplikację Service Fabric na platformie Azure przy użyciu](#publish-your-service-fabric-application-to-azure-with-eclipse)przewyższania poziomu.

    ![Uruchom okno dialogowe konfiguracji lokalnie](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Upewnij się, że **katalog roboczy** wskazuje aplikację, którą chcesz wdrożyć. Aby zmienić aplikację, kliknij przycisk **Workspace** (Obszar roboczy) i wybierz odpowiednią aplikację.
6. Kliknij przycisk **Apply** (Zastosuj), a następnie kliknij przycisk **Run** (Uruchom).

Aplikacja zostanie skompilowana i wdrożona w ciągu kilku minut. Stan wdrażania możesz monitorować w narzędziu Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Dodawanie usługi Service Fabric do aplikacji usługi Service Fabric

Aby dodać usługę Service Fabric do istniejącej aplikacji usługi Service Fabric, wykonaj następujące kroki:

1.  Kliknij prawym przyciskiem myszy projekt, do którego chcesz dodać usługę, a następnie kliknij polecenie **Service Fabric**.

    ![Dodawanie usługi Service Fabric — strona 1][add-service/p1]

2.  Kliknij pozycję **Add Service Fabric Service** (Dodaj usługę Service Fabric) i wykonaj zestaw czynności, aby dodać usługę do projektu.
3.  Wybierz szablon usługi, który chcesz dodać do projektu, a następnie kliknij przycisk **Next** (Dalej).

    ![Dodawanie usługi Service Fabric — strona 2][add-service/p2]

4.  Wprowadź nazwę usługi (i inne szczegóły, zgodnie z potrzebami), a następnie kliknij przycisk **Add Service** (Dodaj usługę) przycisku.  

    ![Dodawanie usługi Service Fabric — strona 3][add-service/p3]

5.  Po dodaniu usługi ogólna struktura projektu powinna wyglądać podobnie do poniższej:

    ![Dodawanie usługi Service Fabric — strona 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Edytowanie wersji manifestu aplikacji Java usługi Service Fabric

Aby edytować wersje manifestu, kliknij prawym przyciskiem myszy projekt, przejdź do lokalizacji **Service Fabric** i wybierz z menu rozwijanego pozycję **Edytuj wersje manifestu**. W kreatorze możesz zaktualizować wersje manifestu aplikacji i manifestu usługi oraz wersje pakietów **Kod**, **Konfiguracja** i **Dane**.

Jeśli zaznaczysz opcję **Automatycznie aktualizuj wersje aplikacji i usługi**, a następnie zaktualizujesz wersję, wersje manifestu zostaną automatycznie zaktualizowane. Przykład: najpierw musisz zaznaczyć pole wyboru, a następnie zaktualizować wersję pakietu **Kod** z 0.0.0 na 0.0.1 i kliknąć przycisk **Zakończ**. Następnie wersja manifestu usługi i wersja manifestu aplikacji zostaną automatycznie zaktualizowane do wersji 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Uaktualnianie aplikacji Java usługi Service Fabric

Na potrzeby scenariusza uaktualniania przyjmijmy, że utworzono projekt **App1** za pomocą wtyczki usługi Service Fabric w środowisku Eclipse. Wdrożono go za pomocą wtyczki w celu utworzenia aplikacji o nazwie **fabric:/App1Application**. Typ aplikacji to **App1ApplicationType**, a wersja aplikacji to 1,0. Teraz chcesz uaktualnić aplikację bez przerw w dostępności.

Najpierw wprowadź zmiany w aplikacji i ponownie skompiluj zmodyfikowaną usługę. Zaktualizuj plik manifestu zmodyfikowanej usługi (ServiceManifest.xml) za pomocą zaktualizowanych wersji usługi (i kodu, konfiguracji lub danych — odpowiednio do potrzeb). Zmodyfikuj także manifest aplikacji (ApplicationManifest.xml), określając zaktualizowany numer wersji dla aplikacji i zmodyfikowaną usługę.  

Aby uaktualnić aplikację przy użyciu środowiska Eclipse, możesz utworzyć zduplikowany profil konfiguracji uruchamiania. Następnie użyj go do uaktualnienia aplikacji zgodnie z potrzebami.

1.  Przejdź do pozycji **Uruchom**  >  **konfiguracje** uruchomieniowe. W lewym okienku kliknij małą strzałkę po lewej stronie pozycji **Gradle Project** (Projekt narzędzia Gradle).
2.  Kliknij prawym przyciskiem myszy pozycję **ServiceFabricDeployer** i wybierz polecenie **Duplicate** (Duplikuj). Wprowadź nową nazwę dla tej konfiguracji, na przykład **ServiceFabricUpgrader**.
3.  W prawym panelu na karcie **Arguments** (Argumenty) zmień wartość **-Pconfig='deploy'** na wartość **-Pconfig='upgrade'** i kliknij przycisk **Apply** (Zastosuj).

Ten proces umożliwia utworzenie i zapisanie profilu konfiguracji uruchamiania, za pomocą którego można w dowolnym momencie uaktualnić aplikację. W jego ramach pobierana jest również najnowsza zaktualizowana wersja typu aplikacji z pliku manifestu aplikacji.

Uaktualnienie aplikacji zajmuje kilka minut. Narzędzie Service Fabric Explorer umożliwia monitorowanie uaktualniania aplikacji.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrowanie starych aplikacji Java usługi Service Fabric na potrzeby użycia z narzędziem Maven
Ostatnio przenieśliśmy biblioteki Java usługi Service Fabric z zestawu SDK Java usługi Service Fabric do repozytorium narzędzia Maven. Nowe aplikacje generowane za pomocą środowiska Eclipse będą generować najnowsze, zaktualizowane projekty (mogące działać z narzędziem Maven), ale możesz zaktualizować swoje istniejące bezstanowe aplikacje Java lub aplikacje Java aktora usługi Service Fabric, które wcześniej korzystały z zestawu SDK Java usługi Service Fabric, aby używały zależności języka Java usługi Service Fabric z narzędzia Maven. Wykonaj kroki wymienione [tutaj](service-fabric-migrate-old-javaapp-to-use-maven.md), aby zapewnić działanie Twojej starszej aplikacji z narzędziem Maven.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z szybkimi krokami dotyczącymi tworzenia aplikacji niezawodnej usługi Java i wdrażania jej lokalnie i na platformie Azure, zobacz [Szybki Start: wdrażanie aplikacji Java Reliable Services](./service-fabric-quickstart-java-reliable-services.md).
- Aby dowiedzieć się, jak debugować aplikację Java w klastrze lokalnym, zobacz [debugowanie usługi Java w programie zaćmienie](./service-fabric-debugging-your-application-java.md).
- Aby dowiedzieć się, jak monitorować i diagnozować Service Fabric aplikacji, zobacz [monitorowanie i diagnozowanie usług w konfiguracji tworzenia maszyn lokalnych](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
