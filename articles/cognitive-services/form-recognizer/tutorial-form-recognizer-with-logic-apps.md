---
title: 'Samouczek: używanie aparatu rozpoznawania formularzy z Azure Logic Apps do analizowania faktur'
titleSuffix: Azure Cognitive Services
description: W tym samouczku użyjesz aparatu rozpoznawania formularzy z Azure Logic Apps, aby utworzyć przepływ pracy, który automatyzuje proces szkolenia modelu i testowania przy użyciu przykładowych danych.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77118270"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Samouczek: używanie aparatu rozpoznawania formularzy z Azure Logic Apps do analizowania faktur

W tym samouczku utworzysz przepływ pracy w Azure Logic Apps, który korzysta z aparatu rozpoznawania formularza, usługi, która jest częścią pakietu Cognitive Services platformy Azure, aby wyodrębnić dane z faktur. Najpierw należy przeszkolić model aparatu rozpoznawania formularzy przy użyciu przykładowego zestawu danych, a następnie przetestować model w innym zestawie danych.

Oto, co obejmuje ten samouczek:

> [!div class="checklist"]
> * Żądaj dostępu do aparatu rozpoznawania formularzy
> * Tworzenie kontenera obiektów BLOB usługi Azure Storage
> * Przekazywanie przykładowych danych do kontenera obiektów blob platformy Azure
> * Tworzenie aplikacji logiki platformy Azure
> * Konfigurowanie aplikacji logiki do korzystania z zasobu aparatu rozpoznawania formularzy
> * Testowanie przepływu pracy przez uruchomienie aplikacji logiki

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).

## <a name="understand-the-invoice-to-be-analyzed"></a>Zrozumienie faktury do analizy

