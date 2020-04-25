---
title: Jak używać Hudson z usługą BLOB Storage | Microsoft Docs
description: Opisuje sposób używania Hudson z magazynem obiektów blob platformy Azure jako repozytorium dla artefaktów kompilacji.
services: storage
author: tamram
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e3b76e9ded7031ce104df9251a851e5e8e430ee4
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133769"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Korzystanie z usługi Azure Storage z rozwiązaniem ciągłej integracji Hudson
## <a name="overview"></a>Omówienie
Poniższe informacje pokazują, jak używać magazynu obiektów BLOB jako repozytorium artefaktów kompilacji utworzonych przez rozwiązanie Hudson ciągłej integracji (CI) lub jako źródło plików do pobrania, które mają być używane w procesie kompilacji. Jednym z scenariuszy, w których można to użyć, jest kodowanie w środowisku deweloperskim Agile (przy użyciu języka Java lub innych języków), kompilacje są uruchamiane na podstawie ciągłej integracji i potrzebne jest repozytorium dla artefaktów kompilacji, dzięki czemu można na przykład udostępnić je innym członkom organizacji, klientom lub obsłudze archiwum.  Innym scenariuszem jest to, że zadanie kompilacji wymaga innych plików, na przykład zależności do pobrania w ramach danych wejściowych kompilacji.

W tym samouczku będziesz używać wtyczki usługi Azure Storage dla Hudson CI udostępnionej przez firmę Microsoft.

## <a name="introduction-to-hudson"></a>Wprowadzenie do Hudson
Hudson zapewnia ciągłą integrację projektu oprogramowania przez umożliwienie deweloperom łatwej integracji zmian w kodzie i tworzenia kompilacji automatycznie i często, zwiększając jednocześnie produktywność deweloperów. Kompilacje są w wersji i artefakty kompilacji można przekazać do różnych repozytoriów. W tym artykule przedstawiono sposób użycia usługi Azure Blob Storage jako repozytorium artefaktów kompilacji. Pokazuje również, jak pobrać zależności z usługi Azure Blob Storage.

