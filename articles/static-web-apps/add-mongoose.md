---
title: 'Samouczek: dostęp do danych w Cosmos DB przy użyciu Mongoose z usługą Azure static Web Apps'
description: Dowiedz się, jak uzyskać dostęp do danych w Cosmos DB przy użyciu Mongoose z funkcji interfejsu API Web Apps static platformy Azure.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260072"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Samouczek: dostęp do danych w Cosmos DB przy użyciu Mongoose z usługą Azure static Web Apps

[Mongoose](https://mongoosejs.com/) to najpopularniejsze ODM (mapowanie dokumentów obiektów) dla Node.js. Umożliwienie projektowania struktury danych i wymuszania walidacji, Mongoose zapewnia wszystkie narzędzia niezbędne do współpracy z bazami danych, które obsługują interfejs API Mongoose. [Cosmos DB](../cosmos-db/mongodb-introduction.md) obsługuje niezbędne interfejsy API Mongoose i jest dostępna jako opcja serwera zaplecza na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz konto bezserwerowe Cosmos DB
> - Tworzenie Web Apps statycznej platformy Azure
> - Aktualizowanie ustawień aplikacji w celu przechowywania parametrów połączenia

Jeśli nie masz subskrypcji platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Konto platformy Azure](https://azure.microsoft.com/free/)
- [Konto usługi GitHub](https://github.com/join)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Tworzenie bezserwerowej bazy danych Cosmos DB

Zacznij od utworzenia [Cosmos DB konta bezserwerowego](https://docs.microsoft.com/azure/cosmos-db/serverless) . Korzystając z konta bezserwerowego, płacisz tylko za zasoby, które są używane, i unikaj tworzenia pełnej infrastruktury.

1. Przejdź do [https://portal.azure.com](https://portal.azure.com)
2. Kliknij pozycję **Utwórz zasób**
3. Wprowadź **Azure Cosmos DB** w polu wyszukiwania
4. Kliknij **Azure Cosmos DB**
5. Kliknij pozycję **Utwórz**
6. Skonfiguruj konto Azure Cosmos DB przy użyciu następujących informacji
    - Subskrypcja: wybierz subskrypcję, której chcesz użyć
    - Zasób: kliknij przycisk **Utwórz nowy** i Ustaw nazwę na **aswa-Mongoose**
    - Nazwa konta: wymagana jest unikatowa wartość
    - Interfejs API: **Azure Cosmos DB dla interfejsu API MongoDB**
    - Notesy (wersja zapoznawcza): **wyłączone**
    - Lokalizacja: **zachodnie stany USA 2**
    - Tryb pojemności: **bezserwerowy (wersja zapoznawcza)**
    - Wersja: **3,6**
    - Strefy dostępności: **Wyłącz** 
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="Tworzenie nowego wystąpienia Cosmos DB":::
7. Kliknij przycisk **Przegląd + Utwórz**
8. Kliknij pozycję **Utwórz**

Proces tworzenia może potrwać kilka minut. Wykonanie kolejnych kroków spowoduje powrót do bazy danych w celu zebrania parametrów połączenia.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

W tym samouczku jest używane repozytorium szablonów usługi GitHub, które ułatwia tworzenie aplikacji.

1. Przejdź do [początkowego szablonu](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Wybierz **właściciela** (Jeśli używana jest organizacja inna niż konto główne)
3. Nadaj nazwę repozytorium **aswa-Mongoose — samouczek**
4. Kliknij pozycję **Utwórz repozytorium na podstawie szablonu**
5. Wróć do [Azure Portal](https://portal.azure.com)
6. Kliknij pozycję **Utwórz zasób**
7. W polu wyszukiwania wpisz **statyczne aplikacje sieci Web**
8. Wybieranie **statycznej aplikacji sieci Web (wersja zapoznawcza)**
9. Kliknij pozycję **Utwórz**
10. Skonfiguruj statyczną aplikację sieci Web platformy Azure przy użyciu następujących informacji
    - Subskrypcja: Wybierz tę samą subskrypcję co poprzednio
    - Grupa zasobów: wybierz pozycję **aswa-Mongoose**
    - Name: **aswa-Mongoose — samouczek**
    - Region: **zachodnie stany USA 2**
    - Kliknij pozycję **Zaloguj się za pomocą usługi GitHub**
    - Kliknij przycisk **Autoryzuj** , jeśli zostanie wyświetlony monit o zezwolenie na Web Apps statycznej platformy Azure, aby utworzyć akcję GitHub w celu włączenia wdrożenia
    - Organizacja: nazwa konta
    - Repozytorium: **aswa-Mongoose — samouczek**
    - Gałąź: **główna**
    - Ustawienia wstępne kompilacji: Wybierz **niestandardowe**
    - Lokalizacja aplikacji: **/Public**
    - Lokalizacja interfejsu API: **interfejs API**
    - Lokalizacja artefaktu aplikacji: nie wypełniaj *pustego* 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="formularza Web Apps statycznej platformy Azure":::
11. Kliknij przycisk **Przeglądaj i Utwórz**
12. Kliknij pozycję **Utwórz**

Proces tworzenia trwa kilka minut, ale po aprowizacji aplikacji można kliknąć pozycję **Przejdź do zasobu** .

## <a name="configure-database-connection-string"></a>Konfigurowanie parametrów połączenia z bazą danych

Aby umożliwić aplikacji sieci Web komunikowanie się z bazą danych, parametry połączenia z bazą danych są przechowywane jako ustawienia aplikacji. Ustawienia wartości są dostępne w Node.js przy użyciu `process.env` obiektu.

1. Kliknij pozycję **Strona główna** w lewym górnym rogu Azure Portal (lub przejdź z powrotem do obszaru [https://portal.azure.com](https://portal.azure.com) ).
2. Kliknij pozycję **grupy zasobów**
3. Kliknij pozycję **aswa-Mongoose**
4. Kliknij nazwę konta bazy danych — będzie ono miało typ **konta Azure Cosmos DB**
5. W obszarze **Ustawienia** kliknij pozycję **Parametry połączenia** .
6. Kopiuj parametry połączenia wymienione w obszarze **podstawowe parametry połączenia**
7. W obszarze nawigacji kliknij pozycję **aswa-Mongoose**
8. Kliknij pozycję **aswa-Mongoose — samouczek** , aby powrócić do wystąpienia witryny sieci Web
9. W obszarze **Ustawienia** kliknij pozycję **Konfiguracja** .
10. Kliknij przycisk **Dodaj** i Utwórz nowe ustawienie aplikacji z następującymi wartościami:
    - Nazwa: **CONNECTION_STRING**
    - Wartość: wklej wcześniej skopiowane parametry połączenia
11. Kliknij przycisk **OK** .
12. Kliknij pozycję **Zapisz**.

## <a name="navigate-to-your-site"></a>Przejdź do swojej witryny

Teraz możesz eksplorować statyczną aplikację sieci Web.

1. Kliknij pozycję **Przegląd**
1. Kliknij adres URL wyświetlany w prawym górnym rogu.
    1. Będzie wyglądać podobnie do `https://calm-pond-05fcdb.azurestaticapps.net`
1. Utwórz nowe zadanie, wpisując tytuł i klikając polecenie **Dodaj zadanie** .
1. Potwierdź, że zadanie jest wyświetlane (może chwilę potrwać)
1. Oznacz zadanie jako ukończone, **klikając pole wyboru**
1. **Odśwież stronę,** aby potwierdzić, że baza danych jest używana

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń grupę zasobów, wykonując następujące czynności:

1. Wróć do [Azure Portal](https://portal.azure.com)
2. Kliknij pozycję **grupy zasobów**
3. Kliknij pozycję **aswa-Mongoose**
4. Kliknij pozycję **Usuń grupę zasobów**
5. Wpisz **aswa-Mongoose** w polu tekstowym
6. Kliknij pozycję **Usuń**

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować lokalne projektowanie...
> [!div class="nextstepaction"]
> [Konfigurowanie programowania lokalnego](./local-development.md)
 
