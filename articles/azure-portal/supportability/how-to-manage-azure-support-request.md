---
title: Zarządzanie wnioskiem o pomoc techniczną platformy Azure
description: Opisuje sposób wyświetlania żądań pomocy technicznej, wysyłania komunikatów, zmiany poziomu ważności żądania, udostępniania informacji diagnostycznych za pomocą pomocy technicznej platformy Azure, ponownego otwarcia zamkniętego żądania obsługi oraz przekazywania plików.
author: mgblythe
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.service: azure-supportability
ms.topic: how-to
ms.date: 06/30/2020
ms.author: mblythe
ms.openlocfilehash: f3b4806bf46750d74a54f68bd2ab58e402e75091
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85852385"
---
# <a name="manage-an-azure-support-request"></a>Zarządzanie wnioskiem o pomoc techniczną platformy Azure

Po [utworzeniu żądania pomocy technicznej platformy Azure](how-to-create-azure-support-request.md)można nim zarządzać w [Azure Portal](https://portal.azure.com), który jest objęty tym artykułem. Żądania można także tworzyć programowo i zarządzać nimi za pomocą [interfejsu API REST biletów pomocy technicznej platformy Azure](/rest/api/support).

## <a name="view-support-requests"></a>Wyświetlanie wszystkich żądań pomocy technicznej

Wyświetl szczegóły i stan żądań pomocy technicznej, przechodząc do **pomocy technicznej i obsługują**  >   **wszystkie żądania**pomocy technicznej.

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

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Wszystkie żądania pomocy technicznej":::

1. Azure Portal przedstawia jeden z dwóch ekranów, w zależności od tego, czy żądanie jest już przypisane do inżyniera pomocy technicznej:

    - Jeśli Twoje żądanie nie zostało przypisane, zobaczysz ekran podobny do poniższego. Wybierz nowy poziom ważności, a następnie wybierz pozycję **Zmień**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Wszystkie żądania pomocy technicznej":::

    - Jeśli Twoje żądanie zostało przypisane, zobaczysz ekran podobny do poniższego. Wybierz przycisk **OK**, a następnie Utwórz [Nowy komunikat](#send-a-message) , aby zażądać zmiany na poziomie ważności.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Wszystkie żądania pomocy technicznej":::

## <a name="share-diagnostic-information-with-azure-support"></a>Udostępnianie informacji diagnostycznych za pomocą pomocy technicznej platformy Azure

Gdy tworzysz żądanie obsługi, domyślnie jest zaznaczona opcja **Udostępnij informacje diagnostyczne** . Dzięki temu platforma Azure może zbierać [informacje diagnostyczne](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) z zasobów platformy Azure:

* Nie można wyczyścić tej opcji po utworzeniu żądania.

* Jeśli wyczyścisz opcję podczas tworzenia żądania, możesz wybrać ją po utworzeniu żądania.

    1. Na stronie **wszystkie żądania pomocy technicznej** wybierz żądanie pomocy technicznej.
    
    1. Na stronie **żądanie pomocy technicznej** wybierz pozycję **Udziel uprawnienia**, a następnie wybierz pozycję **tak** i kliknij przycisk **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Wszystkie żądania pomocy technicznej":::

## <a name="upload-files"></a>Przekazywanie plików

Możesz użyć opcji przekazywania plików, aby przekazać pliki diagnostyczne lub inne pliki, które Twoim zdaniem są odpowiednie dla żądania pomocy technicznej.

1. Na stronie **wszystkie żądania pomocy technicznej** wybierz żądanie pomocy technicznej.

1. Na stronie **żądanie pomocy technicznej** Wyszukaj plik, a następnie wybierz pozycję **Przekaż**. Powtórz ten proces, jeśli masz wiele plików.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Wszystkie żądania pomocy technicznej":::

### <a name="file-upload-guidelines"></a>Wskazówki dotyczące przekazywania plików

W przypadku korzystania z opcji przekazywania plików postępuj zgodnie z poniższymi wskazówkami:

* Aby chronić prywatność, nie dołączaj żadnych informacji osobistych do przekazywania.
* Nazwa pliku nie może być dłuższa niż 110 znaków.
* Nie można przekazać więcej niż jednego pliku.
* Pliki nie mogą być większe niż 4 MB.
* Wszystkie pliki muszą mieć rozszerzenie nazwy pliku, na przykład *docx* lub *xlsx*. W poniższej tabeli przedstawiono rozszerzenia nazw plików, które są dozwolone do przekazywania.

| 0-9, A-C    | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | .hwl        | . ODX  | . rar     | .tdb       | xlam   |
| . a          | . DB   | . ICS        | . oft  | . rdl     | .tdf       | .xlr    |
| . ABC        | . DMP  | . ini        | . old  | . rdlc    | . Text      | xls    |
| . adm        | .do_  | .java       | . one  | .re_     | thmx      | xlsb   |
| . aspx       | doc  | jpg        | . OSD  | . reg     | tif       | xlsm   |
| . ATF        | docm | . LDF        | . OKREŚLONĄ  | . Usuń  | . trc       | xlsx   |
| . b          | docx | . nagłówek list | . P1   | . Ren     | . DOCELOWY       | xlt    |
| .ba_        | dotm | . lnk        | .pcap | . Zmień nazwę  | .tx_       | xltx   |
| . bak        | dotx | .lo_        | . pdb  | .rft     | txt       | xml    |
| bat        | .dtsx | . log        | pdf  | . rpt     | .uccapilog | . XMLA   |
| . blg        | . EDS  | . lpk        | .piz  | .rte     | .uccplog   | xps    |
| .CA_        | . EMF  | . manifest   | .pmls | .rtf     | .udcx      | . xsd    |
| . CAB        | . eml  | . Master     | png  | . Uruchom     | .vb_       | . xsn    |
| . Cap        | .emz  | . MDMP       | potx | .saz     | .vbs_      | . xxx    |
| .catx       | . błąd  | plik MOF        | ppt  | .sql     | . vcf       | .z_     |
| . CFG        | . etl  | mp3        | pptm | . sqlplan | . vsd       | .z01    |
| . skompresowane | . evt  | mpg        | pptx | . stp     | .wdb       | .z02    |
| . Sygnatur     | . evtx | .ms_        | . prn  | .svclog  | . WKS       | . zi     |
| .cpk        | . UPRZEDNI   | . msg        | . WPO  |   -       | wma       | .zi_    |
| . cpp        | .ex_  | .msi        | . pst  |  -        | wmv       | zip    |
| .cs         | .ex0  | . mso        | . pub  | -         | . WMZ       | .zip_   |
| . CSV        | . FRD  | . msu        | -      |-          | . WPS       | .zipp   |
| .cvr        | gif  | . nfo        | -      |-          | .wpt       | . spakowane |
| -            | . GUID | -            | -      | -         | . WSDL      | .zippy  |
| -            | . gz   | -            | -      | -         | . wsp       | .zipx   |
| -            | -      | -            | -      | -         | .wtl       | .zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | . zzz    |

## <a name="reopen-a-closed-request"></a>Ponowne otwieranie zamkniętego żądania

Jeśli musisz ponownie otworzyć zamknięte żądanie obsługi, Utwórz [Nowy komunikat](#send-a-message), który automatycznie ponownie otworzy żądanie.

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć żądanie pomocy technicznej dla platformy Azure](how-to-create-azure-support-request.md)

[Interfejs API REST biletu pomocy technicznej platformy Azure](/rest/api/support)
