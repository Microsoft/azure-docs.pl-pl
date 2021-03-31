---
title: Zarządzanie wnioskiem o pomoc techniczną platformy Azure
description: Opisuje sposób wyświetlania żądań pomocy technicznej, wysyłania komunikatów, zmiany poziomu ważności żądania, udostępniania informacji diagnostycznych za pomocą pomocy technicznej platformy Azure, ponownego otwarcia zamkniętego żądania obsługi oraz przekazywania plików.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502522"
---
# <a name="manage-an-azure-support-request"></a>Zarządzanie wnioskiem o pomoc techniczną platformy Azure

Po [utworzeniu żądania pomocy technicznej platformy Azure](how-to-create-azure-support-request.md)można nim zarządzać w [Azure Portal](https://portal.azure.com), który jest objęty tym artykułem. Żądania można także tworzyć programowo i zarządzać nimi przy użyciu [interfejsu API REST biletów pomocy technicznej platformy Azure](/rest/api/support)lub przy użyciu [wiersza polecenia platformy Azure](/cli/azure/azure-cli-support-request).

## <a name="view-support-requests"></a>Wyświetlanie wszystkich żądań pomocy technicznej

Wyświetl szczegóły i stan żądań pomocy technicznej, przechodząc do **pomocy technicznej i obsługują**  >   **wszystkie żądania** pomocy technicznej.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Wszystkie żądania pomocy technicznej":::

Na tej stronie można wyszukiwać, filtrować i sortować żądania pomocy technicznej. Wybierz żądanie obsługi, aby wyświetlić szczegóły, w tym jego ważność i wszystkie komunikaty skojarzone z żądaniem.

## <a name="send-a-message"></a>Wysyłanie komunikatu

1. Na stronie **wszystkie żądania pomocy technicznej** wybierz żądanie pomocy technicznej.

1. Na stronie **żądanie pomocy technicznej** wybierz pozycję **Nowy komunikat**.

1. Wprowadź wiadomość i wybierz pozycję **Prześlij**.

## <a name="change-the-severity-level"></a>Zmień poziom ważności

> [!NOTE]
> Maksymalny poziom ważności zależy od Twojego [planu pomocy technicznej](https://azure.microsoft.com/support/plans).
>

1. Na stronie **wszystkie żądania pomocy technicznej** wybierz żądanie pomocy technicznej.

1. Na stronie **żądanie pomocy technicznej** wybierz pozycję **Zmień**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Zmiana ważności żądania obsługi":::

1. Azure Portal przedstawia jeden z dwóch ekranów, w zależności od tego, czy żądanie jest już przypisane do inżyniera pomocy technicznej:

    - Jeśli Twoje żądanie nie zostało przypisane, zobaczysz ekran podobny do poniższego. Wybierz nowy poziom ważności, a następnie wybierz pozycję **Zmień**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Wybierz nowy poziom ważności":::

    - Jeśli Twoje żądanie zostało przypisane, zobaczysz ekran podobny do poniższego. Wybierz przycisk **OK**, a następnie Utwórz [Nowy komunikat](#send-a-message) , aby zażądać zmiany na poziomie ważności.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Nie można wybrać nowego poziomu ważności":::

## <a name="share-diagnostic-information-with-azure-support"></a>Udostępnianie informacji diagnostycznych za pomocą pomocy technicznej platformy Azure

Gdy tworzysz żądanie obsługi, domyślnie jest zaznaczona opcja **Udostępnij informacje diagnostyczne** . Dzięki temu platforma Azure może zbierać [informacje diagnostyczne](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) z zasobów platformy Azure:

* Nie można wyczyścić tej opcji po utworzeniu żądania.

* Jeśli wyczyścisz opcję podczas tworzenia żądania, możesz wybrać ją po utworzeniu żądania.

    1. Na stronie **wszystkie żądania pomocy technicznej** wybierz żądanie pomocy technicznej.
    
    1. Na stronie **żądanie pomocy technicznej** wybierz pozycję **Udziel uprawnienia**, a następnie wybierz pozycję **tak** i kliknij przycisk **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Udziel uprawnień do informacji diagnostycznych":::

## <a name="upload-files"></a>Przekazywanie plików

Możesz użyć opcji przekazywania plików, aby przekazać pliki diagnostyczne lub inne pliki, które Twoim zdaniem są odpowiednie dla żądania pomocy technicznej.

1. Na stronie **wszystkie żądania pomocy technicznej** wybierz żądanie pomocy technicznej.

1. Na stronie **żądanie pomocy technicznej** Wyszukaj plik, a następnie wybierz pozycję **Przekaż**. Powtórz ten proces, jeśli masz wiele plików.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Przekaż plik":::

### <a name="file-upload-guidelines"></a>Wskazówki dotyczące przekazywania plików

W przypadku korzystania z opcji przekazywania plików postępuj zgodnie z poniższymi wskazówkami:

* Aby chronić prywatność, nie dołączaj żadnych informacji osobistych do przekazywania.
* Nazwa pliku nie może być dłuższa niż 110 znaków.
* Nie można przekazać więcej niż jednego pliku.
* Pliki nie mogą być większe niż 4 MB.
* Wszystkie pliki muszą mieć rozszerzenie nazwy pliku, na przykład *docx* lub *xlsx*. W poniższej tabeli przedstawiono rozszerzenia nazw plików, które są dozwolone do przekazywania.

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . Har        | . ODX  | . rar     | .uccapilog | xlam   |
| . a          | . DB   | .hwl        | . oft  | . rdl     | .uccplog   | .xlr    |
| . ABC        | . DMP  | . ICS        | . old  | . rdlc    | .udcx      | xls    |
| . adm        | .do_  | . ini        | . one  | .re_     | .vb_       | xlsb   |
| . aspx       | doc  | .java       | . OSD  | . Usuń  | .vbs_      | xlsm   |
| . ATF        | docm | jpg        | . OKREŚLONĄ  | . Ren     | . vcf       | xlsx   |
| . b          | docx | . LDF        | . P1   | . Zmień nazwę  | . vsd       | xlt    |
| .ba_        | dotm | . nagłówek list | .pcap | .rft     | .wdb       | xltx   |
| . bak        | dotx | .lo_        | . pdb  | . rpt     | . WKS       | xml    |
| . blg        | .dtsx | . log        | pdf  | .rte     | wma       | . XMLA   |
| .CA_        | . EDS  | . lpk        | .piz  | .rtf     | wmv       | xps    |
| . CAB        | . EMF  | . manifest   | .pmls | . Uruchom     | . WMZ       | . xsd    |
| . Cap        | . eml  | . Master     | png  | .saz     | . WPS       | . xsn    |
| .catx       | .emz  | . MDMP       | potx | .sql     | .wpt       | . xxx    |
| . CFG        | . błąd  | plik MOF        | ppt  | . sqlplan | . WSDL      | .z_     |
| . skompresowane | . etl  | mp3        | pptm | . stp     | . wsp       | .z01    |
| . Sygnatur     | . evt  | mpg        | pptx | .svclog  | .wtl       | .z02    |
| .cpk        | . evtx | .ms_        | . prn  | .tdb     | -          | . zi     |
| . cpp        | . UPRZEDNI   | . msg        | . WPO  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | . mso        | . pst  | . Text    | -          | zip    |
| . CSV        | .ex0  | . msu        | . pub  | thmx    | -          | .zip_   |
| .cvr        | . FRD  | . nfo        | -     | tif     | -          | .zipp   |
| -           | gif  | -           | -     | . trc     | -          | . spakowane |
| -           | . GUID | -           | -     | . DOCELOWY     | -          | .zippy  |
| -           | . gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | . zzz    |

## <a name="close-a-support-request"></a>Zamykanie żądania obsługi

Jeśli musisz zamknąć żądanie pomocy technicznej, [Wyślij komunikat z](#send-a-message) pytaniem, czy żądanie zostało zamknięte.

## <a name="reopen-a-closed-request"></a>Ponowne otwieranie zamkniętego żądania

Jeśli musisz ponownie otworzyć zamknięte żądanie obsługi, Utwórz [Nowy komunikat](#send-a-message), który automatycznie ponownie otworzy żądanie.

## <a name="cancel-a-support-plan"></a>Anulowanie planu pomocy technicznej

Aby anulować plan pomocy technicznej, zobacz sekcję [anulowanie planu pomocy technicznej](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć żądanie pomocy technicznej dla platformy Azure](how-to-create-azure-support-request.md)

[Interfejs API REST biletu pomocy technicznej platformy Azure](/rest/api/support)