Przykładowy zestaw danych, który będzie używany do uczenia i testowania modelu, jest dostępny jako plik. zip z usługi [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Pobierz i wyodrębnij plik zip, a następnie otwórz plik PDF faktury w folderze **/Train** . Zwróć uwagę, że zawiera on tabelę z numerem faktury, datą faktury itd. 

> [!div class="mx-imgBorder"]
> ![Przykładowa faktura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

W tym samouczku dowiesz się, jak używać przepływu pracy Azure Logic Apps, aby wyodrębnić informacje z tabel takich jak w formacie JSON.

## <a name="create-an-azure-storage-blob-container"></a>Tworzenie kontenera obiektów BLOB usługi Azure Storage

Ten kontener służy do przekazywania przykładowych danych, które są wymagane do uczenia modelu.

1. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md) , aby utworzyć konto magazynu. Użyj **formrecostorage** jako nazwy konta magazynu.
1. Postępuj zgodnie z instrukcjami w temacie [Tworzenie kontenera obiektów blob platformy Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) , aby utworzyć kontener na koncie usługi Azure Storage. Użyj **formrecocontainer** jako nazwy kontenera. Upewnij się, że ustawisz publiczny poziom dostępu do **kontenera (Anonimowy dostęp do odczytu dla kontenerów i obiektów BLOB)**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie kontenera obiektów blob](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Przekazywanie przykładowych danych do kontenera obiektów blob platformy Azure

Pobierz przykładowe dane dostępne w serwisie [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Wyodrębnij dane do folderu lokalnego i przekaż zawartość folderu **/Train** do utworzonego wcześniej **formrecocontainer** . Postępuj zgodnie z instrukcjami w obszarze [Przekaż blokowy obiekt BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) , aby przekazać dane do kontenera.

Skopiuj adres URL kontenera. Ten adres URL będzie potrzebny w dalszej części tego samouczka. Jeśli utworzono konto magazynu i kontener o takich samych nazwach jak wymienione w tym samouczku, adres URL będzie *https:\//formrecostorage.blob.Core.Windows.NET/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

Za pomocą Azure Logic Apps można zautomatyzować i zorganizować zadania i przepływy pracy. W tym samouczku utworzysz aplikację logiki, która jest wyzwalana przez otrzymywanie faktury, która ma zostać przeanalizowana jako załącznik wiadomości e-mail. W tym przepływie pracy wykonuje się następujące zadania:
* Skonfiguruj aplikację logiki, aby była wyzwalana automatycznie po otrzymaniu wiadomości e-mail z załączoną fakturą.
* Skonfiguruj aplikację logiki tak, aby korzystała z operacji **uczenia modelu** aparatu rozpoznawania w celu uczenia modelu przy użyciu przykładowych danych przekazanych do usługi Azure Blob Storage.
* Skonfiguruj aplikację logiki tak, aby korzystała z **przeanalizowania operacji formularza** aparatu rozpoznawania formularzy do korzystania z modelu, który został już przeszkolony. Ten składnik przeanalizuje fakturę przemieszczoną w tej aplikacji logiki na podstawie modelu, który został wcześniej przeszkolony.

Wykonaj następujące kroki, aby skonfigurować przepływ pracy.

1. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób** > **Integration** > **aplikacja logiki**.

1. W obszarze **Tworzenie aplikacji logiki** podaj szczegółowe informacje o aplikacji logiki, jak pokazano poniżej. Po zakończeniu wybierz pozycję **Utwórz**.

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*Logic-App-Name*> | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry,`-`łączniki (), podkreślenia (`_`), nawiasy (`(`, `)`) i kropki (`.`). W tym przykładzie zastosowano "My-First-Logic-App". |
   | **Subskrypcja** | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | <*Azure-Resource-Group-Name*> | Nazwa [grupy zasobów platformy Azure](./../../azure-resource-manager/management/overview.md) używanej do organizowania powiązanych zasobów. W tym przykładzie jest stosowana wartość "My-First-LA-RG". |
   | **Lokalizacja** | <*Platforma Azure — region*> | Region, w którym są przechowywane informacje o aplikacji logiki. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **powiadomienia** > **Przejdź do pozycji zasób** dla wdrożonej aplikacji logiki. Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   > [!div class="mx-imgBorder"]
   > ![Wybieranie pustego szablonu dla aplikacji logiki](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Skonfiguruj aplikację logiki, aby wyzwolić przepływ pracy po nadejściu wiadomości e-mail

W tym samouczku zostanie wyzwolony przepływ pracy po odebraniu wiadomości e-mail z załączoną fakturą. W tym samouczku użyto pakietu Office 365 jako usługi poczty e-mail, ale można użyć dowolnego innego dostawcy poczty e-mail, który ma być używany.

1. Z kart wybierz pozycję Wszystkie, wybierz pozycję **Office 365 Outlook**, a następnie w obszarze **wyzwalacze**wybierz pozycję **po nadejściu nowej wiadomości e-mail**.

    ![Wyzwalanie aplikacji logiki za pośrednictwem przychodzącej wiadomości e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. W oknie **office 365 Outlook** kliknij przycisk **Zaloguj**, a następnie wprowadź szczegóły, aby zalogować się do konta pakietu Office 365.

1. W następnym oknie dialogowym wykonaj następujące czynności.
    1. Wybierz folder, który ma być monitorowany dla każdej nowej wiadomości e-mail.
    1. W przypadku opcji **ma załączniki**wybierz opcję **tak**. Dzięki temu tylko wiadomości e-mail z załącznikami wyzwalają przepływ pracy.
    1. Wybierz opcję **tak**, aby **dołączyć załączniki**. Dzięki temu zawartość załącznika zostanie użyta w przetwarzaniu podrzędnym.

        > [!div class="mx-imgBorder"]
        > ![Konfigurowanie wyzwalacza poczty e-mail aplikacji logiki](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Kliknij przycisk **Zapisz** na pasku narzędzi u góry.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Konfigurowanie aplikacji logiki do korzystania z modelu uczenia aparatu rozpoznawania formularza

Aby można było analizować faktury przy użyciu usługi rozpoznawania formularzy, należy przeprowadzić uczenie modelu przez udostępnienie kilku przykładowych danych faktur, które mogą być analizowane przez model i informacje o nich.

1. Wybierz pozycję **nowy krok**, a **następnie w obszarze Wybierz akcję**Wyszukaj **aparat rozpoznawania formularzy**. Z wyświetlanych wyników wybierz pozycję **aparat rozpoznawania formularzy**, a następnie w obszarze Akcje dostępne dla aparatu rozpoznawania formularzy wybierz pozycję **Testuj model**.

    > [!div class="mx-imgBorder"]
    > ![Uczenie modelu aparatu rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. W oknie dialogowym aparat rozpoznawania formularzy Podaj nazwę połączenia, a następnie wprowadź adres URL punktu końcowego i klucz pobrany dla zasobu aparatu rozpoznawania formularzy.

    > [!div class="mx-imgBorder"]
    > ![Nazwa połączenia dla aparatu rozpoznawania formularza](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Kliknij przycisk **Utwórz**.

1. W oknie dialogowym **model uczenia** w polu **Źródło**wprowadź adres URL kontenera, do którego zostały przekazane przykładowe dane.

    > [!div class="mx-imgBorder"]
    > ![Kontener magazynu dla przykładowych faktur](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Kliknij przycisk **Zapisz** na pasku narzędzi u góry.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Konfigurowanie aplikacji logiki do korzystania z formularza analizy rozpoznawania formularzy

W tej sekcji dodasz operację **Analizuj formularz** do przepływu pracy. Ta operacja używa już przeszkolonego modelu do analizowania nowej faktury udostępnionej aplikacji logiki.

1. Wybierz pozycję **nowy krok**, a **następnie w obszarze Wybierz akcję**Wyszukaj **aparat rozpoznawania formularzy**. Z wyświetlanych wyników wybierz pozycję **aparat rozpoznawania formularzy**, a następnie w obszarze Akcje dostępne dla aparatu rozpoznawania formularzy wybierz pozycję **Analizuj formularz**.

    > [!div class="mx-imgBorder"]
    > ![Analizowanie modelu aparatu rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. W oknie dialogowym **Analizowanie formularza** wykonaj następujące czynności:

    1. Kliknij pole tekstowe **Identyfikator modelu** , a następnie w oknie dialogowym, które zostanie otwarte, na karcie **zawartość dynamiczna** wybierz pozycję **ModelId**. Dzięki temu dostarczasz aplikację Flow z IDENTYFIKATORem modelu, który został przeszkolony w ostatniej sekcji.

        > [!div class="mx-imgBorder"]
        > ![Korzystanie z ModelID do rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Kliknij pole tekstowe **dokument** , a następnie w oknie dialogowym, które się otworzy, na karcie **zawartość dynamiczna** wybierz pozycję **zawartość załączników**. Spowoduje to skonfigurowanie przepływu do użycia przykładowego pliku faktury, który jest dołączony do wiadomości e-mail wyzwalającej przepływ pracy.

        > [!div class="mx-imgBorder"]
        > ![Analizowanie faktur przy użyciu załącznika wiadomości e-mail](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Kliknij przycisk **Zapisz** na pasku narzędzi u góry.

### <a name="extract-the-table-information-from-the-invoice"></a>Wyodrębnij informacje z tabeli z faktury

W tej sekcji skonfigurujesz aplikację logiki, aby wyodrębnić informacje z tabeli w ramach faktur.

1. Wybierz pozycję **Dodaj akcję**, a następnie w obszarze **Wybierz akcję**Wyszukaj pozycję **Utwórz** i w obszarze dostępne akcje wybierz pozycję **Utwórz** ponownie.
    ![Wyodrębnij informacje z tabeli z faktury](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. W oknie dialogowym **redagowanie** kliknij pole tekstowe **dane wejściowe** , a następnie w oknie dialogowym, które się pojawi, wybierz pozycję **tabele**.

    > [!div class="mx-imgBorder"]
    > ![Wyodrębnij informacje z tabeli z faktury](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Kliknij przycisk **Zapisz**.

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby przetestować aplikację logiki, użyj przykładowych faktur w folderze **/test** zestawu danych przykładowych pobranych z usługi [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Wykonaj następujące kroki:

1. Z poziomu projektanta Azure Logic Apps aplikacji wybierz pozycję **Uruchom** na pasku narzędzi u góry. Przepływ pracy jest teraz aktywny i oczekuje na odebranie wiadomości e-mail z załączoną fakturą.
1. Wyślij wiadomość e-mail z przykładową fakturą dołączoną do adresu e-mail podanego podczas tworzenia aplikacji logiki. Upewnij się, że poczta e-mail została dostarczona do folderu podanego podczas konfigurowania aplikacji logiki.
1. Gdy tylko wiadomość e-mail zostanie dostarczona do folderu, Projektant Logic Apps będzie wyświetlać ekran z postępem każdego etapu. Na poniższym zrzucie ekranu zobaczysz, że otrzymasz wiadomość e-mail z załącznikiem, a przepływ pracy jest w toku.

    > [!div class="mx-imgBorder"]
    > ![Uruchom przepływ pracy, wysyłając wiadomość e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Po zakończeniu wszystkich etapów przepływu pracy program Logic Apps Designer wyświetli zielone pole wyboru dla każdego etapu. W oknie Projektant wybierz pozycję **dla każdego 2**, a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Przepływ pracy zakończony](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    W polu **wyjściowe** skopiuj dane wyjściowe i wklej je do dowolnego edytora tekstu.

1. Porównaj dane wyjściowe JSON z przykładową fakturą wysłaną jako załącznik wiadomości e-mail. Sprawdź, czy dane JSON odnoszą się do danych w tabeli w ramach faktury.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Pomyślnie wykonano ten samouczek!

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurujesz przepływ pracy Azure Logic Apps, który będzie używać aparatu rozpoznawania formularzy do uczenia modelu i wyodrębnienia zawartości faktury. Następnie Dowiedz się, jak utworzyć zestaw danych szkoleniowych, aby można było utworzyć podobny scenariusz z własnymi formularzami.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](build-training-data-set.md)