---
title: Typowe kody błędów maszyn wirtualnych na platformie Azure | Microsoft Docs
description: Informacje o typowych kodach błędów napotkanych podczas aprowizacji i zarządzania maszynami wirtualnymi na platformie Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 52508a6820ce0cbbbe3a0341a99894f8b92b1645
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87831230"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Omówienie typowych komunikatów o błędach występujących podczas zarządzania maszynami wirtualnymi na platformie Azure

W tym artykule opisano niektóre najczęstsze kody błędów i komunikaty, które mogą wystąpić podczas tworzenia maszyn wirtualnych i zarządzania nimi na platformie Azure.

>[!NOTE]
> Możesz pozostawić Komentarze na tej stronie, aby uzyskać informacje zwrotne lub opinię na temat [platformy Azure](https://feedback.azure.com/forums/216843-virtual-machines) za pomocą tagu #azerrormessage.

## <a name="error-response-format"></a>Format odpowiedzi na błędy 
Maszyny wirtualne platformy Azure używają następującego formatu JSON w celu uzyskania odpowiedzi na błąd:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Odpowiedź na błąd zawsze zawiera kod stanu i obiekt błędu. Każdy obiekt błędu zawsze zawiera kod błędu i komunikat. Jeśli maszyna wirtualna jest tworzona przy użyciu szablonu, obiekt błędu zawiera również sekcję szczegółów zawierającą wewnętrzny poziom kodów błędów i komunikatów. Zwykle najbardziej wewnętrznym poziomem komunikatu o błędzie jest błąd główny.


## <a name="common-virtual-machine-management-errors"></a>Typowe błędy dotyczące zarządzania maszynami wirtualnymi

W tej sekcji wymieniono typowe komunikaty o błędach, które można napotkać podczas zarządzania maszynami wirtualnymi:

|  Kod błędu  |  Komunikat o błędzie  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Nie można uzyskać dzierżawy podczas tworzenia dysku " {0} " przy użyciu obiektu BLOB z identyfikatorem URI {1} . Obiekt BLOB jest już używany.  |  
|  AllocationFailed  |  Alokacja nie powiodła się. Spróbuj zmniejszyć rozmiar maszyny wirtualnej lub liczbę maszyn wirtualnych, spróbuj ponownie później lub spróbuj wykonać wdrożenie w innym zestawie dostępności lub w innej lokalizacji platformy Azure.  |  
|  AllocationFailed  |  Alokacja maszyny wirtualnej nie powiodła się z powodu błędu wewnętrznego. Spróbuj ponownie później lub spróbuj przeprowadzić wdrożenie w innej lokalizacji.  |
|  ArtifactNotFound  |  Nie można odnaleźć rozszerzenia maszyny wirtualnej z wydawcą " {0} " i typem " {1} " w lokalizacji " {2} ".  |
|  ArtifactNotFound  |  Nie można odnaleźć rozszerzenia z wydawcy " {0} ", typu " {1} " i wersji programu obsługi typu " {2} " w repozytorium rozszerzeń.  |
|  ArtifactVersionNotFound  |  W repozytorium artefaktów nie znaleziono wersji spełniającej żądaną wersję " {0} ".  |
|  ArtifactVersionNotFound  |  W repozytorium artefaktów nie znaleziono wersji spełniającej żądaną wersję " {0} " dla rozszerzenia maszyny wirtualnej z wydawcą " {1} " i typem " {2} ".  |
|  AttachDiskWhileBeingDetached  |  Nie można dołączyć dysku danych " {0} " do maszyny wirtualnej " {1} ", ponieważ dysk jest obecnie odłączony. Zaczekaj na całkowite odłączenie dysku, a następnie spróbuj ponownie.  |
|  BadRequest  |  Wyrównane zestawy dostępności nie są jeszcze obsługiwane w tym regionie.  |
|  BadRequest  |  Dodanie maszyny wirtualnej z dyskami zarządzanymi do niezarządzanego zestawu dostępności lub dodanie maszyny wirtualnej z dyskami opartymi na obiektach Blob do zarządzanego zestawu dostępności nie jest obsługiwane. Utwórz zestaw dostępności z ustawioną właściwością "Managed", aby dodać maszynę wirtualną z dyskami zarządzanymi.  |
|  BadRequest  |  Managed Disks nie są obsługiwane w tym regionie.  |
|  BadRequest  |  Wiele VMExtensions na procedurę obsługi nie jest obsługiwana dla typu systemu operacyjnego " {0} ". VMExtension " {1} " z obsługą " {2} " została już dodana lub określona w danych wejściowych.  |
|  BadRequest  |  Operacja " {0} " nie jest obsługiwana dla zasobu " {1} " z dyskami zarządzanymi.  |
|  CertificateImproperlyFormatted  |  Reprezentacja JSON klucza tajnego {0} uzyskana z zawiera pole danych, które nie jest poprawnie sformatowanym plikiem pfx lub podane hasło nie dekoduje poprawnie pliku PFX.  |
|  CertificateImproperlyFormatted  |  Dane pobierane z {0} nie są deserializowane w formacie JSON.  |
|  Konflikt  |  Zmienianie rozmiarów dysków jest dozwolone tylko podczas tworzenia maszyny wirtualnej lub cofnięcia przydziału maszyny wirtualnej.  |
|  ConflictingUserInput  |  {0}Nie można dołączyć dysku "", ponieważ dysk jest już własnością maszyny wirtualnej " {1} ".  |
|  ConflictingUserInput  |  Źródłowe i docelowe grupy zasobów są takie same.  |
|  ConflictingUserInput  |  Źródłowe i docelowe konta magazynu dla dysku {0} są różne.  |
|  ContainerAlreadyOnLease  |  Istnieje już dzierżawa w kontenerze magazynu przechowującym obiekt BLOB z identyfikatorem URI {0} .  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Żądanie Move Resources zawiera zasoby magazynu kluczy, do których odwołuje się jeden lub więcej {0} s w żądaniu. Nie jest to obecnie obsługiwane w przypadku przenoszenia między subskrypcjami. Sprawdź szczegóły błędu dotyczące identyfikatorów zasobów magazynu kluczy.  |
|  DiagnosticsOperationInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania profilu diagnostyki maszyny wirtualnej {0} .  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Obiekt BLOB {0} jest już używany przez inny dysk należący do maszyny wirtualnej " {1} ". Można przeanalizować metadane obiektu BLOB dla informacji referencyjnych dysku.  |
|  DiskBlobNotFound  |  Nie można znaleźć obiektu BLOB dysku VHD z identyfikatorem URI {0} dla dysku " {1} ".  |
|  DiskBlobNotFound  |  Nie można odnaleźć obiektu BLOB VHD z identyfikatorem URI {0} .  |
|  DiskEncryptionKeySecretMissingTags  |  {0} wpis tajny nie ma {1} tagów. Zaktualizuj wersję wpisu tajnego, Dodaj wymagane znaczniki i ponów próbę.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Odwinięcie wartości klucza tajnego {0} przy użyciu klucza {1} nie powiodło się.  |
|  DiskImageNotReady  |  Obraz dysku {0} jest w {1} stanie. Spróbuj ponownie, gdy obraz będzie gotowy.  |
|  DiskPreparationError  |  Wystąpił co najmniej jeden błąd podczas przygotowywania dysków maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz widok wystąpienia dysku.  |
|  DiskProcessingError  |  Przetwarzanie dysku zostało zatrzymane, ponieważ maszyna wirtualna zawiera inne dyski z uszkodzonymi dyskami.  |
|  ImageBlobNotFound  |  Nie można znaleźć obiektu BLOB dysku VHD z identyfikatorem URI {0} dla dysku " {1} ".  |
|  ImageBlobNotFound  |  Nie można odnaleźć obiektu BLOB VHD z identyfikatorem URI {0} .  |
|  IncorrectDiskBlobType  |  Obiekty blob na dyskach mogą być typu stronicowego obiektu BLOB. Obiekt BLOB {0} dla dysku " {1} " jest typu blokowego obiektu BLOB.  |
|  IncorrectDiskBlobType  |  Obiekty blob na dyskach mogą być typu stronicowego obiektu BLOB. Obiekt BLOB {0} jest typu " {1} ".  |
|  IncorrectImageBlobType  |  Obiekty blob na dyskach mogą być typu stronicowego obiektu BLOB. Obiekt BLOB {0} dla dysku " {1} " jest typu blokowego obiektu BLOB.  |
|  IncorrectImageBlobType  |  Obiekty blob na dyskach mogą być typu stronicowego obiektu BLOB. Obiekt BLOB {0} jest typu " {1} ".  |
|  InternalOperationError  |  Nie można rozpoznać konta magazynu {0} . Upewnij się, że został on utworzony za pomocą dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  InternalOperationError  |  {0} zadania szukania celu nie powiodły się.  |
|  InternalOperationError  |  Wystąpił błąd podczas sprawdzania poprawności profilu sieciowego maszyny wirtualnej " {0} ".  |
|  InvalidAccountType  |  Wartość AccountType {0} jest nieprawidłowa.  |
|  Nieprawidłowy parametr  |  Wartość parametru {0} jest nieprawidłowa.  |
|  Nieprawidłowy parametr  |  Określone hasło administratora jest niedozwolone.  |
|  Nieprawidłowy parametr  |  "Podane hasło musi mieć długość między {0} {1} znakami i musi spełniać co najmniej {2} wymagania dotyczące złożoności haseł z następujących elementów: <ol><li> Zawiera wielką literę</li><li>Zawiera małą literę</li><li>Zawiera cyfrę numeryczną</li><li>Zawiera znak specjalny.</li></ol>  |
|  Nieprawidłowy parametr  |  Określona nazwa użytkownika administratora jest niedozwolona.  |
|  Nieprawidłowy parametr  |  Nie można dołączyć istniejącego dysku systemu operacyjnego, jeśli maszyna wirtualna została utworzona na podstawie obrazu platformy lub użytkownika.  |
|  Nieprawidłowy parametr  |  Nazwa kontenera {0} jest nieprawidłowa. Nazwy kontenerów muszą mieć długość 3-63 znaków i mogą zawierać tylko małe znaki alfanumeryczne i łącznik. Łącznik musi być poprzedzony znakiem alfanumerycznym.  |
|  Nieprawidłowy parametr  |  Nazwa kontenera {0} w adresie URL {1} jest nieprawidłowa. Nazwy kontenerów muszą mieć długość 3-63 znaków i mogą zawierać tylko małe znaki alfanumeryczne i łącznik. Łącznik musi być poprzedzony znakiem alfanumerycznym.  |
|  Nieprawidłowy parametr  |  Nazwa obiektu BLOB w adresie URL {0} zawiera ukośnik. Jest to obecnie nieobsługiwane w przypadku dysków.  |
|  Nieprawidłowy parametr  |  Identyfikator URI {0} nie wygląda na prawidłowy identyfikator URI obiektu BLOB.  |
|  Nieprawidłowy parametr  |  Dysk o nazwie " {0} " już używa tego samego numeru LUN: {1} .  |
|  Nieprawidłowy parametr  |  Dysk o nazwie " {0} " już istnieje.  |
|  Nieprawidłowy parametr  |  Nie można określić przesłonięć obrazu użytkownika dla dysku już zdefiniowanego w określonym odwołaniu do obrazu.  |
|  Nieprawidłowy parametr  |  Dysk o nazwie " {0} " już używa tego samego adresu URL dysku VHD {1} .  |
|  Nieprawidłowy parametr  |  Określona liczba domen błędów {0} musi należeć do zakresu od {1} do {2} .  |
|  Nieprawidłowy parametr  |  Typ licencji {0} jest nieprawidłowy. Prawidłowe typy licencji to: Windows_Client lub Windows_Server, z uwzględnieniem wielkości liter.  |
|  Nieprawidłowy parametr  |  Nazwa hosta systemu Linux nie może być dłuższa niż {0} znaków ani zawierać następujących znaków: {1} .  |
|  Nieprawidłowy parametr  |  Ścieżka docelowa kluczy publicznych SSH jest obecnie ograniczona do wartości domyślnej {0}  ze względu na znany problem w agencie aprowizacji systemu Linux.  |
|  Nieprawidłowy parametr  |  Dysk w jednostce LUN {0} już istnieje.  |
|  Nieprawidłowy parametr  |  Subskrypcja {0} żądania musi być zgodna z subskrypcją {1} zawartą w identyfikatorze dysku zarządzanego.  |
|  Nieprawidłowy parametr  |  Dane niestandardowe w OSProfile muszą być zakodowane w formacie base64 i mieć maksymalną długość {0} znaków.  |
|  Nieprawidłowy parametr  |  Nazwa obiektu BLOB w adresie URL {0} musi kończyć się {1} rozszerzeniem "".  |
|  Nieprawidłowy parametr  |  {0}' nie jest prawidłowym prefiksem nazwy obiektu BLOB przechwyconego wirtualnego dysku twardego. Prawidłowy prefiks pasuje do wyrażenia regularnego " {1} ".  |
|  Nieprawidłowy parametr  |  Nie można dodać certyfikatów do maszyny wirtualnej, jeśli nie zainicjowano obsługi administracyjnej agenta maszyny wirtualnej.  |
|  Nieprawidłowy parametr  |  Dysk w jednostce LUN {0} już istnieje.  |
|  Nieprawidłowy parametr  |  Nie można utworzyć maszyny wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym zestaw dostępności jest aktualnie przydzielony. Dostępne są następujące rozmiary: {1} . Więcej informacji na temat strategii zmiany rozmiarów maszyny wirtualnej można znaleźć pod adresem https://aka.ms/azure-resizevm .  |
|  Nieprawidłowy parametr  |  Żądany rozmiar maszyny wirtualnej {0} nie jest dostępny w bieżącym regionie. Dostępne są rozmiary w bieżącym regionie: {1} . Dowiedz się więcej na temat dostępnych rozmiarów maszyn wirtualnych w każdym regionie https://aka.ms/azure-regions .  |
|  Nieprawidłowy parametr  |  Żądany rozmiar maszyny wirtualnej {0} nie jest dostępny w bieżącym regionie. Dowiedz się więcej na temat dostępnych rozmiarów maszyn wirtualnych w każdym regionie https://aka.ms/azure-regions .  |
|  Nieprawidłowy parametr  |  Nazwa użytkownika administratora systemu Windows nie może być dłuższa niż {0} znaków, kończyć się kropką (.) lub zawierać następujące znaki: {1} .  |
|  Nieprawidłowy parametr  |  Nazwa komputera z systemem Windows nie może być dłuższa niż {0} znaków, być całkowicie numeryczna ani zawierać następujących znaków: {1} .  |
|  MissingMoveDependentResources  |  Żądanie Move Resources nie zawiera wszystkich zasobów zależnych. Sprawdź szczegóły błędu dotyczące brakujących identyfikatorów zasobów.  |
|  MoveResourcesHaveInvalidState  |  Żądanie Move Resources zawiera maszyny wirtualne, które są skojarzone z nieprawidłowymi kontami magazynu. Sprawdź szczegóły tych identyfikatorów zasobów i nazwy kont magazynu, do których istnieją odwołania.  |
|  MoveResourcesHavePendingOperations  |  Żądanie Move Resources zawiera zasoby, dla których operacja oczekuje. Sprawdź szczegóły tych identyfikatorów zasobów. Spróbuj ponownie wykonać operację po zakończeniu oczekujących operacji.  |
|  MoveResourcesNotFound  |  Żądanie Move Resources zawiera zasoby, których nie można znaleźć. Sprawdź szczegóły tych identyfikatorów zasobów.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci.  |
|  NetworkingInternalOperationError  |  Nieznany błąd alokacji sieci  |
|  NetworkingInternalOperationError  |  Wystąpił błąd wewnętrzny podczas przetwarzania profilu sieciowego maszyny wirtualnej.  |
|  NotFound  |  Nie można znaleźć zestawu dostępności {0} .  |
|  NotFound  |  Źródłowa maszyna wirtualna " {0} " określona w żądaniu nie istnieje w tej lokalizacji platformy Azure.  |
|  NotFound  |  Nie znaleziono dzierżawy o identyfikatorze {0} .  |
|  NotFound  |  Nie można {0} znaleźć obrazu.  |
|  NotSupported  |  Typ licencji to {0} , ale obiekt BLOB obrazu {1} nie znajduje się w środowisku lokalnym.  |
|  OperationNotAllowed  |  {0}Nie można usunąć zestawu dostępności. Przed usunięciem zestawu dostępności upewnij się, że nie zawiera on żadnej maszyny wirtualnej.  |
|  OperationNotAllowed  |  Zmiana jednostki SKU zestawu dostępności z "wyrównany" na "Klasyczna" jest niedozwolona.  |
|  OperationNotAllowed  |  Nie można zmodyfikować rozszerzeń w maszynie wirtualnej, gdy maszyna wirtualna nie jest uruchomiona.  |
|  OperationNotAllowed  |  Akcja przechwytywania jest obsługiwana tylko na maszynie wirtualnej z dyskami opartymi na obiektach Blob. Użyj interfejsów API zasobów "Image", aby utworzyć obraz z zarządzanej maszyny wirtualnej.  |
|  OperationNotAllowed  |  {0}Nie można utworzyć zasobu z obrazu {1} do momentu pomyślnego utworzenia obrazu.  |
|  OperationNotAllowed  |  Aktualizacje encryptionSettings nie są dozwolone, gdy maszyna wirtualna jest przypisana, spróbuj ponownie po cofnięciu przydziału maszyny wirtualnej  |
|  OperationNotAllowed  |  Dodanie dysku zarządzanego do maszyny wirtualnej z dyskami opartymi na obiektach Blob nie jest obsługiwane.  |
|  OperationNotAllowed  |  Maksymalna liczba dysków z danymi, które mogą być dołączone do maszyny wirtualnej o tym rozmiarze, to {0} .  |
|  OperationNotAllowed  |  Dodanie dysku z użyciem obiektów BLOB do maszyny wirtualnej z dyskami zarządzanymi nie jest obsługiwane.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona w przypadku obrazu " {1} ", ponieważ obraz jest oznaczony do usunięcia. Można ponowić próbę wykonania operacji usuwania (lub poczekać na jej zakończenie).  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ maszyna wirtualna została uogólniona.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona, ponieważ kolekcja punktów przywracania " {1} " została oznaczona do usunięcia.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona w rozszerzeniu maszyny wirtualnej " {1} ", ponieważ jest oznaczona do usunięcia. Można ponowić próbę wykonania operacji usuwania (lub poczekać na jej zakończenie).  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona, ponieważ Virtual Machines " {1} " jest inicjowana przy użyciu obrazu " {2} ".  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona, ponieważ maszyna wirtualna zestawu skalowania " {1} " aktualnie używa obrazu " {2} ".  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ maszyna wirtualna jest oznaczona do usunięcia. Można ponowić próbę wykonania operacji usuwania (lub poczekać na jej zakończenie).  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ maszyna wirtualna jest cofana lub jest oznaczona jako do cofnięcia przydziału.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ maszyna wirtualna jest uruchomiona. Wyłącz wyłączenie maszyny wirtualnej z poziomu systemu operacyjnego gościa.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ nie cofnięto przydziału maszyny wirtualnej.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ maszyna wirtualna ma rozszerzenie " {2} " w stanie niepowodzenia.  |
|  OperationNotAllowed  |  Operacja " {0} " jest niedozwolona na maszynie wirtualnej " {1} ", ponieważ inna operacja jest w toku.  |
|  OperationNotAllowed  |  Operacja " {0} " wymaga uogólnionia maszyny wirtualnej " {1} ".  |
|  OperationNotAllowed  |  Operacja wymaga uruchomienia maszyny wirtualnej (lub ustawionej do uruchomienia).  |
|  OperationNotAllowed  |  Dysk o rozmiarze {0} GB, który jest mniejszy niż {1} GB odpowiedniego dysku w obrazie, jest niedozwolony.  |
|  OperationNotAllowed  |  Rozszerzenia zestawu skalowania maszyn wirtualnych programu obsługi " {0} " można dodać tylko w momencie tworzenia zestawu skalowania maszyn wirtualnych.  |
|  OperationNotAllowed  |  Rozszerzenia zestawu skalowania maszyn wirtualnych programu obsługi " {0} " można usunąć tylko w czasie usuwania zestawu skalowania maszyn wirtualnych.  |
|  OperationNotAllowed  |  Maszyna wirtualna " {0} " korzysta już z dysków zarządzanych.  |
|  OperationNotAllowed  |  Maszyna wirtualna " {0} " należy do "klasycznego" zestawu dostępności " {1} ". Zaktualizuj zestaw dostępności, aby używał "wyrównanej" jednostki SKU, a następnie ponów próbę konwersji.  |
|  OperationNotAllowed  |  Maszyna wirtualna utworzona na podstawie obrazu nie może mieć dysków opartych na obiektach Blob. Wszystkie dyski muszą być dyskami zarządzanymi.  |
|  OperationNotAllowed  |  Nie można ukończyć operacji przechwytywania, ponieważ maszyna wirtualna nie została uogólniona.  |
|  OperationNotAllowed  |  Operacje zarządzania na maszynie wirtualnej " {0} " są niedozwolone, ponieważ dyski maszyny wirtualnej są konwertowane na dyski zarządzane.  |
|  OperationNotAllowed  |  Bieżąca operacja zmienia stan zużywanej maszyny wirtualnej {0} na {1} . Wykonaj operację {2} za jakiś czas.  |
|  OperationNotAllowed  |  Nie można dodać lub zaktualizować maszyny wirtualnej. Żądany rozmiar maszyny wirtualnej {0} może nie być dostępny w istniejącej jednostce alokacji. Więcej informacji na temat strategii zmiany rozmiarów maszyny wirtualnej można znaleźć pod adresem https://aka.ms/azure-resizevm .  |
|  OperationNotAllowed  |  Nie można zmienić rozmiaru maszyny wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym zestaw dostępności jest aktualnie przydzielony. Dostępne są następujące rozmiary: {1} . Więcej informacji na temat strategii zmiany rozmiarów maszyny wirtualnej można znaleźć pod adresem https://aka.ms/azure-resizevm .  |
|  OperationNotAllowed  |  Nie można zmienić rozmiaru maszyny wirtualnej, ponieważ żądany rozmiar {0} nie jest dostępny w klastrze, w którym maszyna wirtualna jest aktualnie przypisana. Aby zmienić rozmiar maszyny wirtualnej w celu {1} cofnięcia alokacji (jest to operacja zatrzymania w Azure Portal), a następnie spróbuj ponownie wykonać operację zmiany rozmiaru. Więcej informacji na temat strategii zmiany rozmiarów maszyny wirtualnej można znaleźć pod adresem https://aka.ms/azure-resizevm .  |
|  OSProvisioningClientError  |  Inicjowanie obsługi systemu operacyjnego nie powiodło się dla maszyny wirtualnej " {0} ", ponieważ obecnie jest inicjowana obsługa systemu operacyjnego gościa.  |
|  OSProvisioningClientError  |  Inicjowanie obsługi administracyjnej systemu operacyjnego dla maszyny wirtualnej " {0} " nie powiodło się. Szczegóły błędu: Upewnij {1} się, że obraz został prawidłowo przygotowany (uogólniony). <ul><li>Instrukcje dla systemu Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Generowanie klucza hosta SSH nie powiodło się. Szczegóły błędu: {0} . Aby rozwiązać ten problem, sprawdź, czy Agent systemu Linux jest prawidłowo skonfigurowany. <ul><li>Instrukcje można znaleźć pod adresem: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Nazwa użytkownika określona dla maszyny wirtualnej jest nieprawidłowa dla tej dystrybucji systemu Linux. Szczegóły błędu: {0} .  |
|  OSProvisioningInternalError  |  Inicjowanie obsługi systemu operacyjnego nie powiodło się dla maszyny wirtualnej " {0} " z powodu błędu wewnętrznego.  |
|  OSProvisioningTimedOut  |  Inicjowanie obsługi systemu operacyjnego dla maszyny wirtualnej " {0} " nie zostało ukończone w wyznaczonym czasie. Obsługa administracyjna może nadal zakończyć się pomyślnie. Sprawdź stan aprowizacji później.  |
|  OSProvisioningTimedOut  |  Inicjowanie obsługi systemu operacyjnego dla maszyny wirtualnej " {0} " nie zostało ukończone w wyznaczonym czasie. Obsługa administracyjna może nadal zakończyć się pomyślnie. Sprawdź stan aprowizacji później. Upewnij się również, że obraz został prawidłowo przygotowany (uogólniony).   <ul><li>Instrukcje dla [systemu Windows]( ../windows/upload-generalized-managed.md).</li><li> Instrukcje dotyczące systemu [Linux](../linux/capture-image.md)</li></ul>  |
|  OSProvisioningTimedOut  |  Inicjowanie obsługi systemu operacyjnego dla maszyny wirtualnej " {0} " nie zostało ukończone w wyznaczonym czasie. Jednak wykryto agenta gościa maszyny wirtualnej. Sugeruje to, że system operacyjny gościa nie został poprawnie przygotowany do użycia jako obraz maszyny wirtualnej (z opcją with Option = FromImage). Aby rozwiązać ten problem, użyj wirtualnego dysku twardego w postaci z opcją with nooption = Attach lub przygotuj go prawidłowo do użycia jako obraz:   <ul><li>Instrukcje dla systemu Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instrukcje dotyczące systemu Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Wymagany rozmiar maszyny wirtualnej nie jest obecnie dostępny w wybranej lokalizacji.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Nie można teraz zaktualizować zasobu z powodu trwającej aktualizacji platformy. Spróbuj ponownie później.  |
|  StorageAccountLimitation  |  Konto magazynu " {0} " nie obsługuje stronicowych obiektów blob, które są wymagane do tworzenia dysków.  |
|  StorageAccountLimitation  |  Konto magazynu " {0} " przekroczyło przydzieloną liczbę przydziałów.  |
|  StorageAccountLocationMismatch  |  Nie można rozpoznać konta magazynu {0} . Upewnij się, że został on utworzony za pomocą dostawcy zasobów magazynu w tej samej lokalizacji co zasób obliczeniowy.  |
|  StorageAccountNotFound  |  {0}Nie znaleziono konta magazynu. Upewnij się, że konto magazynu nie zostało usunięte i należy do tej samej lokalizacji platformy Azure co maszyna wirtualna.  |
|  StorageAccountNotRecognized  |  Użyj konta magazynu zarządzanego przez dostawcę zasobów magazynu. Użycie {0} nie jest obsługiwane.  |
|  StorageAccountOperationInternalError  |  Wystąpił błąd wewnętrzny podczas uzyskiwania dostępu do konta magazynu {0} .  |
|  StorageAccountSubscriptionMismatch  |  Konto magazynu {0} nie należy do subskrypcji {1} .  |
|  StorageAccountTooBusy  |  Konto magazynu " {0} " jest obecnie zajęte. Rozważ użycie innego konta.  |
|  StorageAccountTypeNotSupported  |  {0}Użycie dysku {1} , które jest kontem magazynu obiektów BLOB. Spróbuj ponownie za pomocą konta magazynu ogólnego przeznaczenia.  |
|  StorageAccountTypeNotSupported  |  Konto magazynu {0} jest {1} typu. Diagnostyka rozruchu obsługuje {2} typy kont magazynu.  <ul><li>Ten błąd występuje, jeśli używasz konta usługi Premium Storage na potrzeby diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [jak korzystać z diagnostyki rozruchu](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Subskrypcja nie jest autoryzowana.  |
|  TargetDiskBlobAlreadyExists  |  Obiekt BLOB {0} już istnieje. Podaj inny identyfikator URI obiektu BLOB, aby utworzyć nowy pusty dysk danych " {1} ".  |
|  TargetDiskBlobAlreadyExists  |  Nie można kontynuować operacji przechwytywania, ponieważ obiekt BLOB obrazu docelowego {0} już istnieje i flaga zastąpienia obiektów BLOB wirtualnego dysku twardego nie została ustawiona. Usuń obiekt BLOB lub Ustaw flagę w celu zastąpienia obiektów BLOB wirtualnego dysku twardego, a następnie ponów próbę.  |
|  TargetDiskBlobAlreadyExists  |  Nie można kontynuować operacji przechwytywania, ponieważ docelowy obiekt BLOB obrazu {0} ma aktywną dzierżawę.   |
|  TargetDiskBlobAlreadyExists  |  Obiekt BLOB {0} już istnieje. Podaj inny identyfikator URI obiektu BLOB jako element docelowy dla dysku " {1} ".  |
|  TooManyVMRedeploymentRequests  |  Odebrano zbyt wiele żądań ponownego wdrożenia dla maszyny wirtualnej " {0} " lub maszyny wirtualne w tym samym availabilityset z tą maszyną wirtualną. Spróbuj ponownie później.  |
|  VHDSizeInvalid  |  Określona wartość rozmiaru dysku {0} dla dysku " {1} " z obiektem BLOB {2} jest nieprawidłowa. Rozmiar dysku musi mieścić się w przedziale od {3} do {4} .  |
|  VMAgentStatusCommunicationError  |  Maszyna wirtualna " {0} " nie zgłosiła stanu agenta lub rozszerzeń maszyny wirtualnej. Upewnij się, że maszyna wirtualna ma uruchomionego agenta maszyny wirtualnej i może nawiązać połączenia wychodzące z usługą Azure Storage.  |
|  VMArtifactRepositoryInternalError  |  Wystąpił błąd podczas komunikacji z repozytorium artefaktu w celu pobrania szczegółów artefaktu maszyny wirtualnej.  |
|  VMArtifactRepositoryInternalError  |  Wystąpił błąd wewnętrzny podczas pobierania danych artefaktów maszyny wirtualnej z repozytorium artefaktów.  |
|  VMExtensionHandlerNonTransientError  |  Procedura obsługi " {0} " zgłosiła niepowodzenie dla rozszerzenia maszyny wirtualnej " {1} " z kodem błędu terminalu " {2} " i komunikatem o błędzie: " {3} "  |
|  VMExtensionManagementInternalError  |  Wystąpił błąd wewnętrzny podczas przetwarzania rozszerzenia maszyny wirtualnej " {0} ".  |
|  VMExtensionManagementInternalError  |  Podczas przygotowywania rozszerzeń maszyny wirtualnej wystąpiło wiele błędów. Aby uzyskać szczegółowe informacje, zobacz widok wystąpienia rozszerzenia maszyny wirtualnej.  |
|  VMExtensionProvisioningError  |  Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia " {0} ". Komunikat o błędzie: " {1} ".  |
|  VMExtensionProvisioningError  |  Obsługa wielu rozszerzeń maszyny wirtualnej na maszynie wirtualnej nie powiodła się. Aby uzyskać szczegółowe informacje, zobacz widok wystąpienia rozszerzenia maszyny wirtualnej.  |
|  VMExtensionProvisioningTimeout  |  Przekroczono limit czasu aprowizacji rozszerzenia maszyny wirtualnej " {0} ". Instalacja rozszerzenia może być zbyt długa lub nie można uzyskać stanu rozszerzenia.  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej na podstawie obrazu spoza witryny Marketplace nie wymaga informacji o planie, Usuń informacje o planie w żądaniu. Nazwa dysku systemu operacyjnego to {0} .  |
|  VMMarketplaceInvalidInput  |  Informacje o zakupie nie są zgodne. Nie można wdrożyć z obrazu portalu Marketplace. Nazwa dysku systemu operacyjnego to {0} .  |
|  VMMarketplaceInvalidInput  |  Tworzenie maszyny wirtualnej na podstawie obrazu z portalu Marketplace wymaga informacji o planie w żądaniu. Nazwa dysku systemu operacyjnego to {0} .  |
|  VMNotFound  |  Nie można znaleźć maszyny wirtualnej " {0} ".  |
|  VMRedeploymentFailed  |  Ponowne wdrażanie maszyny wirtualnej " {0} " nie powiodło się z powodu błędu wewnętrznego. Spróbuj ponownie później.  |
|  VMRedeploymentTimedOut  |  Ponowne wdrażanie maszyny wirtualnej " {0} " nie zostało ukończone w wyznaczonym czasie. Może zakończyć się pomyślnie w pewnym czasie. W przeciwnym razie możesz ponowić próbę żądania.  |
|  VMStartTimedOut  |  Maszyna wirtualna " {0} " nie została uruchomiona w wyznaczonym czasie. Maszyna wirtualna może być nadal uruchomiona pomyślnie. Sprawdź stan zasilacza później.  |


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.