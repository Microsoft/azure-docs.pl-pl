---
title: Uwierzytelnij Azure Stream Analytics do Azure Data Lake Storage Gen1
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics do Azure Data Lake Storage Gen1 danych wyjściowych.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 82c5a246dca69c0723394e41058c4fc123bbb84e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571950"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>Uwierzytelnianie Stream Analytics do Azure Data Lake Storage Gen1 przy użyciu tożsamości zarządzanych (wersja zapoznawcza)

Azure Stream Analytics obsługuje uwierzytelnianie tożsamości zarządzanej z Azure Data Lake Storage (ADLS) Gen1 dane wyjściowe. Tożsamość to zarządzana aplikacja zarejestrowana w Azure Active Directory, która reprezentuje daną Stream Analytics zadanie i może służyć do uwierzytelniania w zasobie przeznaczonym do zasobów. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartych na użytkownikach, takich jak wymaganie ponownego uwierzytelnienia ze względu na zmiany hasła lub wygaśnięcia tokenów użytkowników, które wystąpiły co 90 dni. Ponadto zarządzane tożsamości pomagają w automatyzowaniu wdrożeń zadań Stream Analytics, które są przekazywane do Azure Data Lake Storage Gen1.

W tym artykule przedstawiono trzy sposoby włączania zarządzanej tożsamości dla zadania Azure Stream Analytics, które wyprowadza Azure Data Lake Storage Gen1 za pomocą Azure Portal, wdrożenia szablonu Azure Resource Manager i narzędzi Azure Stream Analytics dla programu Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Zacznij od utworzenia nowego zadania Stream Analytics lub otwierając istniejące zadanie w Azure Portal. Na pasku menu znajdującym się po lewej stronie ekranu wybierz pozycję **zarządzana tożsamość** znajdującą się w obszarze **Konfiguruj**.

   ![Konfigurowanie tożsamości zarządzanej Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. W oknie, które pojawia się po prawej stronie, wybierz pozycję **Użyj zarządzanej tożsamości przypisanej do systemu** . Kliknij przycisk **Zapisz** w jednostce usługi, aby uzyskać tożsamość zadania Stream Analytics w Azure Active Directory. Cykl życia nowo utworzonej tożsamości będzie zarządzany przez platformę Azure. Po usunięciu zadania Stream Analytics skojarzona tożsamość (czyli nazwa główna usługi) zostanie automatycznie usunięta przez platformę Azure.

   Po zapisaniu konfiguracji identyfikator obiektu (OID) jednostki usługi jest wymieniony jako identyfikator podmiotu zabezpieczeń, jak pokazano poniżej:

   ![Identyfikator jednostki usługi Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Nazwa główna usługi ma taką samą nazwę jak zadanie Stream Analytics. Jeśli na przykład nazwa zadania to **MyASAJob**, tworzona jest również nazwa jednostki usługi **MyASAJob**.

3. W oknie właściwości danych wyjściowych ujścia danych wyjściowych ADLS Gen1 kliknij listę rozwijaną tryb uwierzytelniania i wybierz pozycję * * tożsamość zarządzana * *.

4. Wypełnij pozostałe właściwości. Aby dowiedzieć się więcej na temat tworzenia danych wyjściowych ADLS, zobacz [Tworzenie danych wyjściowych usługi Data Lake Store za pomocą usługi Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Po zakończeniu kliknij pozycję **Zapisz**.

   ![Konfigurowanie usługi Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Przejdź do strony przeglądu ADLS Gen1 i kliknij pozycję **Eksplorator danych**.

   ![Konfigurowanie Data Lake Storage — Omówienie](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. W okienku Eksplorator danych wybierz pozycję **dostęp** , a następnie kliknij pozycję **Dodaj** w okienku dostęp.

   ![Konfigurowanie dostępu Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. W polu tekstowym w okienku **Wybierz użytkownika lub grupę** wpisz nazwę jednostki usługi. Należy pamiętać, że nazwa jednostki usługi jest również nazwą odpowiedniego zadania Stream Analytics. Po rozpoczęciu wpisywania nazwy głównej zostanie ona wyświetlona poniżej pola tekstowego. Wybierz żądaną nazwę główną usługi, a następnie kliknij przycisk **Wybierz**.

   ![Wybierz nazwę główną usługi](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. W okienku **uprawnienia** Sprawdź uprawnienia **zapis** i **wykonywanie** i przypisz je do **tego folderu i wszystkich elementów podrzędnych**. Następnie kliknij przycisk **OK**.

   ![Wybierz uprawnienia do zapisu i wykonywania](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Nazwa główna usługi jest wymieniona w obszarze **przypisane uprawnienia** w okienku **dostępu** , jak pokazano poniżej. Teraz możesz wrócić i uruchomić zadanie Stream Analytics.

   ![Stream Analytics listy dostępu w portalu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Aby dowiedzieć się więcej na temat uprawnień systemu plików Data Lake Storage Gen1, zobacz [Access Control w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Narzędzia Stream Analytics Tools for Visual Studio

1. W JobConfig.jsna, ustaw opcję **Użyj tożsamości przypisanej do systemu** na **wartość true**.

   ![Tożsamość zarządzana konfiguracji zadania Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. W oknie właściwości danych wyjściowych ujścia danych wyjściowych ADLS Gen1 kliknij listę rozwijaną tryb uwierzytelniania i wybierz pozycję * * tożsamość zarządzana * *.

   ![ADLS wyjściowe tożsamości zarządzane](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Wypełnij pozostałe właściwości, a następnie kliknij przycisk **Zapisz**.

4. Kliknij pozycję **Prześlij do platformy Azure** w edytorze zapytań.

   Po przesłaniu zadania narzędzia te wykonują dwie rzeczy:

   * Automatycznie tworzy nazwę główną usługi dla tożsamości zadania Stream Analytics w Azure Active Directory. Cykl życia nowo utworzonej tożsamości będzie zarządzany przez platformę Azure. Po usunięciu zadania Stream Analytics skojarzona tożsamość (czyli nazwa główna usługi) zostanie automatycznie usunięta przez platformę Azure.

   * Automatycznie Ustaw uprawnienia do **zapisu** i **wykonywania** dla ścieżki prefiksu ADLS Gen1 użytej w zadaniu i przypisz ją do tego folderu i wszystkich elementów podrzędnych.

5. Szablony Menedżer zasobów można generować z następującą właściwością, korzystając z [Stream Analytics ci.CD pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) w wersji 1.5.0 lub nowszej na maszynie kompilacji (poza programem Visual Studio). Wykonaj kroki wdrażania szablonu Menedżer zasobów w następnej sekcji, aby uzyskać nazwę główną usługi i udzielić dostępu do jednostki usługi za pośrednictwem programu PowerShell.

## <a name="resource-manager-template-deployment"></a>Wdrożenie szablonu Menedżer zasobów

1. Zasób *Microsoft. StreamAnalytics/streamingjobs* można utworzyć przy użyciu tożsamości zarządzanej, dołączając następującą właściwość w sekcji resource szablonu Menedżer zasobów:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ta właściwość nakazuje Azure Resource Manager tworzenia tożsamości dla zadania Azure Stream Analytics i zarządzania nią.

   **Przykładowe zadanie**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Przykładowa odpowiedź na zadanie**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Zwróć uwagę na identyfikator podmiotu zabezpieczeń z odpowiedzi na zadanie, aby udzielić dostępu do wymaganego zasobu ADLS.

   **Identyfikator dzierżawy** jest identyfikatorem dzierżawy Azure Active Directory, w której jest tworzona jednostka usługi. Nazwa główna usługi jest tworzona w dzierżawie platformy Azure, która jest zaufana przez subskrypcję.

   **Typ** wskazuje typ tożsamości zarządzanej, jak wyjaśniono w typach zarządzanych tożsamości. Obsługiwany jest tylko typ przypisany do systemu.

2. Zapewnianie dostępu do jednostki usługi przy użyciu programu PowerShell. Aby udzielić dostępu do jednostki usługi za pośrednictwem programu PowerShell, wykonaj następujące polecenie:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** jest identyfikatorem obiektu jednostki usługi i jest wyświetlany na ekranie portalu po utworzeniu jednostki usługi. Jeśli zadanie zostało utworzone przy użyciu wdrożenia szablonu Menedżer zasobów, identyfikator obiektu zostanie wyświetlony we właściwości tożsamość odpowiedzi na zadanie.

   **Przykład**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Aby dowiedzieć się więcej o powyższym poleceniu programu PowerShell, zapoznaj się z dokumentacją [zestawu AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="remove-managed-identity"></a>Usuwanie tożsamości zarządzanej

Tożsamość zarządzana utworzona dla zadania Stream Analytics jest usuwana tylko po usunięciu zadania. Nie można usunąć tożsamości zarządzanej bez usuwania zadania. Jeśli nie chcesz już używać tożsamości zarządzanej, możesz zmienić metodę uwierzytelniania dla danych wyjściowych. Tożsamość zarządzana będzie nadal istnieć do momentu usunięcia zadania i zostanie użyta, jeśli zdecydujesz się ponownie użyć uwierzytelniania tożsamości zarządzanej.

## <a name="limitations"></a>Ograniczenia
Ta funkcja nie obsługuje następujących czynności:

1. **Dostęp z wieloma dzierżawcami**: jednostka usługi utworzona dla danego zadania Stream Analytics będzie znajdować się w dzierżawie Azure Active Directory, w której zadanie zostało utworzone, i nie można jej używać w odniesieniu do zasobu znajdującego się w innej dzierżawie Azure Active Directory. Z tego względu można używać tylko MSI w przypadku zasobów ADLS generacji 1, które znajdują się w tej samej dzierżawie Azure Active Directory, co zadanie Azure Stream Analytics. 

2. **[Tożsamość przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md)**: nie jest obsługiwana. Oznacza to, że użytkownik nie może wprowadzić własnej nazwy głównej usługi, która będzie używana przez ich Stream Analytics zadanie. Nazwa główna usługi jest generowana przez Azure Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie danych wyjściowych usługi Data Lake Store za pomocą usługi Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testowanie Stream Analytics zapytań lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Lokalne testowanie danych na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-live-data-local-testing.md) 
