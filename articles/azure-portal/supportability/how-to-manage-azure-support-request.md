---
title: Zarządzanie wnioskiem o pomoc techniczną platformy Azure
description: Opisuje sposób wyświetlania żądań pomocy technicznej, wysyłania komunikatów, zmiany poziomu ważności żądania, udostępniania informacji diagnostycznych za pomocą pomocy technicznej platformy Azure, ponownego otwarcia zamkniętego żądania obsługi oraz przekazywania plików.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 8110f87401da1352309fb55615093d49981c754d
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504818"
---
# <a name="manage-an-azure-support-request"></a>Zarządzanie wnioskiem o pomoc techniczną platformy Azure

Po [utworzeniu żądania pomocy technicznej platformy Azure](how-to-create-azure-support-request.md)można nim zarządzać w [Azure Portal](https://portal.azure.com), który jest objęty tym artykułem. Żądania można także tworzyć programowo i zarządzać nimi za pomocą [interfejsu API REST biletów pomocy technicznej platformy Azure](/rest/api/support).

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

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Przekazywanie pliku":::

### <a name="file-upload-guidelines"></a>Wskazówki dotyczące przekazywania plików

W przypadku korzystania z opcji przekazywania plików postępuj zgodnie z poniższymi wskazówkami:

* Aby chronić prywatność, nie dołączaj żadnych informacji osobistych do przekazywania.
* Nazwa pliku nie może być dłuższa niż 110 znaków.
* Nie można przekazać więcej niż jednego pliku.
* Pliki nie mogą być większe niż 4 MB.
* Wszystkie pliki muszą mieć rozszerzenie nazwy pliku, na przykład *docx* lub *xlsx*. W poniższej tabeli przedstawiono rozszerzenia nazw plików, które są dozwolone do przekazywania.

| 0-9, A-C     | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . Har        | . ODX  | . rar     | .tdb       | xlam   |
| . a          | . DB   | .hwl        | . oft  | . rdl     | .tdf       | .xlr    |
| . ABC        | . DMP  | . ICS        | . old  | . rdlc    | . Text      | xls    |
| . adm        | .do_  | . ini        | . one  | .re_     | thmx      | xlsb   |
| . aspx       | doc  | .java       | . OSD  | . reg     | tif       | xlsm   |
| . ATF        | docm | jpg        | . OKREŚLONĄ  | . Usuń  | . trc       | xlsx   |
| . b          | docx | . LDF        | . P1   | . Ren     | . DOCELOWY       | xlt    |
| .ba_        | dotm | . nagłówek list | .pcap | . Zmień nazwę  | .tx_       | xltx   |
| . bak        | dotx | . lnk        | . pdb  | .rft     | txt       | xml    |
| bat        | .dtsx | .lo_        | pdf  | . rpt     | .uccapilog | . XMLA   |
| . blg        | . EDS  | . log        | .piz  | .rte     | .uccplog   | xps    |
| .CA_        | . EMF  | . lpk        | .pmls | .rtf     | .udcx      | . xsd    |
| . CAB        | . eml  | . manifest   | png  | . Uruchom     | .vb_       | . xsn    |
| . Cap        | .emz  | . Master     | potx | .saz     | .vbs_      | . xxx    |
| .catx       | . błąd  | . MDMP       | ppt  | .sql     | . vcf       | .z_     |
| . CFG        | . etl  | plik MOF        | pptm | . sqlplan | . vsd       | .z01    |
| . skompresowane | . evt  | mp3        | pptx | . stp     | .wdb       | .z02    |
| . Sygnatur     | . evtx | mpg        | . prn  | .svclog  | . WKS       | . zi     |
| .cpk        | . UPRZEDNI   | .ms_        | . WPO  | -        | wma       | .zi_    |
| . cpp        | .ex_  | . msg        | . pst  | -        | wmv       | zip    |
| .cs         | .ex0  | .msi        | . pub  | -        | . WMZ       | .zip_   |
| . CSV        | . FRD  | . mso        | -     | -        | . WPS       | .zipp   |
| .cvr        | gif  | . msu        | -     | -        | .wpt       | . spakowane |
| -           | . GUID | . nfo        | -     | -        | . WSDL      | .zippy  |
| -           | . gz   | -           | -     | -        | . wsp       | .zipx   |
| -           | -     | -           | -     | -        | .wtl       | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | . zzz    |

## <a name="close-a-support-request"></a>Zamykanie żądania obsługi

Jeśli musisz zamknąć żądanie pomocy technicznej, [Wyślij komunikat z](#send-a-message) pytaniem, czy żądanie zostało zamknięte.

## <a name="reopen-a-closed-request"></a>Ponowne otwieranie zamkniętego żądania

Jeśli musisz ponownie otworzyć zamknięte żądanie obsługi, Utwórz [Nowy komunikat](#send-a-message), który automatycznie ponownie otworzy żądanie.

## <a name="cancel-a-support-plan"></a>Anuluj plan pomocy technicznej

Aby anulować plan pomocy technicznej, zobacz sekcję [anulowanie planu pomocy technicznej](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć żądanie pomocy technicznej dla platformy Azure](how-to-create-azure-support-request.md)

[Interfejs API REST biletu pomocy technicznej platformy Azure](/rest/api/support)