Więcej informacji na temat Hudson można znaleźć pod adresem [Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Zalety korzystania z Blob service
Korzyści wynikające z używania Blob service do hostowania artefaktów kompilacji Agile obejmują:

* Wysoka dostępność artefaktów kompilacji i/lub możliwe do pobrania zależności.
* Wydajność, gdy rozwiązanie Hudson CI przekazuje artefakty kompilacji.
* Wydajność, gdy klienci i partnerzy pobierają artefakty kompilacji.
* Kontrola nad zasadami dostępu użytkowników przy użyciu opcji dostępu anonimowego, dostępu do sygnatur dostępu współdzielonego opartego na wygaśnięciu, dostępu prywatnego itp.

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć Blob service z rozwiązaniem Hudson CI, potrzebne są następujące elementy:

* Rozwiązanie Hudson ciągłej integracji.
  
    Jeśli obecnie nie masz rozwiązania CI Hudson, możesz uruchomić rozwiązanie Hudson CI, korzystając z następującej techniki:
  
  1. Na maszynie z obsługą języka Java [Pobierz plik War Hudson](https://www.eclipse.org/hudson/download.php).
  2. W wierszu polecenia otwartym w folderze zawierającym WAR Hudson Uruchom Hudson WAR. Na przykład jeśli pobrano wersję 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. W przeglądarce Otwórz `http://localhost:8080/`program. Spowoduje to otwarcie pulpitu nawigacyjnego Hudson.
  4. Po pierwszym użyciu Hudson, wykonaj konfigurację początkową pod adresem `http://localhost:8080/`.
  5. Po zakończeniu wstępnej konfiguracji Anuluj uruchomione wystąpienie wojny Hudson, ponownie uruchom Hudson WAR, a następnie otwórz pulpit nawigacyjny Hudson, `http://localhost:8080/`który zostanie użyty do zainstalowania i skonfigurowania wtyczki usługi Azure Storage.
     
      W przypadku typowego rozwiązania CI Hudson można skonfigurować do uruchamiania jako usługa, a w wierszu polecenia musi być wystarczająca wartość Hudson War.
* Konto platformy Azure. Konto platformy Azure można zarejestrować pod adresem <https://www.azure.com>.
* Konto usługi Azure Storage. Jeśli nie masz jeszcze konta magazynu, możesz utworzyć je, wykonując czynności opisane w sekcji [Tworzenie konta magazynu](../common/storage-account-create.md).
* Znajomość rozwiązania Hudson CI jest zalecana, ale nie jest wymagana, ponieważ Poniższa zawartość będzie używać przykładu podstawowego do wyświetlania kroków potrzebnych podczas korzystania z Blob service jako repozytorium dla artefaktów kompilacji Hudson CI.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Jak używać Blob service z Hudsoną CI
Aby użyć Blob service z Hudson, należy zainstalować wtyczkę usługi Azure Storage, skonfigurować wtyczkę do korzystania z konta magazynu, a następnie utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu. Te kroki opisano w poniższych sekcjach.

## <a name="how-to-install-the-azure-storage-plugin"></a>Jak zainstalować wtyczkę usługi Azure Storage
1. Na pulpicie nawigacyjnym Hudson kliknij pozycję **Zarządzaj Hudson**.
2. Na stronie **Zarządzanie Hudson** kliknij pozycję **Zarządzaj wtyczkami**.
3. Kliknij kartę **dostępne** .
4. Kliknij pozycję **inne**.
5. W sekcji **operacje przekazywania artefaktów** wybierz pozycję **wtyczka Microsoft Azure Storage**.
6. Kliknij przycisk **Instaluj**.
7. Po zakończeniu instalacji uruchom ponownie program Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Jak skonfigurować wtyczkę usługi Azure Storage pod kątem korzystania z konta magazynu
1. Na pulpicie nawigacyjnym Hudson kliknij pozycję **Zarządzaj Hudson**.
2. Na stronie **Zarządzanie Hudson** kliknij pozycję **Konfiguruj system**.
3. W sekcji **Konfiguracja konta Microsoft Azure Storage** :
   
    a. Wprowadź nazwę konta magazynu, którą można uzyskać z [Azure Portal](https://portal.azure.com).
   
    b. Wprowadź klucz konta magazynu, który można również uzyskać z [Azure Portal](https://portal.azure.com).
   
    d. Jeśli używasz globalnej chmury platformy Azure, użyj wartości domyślnej dla **adresu URL punktu końcowego usługi BLOB Service** . Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego określonego w [Azure Portal](https://portal.azure.com) dla konta magazynu.
   
    d. Kliknij pozycję **Weryfikuj poświadczenia magazynu** , aby zweryfikować konto magazynu.
   
    e. Obowiązkowe Jeśli masz dodatkowe konta magazynu, które chcesz udostępnić Hudson CI, kliknij pozycję **Dodaj więcej kont magazynu**.
   
    f. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Jak utworzyć akcję po kompilacji, która przekazuje artefakty kompilacji do konta magazynu
Aby uzyskać instrukcje, najpierw należy utworzyć zadanie, które spowoduje utworzenie kilku plików, a następnie dodanie go do akcji po kompilacji w celu przekazania plików na konto magazynu.

1. Na pulpicie nawigacyjnym Hudson kliknij pozycję **nowe zadanie**.
2. Nazwij **MyJob**zadania, kliknij pozycję **Kompiluj zadanie oprogramowania w dowolnym miejscu**, a następnie kliknij przycisk **OK**.
3. W sekcji **kompilacja** w obszarze Konfiguracja zadania kliknij pozycję **Dodaj krok kompilacji** i wybierz **polecenie Uruchom zadanie wsadowe systemu Windows**.
4. W **poleceniu**Użyj następujących poleceń:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. W sekcji **Akcje po kompilacji** w obszarze Konfiguracja zadania kliknij pozycję **Przekaż artefakty do Microsoft Azure Storage BLOB**.
6. W polu **nazwa konta magazynu**wybierz konto magazynu, które ma być używane.
7. W polu **nazwa kontenera**Określ nazwę kontenera. (Kontener zostanie utworzony, jeśli jeszcze nie istnieje, gdy artefakty kompilacji są przekazywane). Możesz użyć zmiennych środowiskowych, więc w tym przykładzie wprowadź **$ {JOB_NAME}** jako nazwę kontenera.
   
    **Wyowietlon**
   
    Poniżej sekcji **polecenie** , w której wprowadzono skrypt do **wykonywania polecenia Windows Batch** jest łączem do zmiennych środowiskowych rozpoznawanych przez Hudson. Kliknij ten link, aby poznać nazwy zmiennych środowiskowych i opisy. Zmienne środowiskowe, które zawierają znaki specjalne, takie jak **BUILD_URL** zmienna środowiskowa, nie mogą być nazwami kontenerów ani wspólną ścieżką wirtualną.
8. Kliknij pozycję **Utwórz nowy kontener jako publiczny domyślnie** dla tego przykładu. (Jeśli chcesz użyć prywatnego kontenera, musisz utworzyć sygnaturę dostępu współdzielonego, aby zezwolić na dostęp. Wykracza to poza zakres tego artykułu. Więcej informacji na temat sygnatur dostępu współdzielonego można uzyskać przy [użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
9. Obowiązkowe Kliknij przycisk **Oczyść kontener przed przekazaniem** , jeśli chcesz, aby kontener został usunięty z zawartości przed przekazaniem artefaktów kompilacji (pozostaw to pole niezaznaczone, jeśli nie chcesz czyścić zawartości kontenera).
10. Aby uzyskać **listę artefaktów do przekazania**, wprowadź **tekst/*. txt**.
11. W przypadku **wspólnej ścieżki wirtualnej dla przekazanych artefaktów**wprowadź **$\_{ID kompilacji}/$\_{numer kompilacji}**.
12. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.
13. Na pulpicie nawigacyjnym Hudson kliknij pozycję **Kompiluj teraz** , aby uruchomić **MyJob**. Przejrzyj dane wyjściowe konsoli dla stanu. Komunikaty o stanie usługi Azure Storage zostaną uwzględnione w danych wyjściowych konsoli, gdy akcja po kompilacji zacznie przekazywać artefakty kompilacji.
14. Po pomyślnym zakończeniu zadania można przeanalizować artefakty kompilacji, otwierając publiczny obiekt BLOB.
    
    a. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    
    b. Kliknij pozycję **Magazyn**.
    
    d. Kliknij nazwę konta magazynu, która została użyta dla Hudson.
    
    d. Kliknij pozycję **Kontenery**.
    
    e. Kliknij kontener o nazwie **myjob**, który jest małymi wersjami nazwy zadania przypisanego podczas tworzenia zadania Hudson. Nazwy kontenerów i nazw obiektów BLOB są małymi literami (i rozróżniana wielkość liter) w usłudze Azure Storage. Na liście obiektów BLOB kontenera o nazwie **myjob** powinna zostać wyświetlona wartość **Hello. txt** i **Date. txt**. Skopiuj adres URL dla każdego z tych elementów i otwórz go w przeglądarce. Zobaczysz plik tekstowy, który został przekazany jako artefakt kompilacji.

Dla każdego zadania można utworzyć tylko jedną akcję po kompilacji, która przekazuje artefakty do magazynu obiektów blob platformy Azure. Pojedyncza Akcja po kompilacji w celu przekazania artefaktów do usługi Azure Blob Storage może określać różne pliki (w tym symbole wieloznaczne) i ścieżki do plików znajdujących się na **liście artefaktów do przekazania** przy użyciu średnika jako separatora. Na przykład jeśli kompilacja Hudson tworzy pliki JAR i TXT w folderze **kompilacji** obszaru roboczego i chcesz przekazać oba do usługi Azure Blob Storage, użyj następujących elementów, aby uzyskać **listę artefaktów do przekazania** wartości: **Build/\*. jar; Build/\*. txt**. Aby określić ścieżkę do użycia w nazwie obiektu BLOB, można również użyć składni podwójnego dwukropka. Na przykład jeśli chcesz, aby JARs przekazywać dane przy użyciu plików **binarnych** w ścieżce obiektu BLOB i plików txt do przekazania przy użyciu **powiadomień** w ścieżce obiektu BLOB, użyj następujących **elementów dla listy artefaktów do przekazania** wartości: **Build/\*. jar:: binarne; Build/\*. txt:: uwagi**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Jak utworzyć krok kompilacji pobierany z usługi Azure Blob Storage
Poniższe kroki pokazują, jak skonfigurować krok kompilacji, aby pobierać elementy z usługi Azure Blob Storage. Może to być przydatne, jeśli chcesz dołączyć elementy do kompilacji, na przykład JARs, że przechowujesz w usłudze Azure Blob Storage.

1. W sekcji **kompilacja** w obszarze Konfiguracja zadania kliknij pozycję **Dodaj krok kompilacji** , a następnie wybierz pozycję **Pobierz z usługi Azure Blob Storage**.
2. W polu **nazwa konta magazynu**wybierz konto magazynu, które ma być używane.
3. W polu **nazwa kontenera**Określ nazwę kontenera zawierającego obiekty blob, które chcesz pobrać. Można używać zmiennych środowiskowych.
4. W polu **Nazwa obiektu BLOB**Określ nazwę obiektu BLOB. Można używać zmiennych środowiskowych. Ponadto można użyć gwiazdki jako symbolu wieloznacznego po określeniu początkowych liter nazwy obiektu BLOB. Na przykład **projekt\\*** będzie określać wszystkie obiekty blob, których nazwy zaczynają się od **Project**.
5. Obowiązkowe W polu **ścieżka do pobierania**określ ścieżkę na maszynie Hudson, w której chcesz pobrać pliki z usługi Azure Blob Storage. Można również użyć zmiennych środowiskowych. (Jeśli nie podasz wartości **ścieżki pobierania**, pliki z usługi Azure Blob Storage zostaną pobrane do obszaru roboczego zadania).

Jeśli masz dodatkowe elementy, które chcesz pobrać z usługi Azure Blob Storage, możesz utworzyć dodatkowe kroki kompilacji.

Po uruchomieniu kompilacji można sprawdzić dane wyjściowe konsoli historia kompilacji lub poszukać lokalizacji pobierania, aby sprawdzić, czy obiekty blob, których oczekiwano, zostały pobrane pomyślnie.

## <a name="components-used-by-the-blob-service"></a>Składniki używane przez Blob service
Poniżej przedstawiono omówienie składników Blob service.

* **Konto magazynu**: cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Jest to najwyższy poziom przestrzeni nazw do uzyskiwania dostępu do obiektów BLOB. Konto może zawierać nieograniczoną liczbę kontenerów, o ile ich łączny rozmiar przekracza 100 TB.
* **Kontener**: kontener zawiera grupowanie zestawu obiektów BLOB. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob.
* **BLOB**: plik dowolnego typu i rozmiaru. Istnieją dwa typy obiektów blob, które mogą być przechowywane w usłudze Azure Storage: blokowe i stronicowe obiekty blob. Większość plików to blokowe obiekty blob. Pojedynczy blokowy obiekt BLOB może mieć rozmiar do 200 GB. W tym samouczku są stosowane blokowe obiekty blob. Stronicowe obiekty blob, inne typy obiektów blob, mogą mieć rozmiar do 1 TB i są bardziej wydajne, gdy często są modyfikowane zakresy bajtów w pliku. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [Opis blokowych obiektów blob, dołączania obiektów blob i stronicowych obiektów BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Format adresu URL**: obiekty blob są adresowane przy użyciu następującego formatu adresu URL:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Powyższy format dotyczy globalnej chmury platformy Azure. Jeśli używasz innej chmury platformy Azure, użyj punktu końcowego w [Azure Portal](https://portal.azure.com) , aby określić punkt końcowy adresu URL.)
  
    W powyższym formacie `storageaccount` reprezentuje nazwę konta magazynu, `container_name` reprezentuje nazwę kontenera i `blob_name` reprezentuje odpowiednio nazwę obiektu BLOB. W obrębie nazwy kontenera można mieć wiele ścieżek oddzielonych ukośnikiem **/**. Przykładowa nazwa kontenera w tym samouczku została **MyJoba**, a **$\_{Build ID}/$\_{build number}** została użyta dla wspólnej ścieżki wirtualnej, co spowodowało, że obiekt BLOB ma adres URL w następującej postaci:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Następne kroki
* [Poznaj Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).
