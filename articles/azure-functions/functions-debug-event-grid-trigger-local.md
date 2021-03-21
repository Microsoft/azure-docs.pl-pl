---
title: Azure Functions Event Grid debugowania lokalnego
description: Dowiedz się, jak Azure Functions debugowania lokalnego wyzwalane przez zdarzenie Event Grid
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94833065"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure

W tym artykule pokazano, jak debugować funkcję lokalną, która obsługuje zdarzenie Azure Event Grid zgłoszone przez konto magazynu. 

## <a name="prerequisites"></a>Wymagania wstępne

- Tworzenie lub używanie istniejącej aplikacji funkcji
- Tworzenie istniejącego konta magazynu lub korzystanie z niego
- Pobierz [ngrok](https://ngrok.com/) , aby umożliwić platformie Azure wywołanie funkcji lokalnej

## <a name="create-a-new-function"></a>Tworzenie nowej funkcji

Otwórz aplikację funkcji w programie Visual Studio i kliknij prawym przyciskiem myszy nazwę projektu w Eksplorator rozwiązań a następnie kliknij pozycję **dodaj > nową funkcję platformy Azure**.

W oknie *Nowa funkcja platformy Azure* wybierz **wyzwalacz Event Grid** i kliknij przycisk **OK**.

![Tworzenie nowej funkcji](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Po utworzeniu funkcji Otwórz plik kodu i skopiuj adres URL oznaczony jako komentarz w górnej części pliku. Ta lokalizacja jest używana podczas konfigurowania wyzwalacza Event Grid.

![Kopiuj lokalizację](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Następnie ustaw punkt przerwania w wierszu zaczynającym się od `log.LogInformation` .

![Ustaw punkt przerwania](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Następnie **naciśnij klawisz F5** , aby rozpocząć sesję debugowania.

## <a name="allow-azure-to-call-your-local-function"></a>Zezwalaj platformie Azure na wywoływanie funkcji lokalnej

Aby podzielić na maszynę debugowaną funkcję, musisz umożliwić platformie Azure komunikowanie się z lokalną funkcją z chmury.

Narzędzie [ngrok](https://ngrok.com/) umożliwia platformie Azure wywoływanie funkcji działającej na maszynie. Uruchom *ngrok* przy użyciu następującego polecenia:

```bash
ngrok http -host-header=localhost 7071
```
Po skonfigurowaniu narzędzia okno polecenia powinno wyglądać podobnie do poniższego zrzutu ekranu:

![Zrzut ekranu pokazujący wiersz polecenia po uruchomieniu narzędzia "ngrok".](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Skopiuj adres URL **protokołu HTTPS** wygenerowany, gdy *ngrok* jest uruchomiony. Ta wartość jest używana podczas konfigurowania punktu końcowego zdarzenia w usłudze Event Grid.

## <a name="add-a-storage-event"></a>Dodawanie zdarzenia magazynu

Otwórz Azure Portal i przejdź do konta magazynu i kliknij opcję **zdarzenia** .

![Dodawanie zdarzenia konta magazynu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

W oknie *zdarzenia* kliknij przycisk **subskrypcja zdarzeń** . W oknie *subskrypcji zdarzeń* kliknij listę rozwijaną *Typ punktu końcowego* i wybierz **element webhook**.

![Wybierz typ subskrypcji](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Po skonfigurowaniu typu punktu końcowego kliknij pozycję **Wybierz punkt końcowy** , aby skonfigurować wartość punktu końcowego.

![Wybierz typ punktu końcowego](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Wartość *punktu końcowego subskrybenta* składa się z trzech różnych wartości. Prefiks jest adresem URL HTTPS wygenerowanym przez *ngrok*. Pozostała część adresu URL pochodzi z adresu URL znajdującego się w pliku kodu funkcji z dodaną nazwą funkcji na końcu. Począwszy od adresu URL z pliku kodu funkcji, adres URL *ngrok* zastępuje `http://localhost:7071` i zastępuje nazwę funkcji `{functionname}` .

Poniższy zrzut ekranu pokazuje, jak powinien wyglądać końcowy adres URL:

![Wybór punktu końcowego](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Po wprowadzeniu odpowiedniej wartości kliknij przycisk **Potwierdź wybór**.

> [!IMPORTANT]
> Za każdym razem, gdy zaczniesz *ngrok*, adres URL https jest generowany ponownie i zmieniana jest wartość. W związku z tym należy utworzyć nową subskrypcję zdarzeń przy każdym udostępnieniu funkcji na platformie Azure za pośrednictwem *ngrok*.

## <a name="upload-a-file"></a>Przekazywanie pliku

Teraz możesz przekazać plik na konto magazynu, aby wyzwolić Event Grid zdarzenie do obsługi funkcji lokalnej. 

Otwórz [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) i nawiąż połączenie z kontem magazynu. 

- Rozwijanie **kontenerów obiektów BLOB** 
- Kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz kontener obiektów BLOB**.
- Nazwij **test** kontenera
- Wybierz kontener *testu*
- Kliknij przycisk **Przekaż**
- Kliknij pozycję **Przekaż pliki**
- Wybierz plik i przekaż go do kontenera obiektów BLOB

## <a name="debug-the-function"></a>Debuguj funkcję

Gdy Event Grid rozpoznaje nowy plik zostanie przekazany do kontenera magazynu, punkt przerwania zostanie osiągnięty w funkcji lokalnej.

![Uruchom ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym artykule, Usuń kontener **testowy** na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

- [Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Wyzwalacz Event Grid dla Azure Functions](./functions-bindings-event-grid.md)
