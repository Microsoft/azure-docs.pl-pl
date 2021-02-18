---
title: Samouczek `:` Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do Azure Data Lake Store-Windows-Azure AD
description: Samouczek przedstawiający sposób uzyskiwania dostępu do usługi Azure Data Lake Store za pomocą przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Windows.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093809"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób używania tożsamości zarządzanej przypisanej przez system dla maszyny wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Azure Data Lake Store. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
> * Udzielanie maszynie wirtualnej praw dostępu do usługi Azure Data Lake Store
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej oraz używanie go do uzyskiwania dostępu do usługi Azure Data Lake Store

## <a name="prerequisites"></a>Wymagania wstępne

- Zrozumienie zarządzanych tożsamości. Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](overview.md). 
- Konto platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Uprawnienia "właściciel" w odpowiednim zakresie (subskrypcji lub grupy zasobów) do wykonywania wymaganych kroków tworzenia zasobów i zarządzania rolami. Jeśli potrzebujesz pomocy z przypisaniem roli, zobacz [Przypisywanie ról platformy Azure do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md).
- Potrzebna jest również maszyna wirtualna z systemem Windows z włączonymi tożsamościami zarządzanymi przez system.
  - Jeśli musisz utworzyć maszynę wirtualną dla tego samouczka, możesz wykonać czynności opisane w artykule zatytułowanym [Tworzenie maszyny wirtualnej z włączoną tożsamością przypisaną przez system](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)




## <a name="enable"></a>Włącz

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Udzielanie dostępu

Teraz możesz udzielić maszynie wirtualnej praw dostępu do plików i folderów w usłudze Azure Data Lake Store.  W tym kroku możesz użyć istniejącej usługi Data Lake Store lub utworzyć nową.  Aby utworzyć nową usługę Data Lake Store przy użyciu witryny Azure Portal, wykonaj czynności opisane w [przewodniku Szybki start dotyczącym usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). W [dokumentacji usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md) są również dostępne przewodniki Szybki start, które używają interfejsu wiersza polecenia platformy Azure oraz programu Azure PowerShell.

W usłudze Data Lake Store utwórz nowy folder, a następnie przyznaj przypisanej przez system tożsamości zarządzanej maszyny wirtualnej uprawnienia do odczytu, zapisu i wykonywania plików w tym folderze:

1. W witrynie Azure Portal kliknij opcję **Data Lake Store** w obszarze nawigacyjnym po lewej stronie.
2. Kliknij usługę Data Lake Store, której chcesz użyć na potrzeby tego samouczka.
3. Kliknij pozycję **Eksplorator danych** na pasku poleceń.
4. Zostanie zaznaczony folder główny usługi Data Lake Store.  Kliknij pozycję **Dostęp** na pasku poleceń.
5. Kliknij pozycję **Dodaj**.  W polu **Wybierz** wprowadź nazwę maszyny wirtualnej, na przykład **DevTestVM**.  Kliknij, aby wybrać maszynę wirtualną spośród wyników wyszukiwania, a następnie kliknij pozycję **Wybierz**.
6. Kliknij przycisk **Wybierz uprawnienia**.  Wybierz pozycje **Odczyt** i **Wykonywanie**, dodaj do pozycji **Ten folder** i dodaj jako **Tylko uprawnienie dostępu**.  Kliknij przycisk **OK**.  Dodawanie uprawnienia powinno zakończyć się pomyślnie.
7. Zamknij blok **Dostęp**.
8. W tym samouczku utworzymy nowy folder.  Kliknij pozycję **Nowy folder** na pasku poleceń i nadaj folderowi nową nazwę, na przykład **TestFolder**.  Kliknij przycisk **OK**.
9. Kliknij utworzony folder, a następnie kliknij opcję **Dostęp** na pasku poleceń.
10. Podobnie jak w kroku 5, kliknij przycisk **Dodaj**, w polu **Wybierz** wpisz nazwę maszyny wirtualnej, wybierz ją, a następnie kliknij opcję **Wybierz**.
11. Podobnie jak w kroku 6, kliknij opcję **Wybierz uprawnienia** i wybierz pozycje **Odczyt**, **Zapis** i **Wykonywanie**, dodaj do pozycji **Ten folder** i dodaj jako **Wpis uprawnień dostępu i wpis uprawnień domyślnych**.  Kliknij przycisk **OK**.  Dodawanie uprawnienia powinno zakończyć się pomyślnie.

Przypisana przez system tożsamość usługi zarządzanej maszyny wirtualnej może teraz wykonywać wszystkie operacje na plikach w utworzonym folderze.  Aby uzyskać więcej informacji na temat zarządzania dostępem do usługi Data Lake Store, przeczytaj ten artykuł w sekcji [Kontrola dostępu w usłudze Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="access-data"></a>Uzyskiwanie dostępu do danych

Usługa Azure Data Lake Store natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure.  Aby przeprowadzić uwierzytelnianie w systemie plików usługi Data Lake Store, musisz wysłać token dostępu wydany przez usługę Azure AD do punktu końcowego systemu plików Data Lake Store w nagłówku autoryzacji w formacie „Bearer <WARTOŚĆ_TOKENU_DOSTĘPU>”.  Aby dowiedzieć się więcej na temat obsługi usługi Data Lake Store na potrzeby uwierzytelniania usługi Azure AD, przeczytaj [Authentication with Data Lake Store using Azure Active Directory (Uwierzytelnianie w usłudze Data Lake Store za pomocą usługi Azure Active Directory)](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Zestawy SDK klientów systemu plików usługi Data Lake Store jeszcze nie obsługują przypisanej przez system tożsamości zarządzanej dla zasobów platformy Azure.  Ten samouczek zostanie zaktualizowany po dodaniu obsługi do zestawów SDK.

W tym samouczku uwierzytelniasz się w interfejsie REST API dla systemu plików usługi Data Lake Store przy użyciu programu PowerShell w celu wysłania żądań REST. Aby użyć przypisanej przez system tożsamości zarządzanej maszyny wirtualnej na potrzeby uwierzytelniania, musisz wysłać żądania z maszyny wirtualnej.

1. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows. 
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program **PowerShell** w sesji zdalnej. 
4. Używając polecenia `Invoke-WebRequest` programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać token dostępu na potrzeby usługi Azure Data Lake Store.  Identyfikator zasobu dla Data Lake Store ma wartość `https://datalake.azure.net/` .  Usługa Data Lake wykonuje dokładne dopasowanie identyfikatora zasobu — końcowy ukośnik jest ważny.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Skonwertuj odpowiedź z obiektu JSON do obiektu PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Wyodrębnij token dostępu z odpowiedzi.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Za pomocą polecenia „Invoke-WebRequest” programu PowerShell wyślij żądanie do punktu końcowego REST usługi Data Lake Store, aby wyświetlić listę folderów w folderze głównym.  Jest to prosty sposób sprawdzenia, czy wszystko zostało poprawnie skonfigurowane.  Ważne jest, żeby ciąg „Bearer” w nagłówku autoryzacji rozpoczynał się wielką literą „B”.  Nazwę usługi Data Lake Store można znaleźć w sekcji **Przegląd** bloku Data Lake Store w witrynie Azure Portal.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Odpowiedź oznaczająca powodzenie wygląda następująco:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Teraz możesz spróbować przekazać plik do usługi Data Lake Store.  Najpierw utwórz plik do przekazania.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Za pomocą polecenia `Invoke-WebRequest` programu PowerShell wyślij żądanie do punktu końcowego REST usługi Data Lake Store, aby przekazać plik do utworzonego wcześniej folderu.  To żądanie ma dwa kroki.  W pierwszym kroku tworzy się żądanie i uzyskuje przekierowanie do lokalizacji, do której należy przekazać plik.  W drugim kroku plik zostaje faktycznie przekazany.  Pamiętaj, aby odpowiednio ustawić nazwę folderu i pliku, jeżeli użyto innych wartości niż stosowanych w tym samouczku. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   W przypadku sprawdzenia wartości `$HdfsRedirectResponse` powinna ona wyglądać jak poniższa odpowiedź:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Zakończ przekazywanie, wysyłając żądanie do punktu końcowego przekierowania:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Odpowiedź oznaczająca powodzenie wygląda następująco:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Za pomocą innych interfejsów API systemu plików usługi Data Lake Store można dołączać dane do plików, pobierać pliki i wykonywać inne operacje.


## <a name="disable"></a>Wyłącz

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Windows w celu uzyskania dostępu do usługi Azure Data Lake Store. Aby dowiedzieć się więcej o usłudze Azure Data Lake Store, zobacz:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)